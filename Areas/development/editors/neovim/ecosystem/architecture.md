---
aliases: ["Neovim architecture", "How Nvim works", "dev-arch"]
tags: [neovim, ecosystem, architecture, reference]
---

# Neovim architecture

The conceptual model of how Nvim is built — the state machine, the event loop, the API surface, the RPC boundary. This is the "how does it actually work" note, distinct from [[core]] (where files live) and [[learning_path]] (what order to read them in).

Distilled from `:help dev-arch` (508 lines in `runtime/doc/dev_arch.txt`) with additional context from `:help event-loop`, `:help ui-protocol`, and reading the code itself.

## The layered picture

```
                    ┌────────────────────────────────────────────┐
                    │  External world                            │
                    │  ─ TUI (terminal + termkey)                │
                    │  ─ GUIs (Neovide, vscode-neovim, …)        │
                    │  ─ Scripts (pynvim, node-client, …)        │
                    └───────────────────┬────────────────────────┘
                                        │  msgpack-RPC
                                        │  (stdio, socket, pipe)
                    ┌───────────────────┴────────────────────────┐
                    │  API surface                               │
                    │  src/nvim/api/*.c  — every nvim_* function │
                    │  src/nvim/api/ui.c — the redraw stream out │
                    └───────────────────┬────────────────────────┘
                                        │  in-process calls
                                        │  (Lua & VimL & UIs)
                    ┌───────────────────┴────────────────────────┐
                    │  Editor core                               │
                    │  ─ State machine  (state.c, normal.c, …)   │
                    │  ─ Event loop     (event/, channel.c)      │
                    │  ─ Buffer + text  (memline.c, buffer.c)    │
                    │  ─ VimL interp    (viml/, eval/)           │
                    │  ─ Lua runtime    (lua/, LuaJIT)           │
                    │  ─ Screen draw    (drawscreen.c, screen.c) │
                    └───────────────────┬────────────────────────┘
                                        │
                    ┌───────────────────┴────────────────────────┐
                    │  Runtime                                   │
                    │  ─ Lua stdlib (runtime/lua/vim/)           │
                    │  ─ Filetype, syntax, ftplugin, docs, …     │
                    └────────────────────────────────────────────┘
```

Every arrow is worth understanding. The rest of this note walks through each layer, then explains the three cross-cutting mechanisms that tie them together: the **state machine**, the **event loop**, and the **API + RPC**.

Companion to [[core]] (physical layout), [[runtime]] (Lua stdlib deep-dive), [[client_libraries]] (the top of the diagram), [[design_goals]] (why it's shaped this way).

## The three cross-cutting mechanisms

### 1. The state machine — input → mode → action

The one insight that unlocks the whole editor: **Nvim is a pushdown automaton driven by input**. Each mode is a state with a callback; typing a key either handles the input in the current state, pushes to a new state, or pops back.

Concretely (from `:help dev-arch § NVIM LIFECYCLE`), a session like `:edit README.txt<CR>G5dwggiword<ESC>` decomposes into:

| Input | Effect |
|---|---|
| `:` | normal → **command-line mode** |
| `edit README.txt<CR>` | run ex command, pop back to normal |
| `G` | jump to EOF (normal state, no push) |
| `5` | normal → **count-pending mode** |
| `d` | count → **operator-pending mode** (carries count=5) |
| `w` | delete 5 words (operator state resolves) |
| `g` | normal → **`g`-command mode** |
| `g` | resolves to `gg`, jump to top |
| `i` | normal → **insert mode** |
| `word<ESC>` | insert text, pop back to normal |

The pattern in pseudocode:

```python
def state_enter(state_callback, data):
    while True:
        key = readkey()
        if not state_callback(data, key):
            break                      # pop back to caller
```

Each mode is a `state_callback`. When one returns false, the caller resumes. Nested states pushdown; unwinding pops.

**Where the code lives (canonical reading order):**

1. **`state_enter()`** in `src/nvim/state.c` — the actual program loop. Takes a `VimState` struct with function pointers for the callback and data.
2. **`main()`** in `src/nvim/main.c` — after startup, calls `normal_enter()`.
3. **`normal_enter()`** in `src/nvim/normal.c` — wraps `state_enter()` with a `NormalState`.
4. **`normal_check()`** in `normal.c` — called before each iteration of normal mode (idle work, autocmds).
5. **`normal_execute()`** in `normal.c` — called when a key is read in normal mode.

The `{enter, check, execute}` triple exists for every mode:

| Mode | File | Functions |
|---|---|---|
| Normal | `src/nvim/normal.c` | `normal_{enter,check,execute}` |
| Command-line | `src/nvim/ex_getln.c` | `command_line_{enter,check,execute}` |
| Insert | `src/nvim/insert.c` | `insert_{enter,check,execute}` |
| Terminal | `src/nvim/terminal.c` | `terminal_{enter,execute}` |

Some sub-states (operator-pending, `g`-command, count-pending) don't have their own `state_enter` callback — they're implicitly embedded in a parent state. `:help dev-arch` notes this is being refactored, gradually.

**Key globals to know:**

- `State` — the current mode (`MODE_NORMAL`, `MODE_INSERT`, `MODE_CMDLINE`, …)
- `curwin` — the current window (points to a struct with cursor position, options, size)
- `curbuf` — the current buffer (points to a struct with file name, lines, marks)
- Everything global is declared in `src/nvim/globals.h`

**Screen updates are postponed.** After a command sequence completes, `update_screen()` runs, calling `win_update()` per window, calling `win_line()` per line. This lives in `drawscreen.c` — read the file header for the details.

### 2. The event loop — how "waiting for input" also does everything else

Nvim's twist on the traditional editor loop: **you're not waiting for a key, you're waiting for an event**. The event might be a key, or it might be a timer, or an RPC request, or a job callback.

The naive loop (from `dev-arch`):

```python
def state_enter(on_state, data):
    while on_state(data, key := readkey()):
        pass
```

The Nvim loop:

```python
def state_enter(on_state, data):
    while on_state(data, event := read_next_event()):
        pass
```

`read_next_event()` is delegated to **libuv**. Under the hood, `Loop.uv` is a `uv_loop_t`; Nvim dispatches events off it.

Because Nvim inherited its code from Vim, most states were written expecting keys, not arbitrary events. So arbitrary events (timers, RPC, job callbacks) are delivered as a special key. State callbacks that don't care update the screen and return.

**The `main_loop` structure:**

```c
uv_loop_t    uv;             // the libuv loop
MultiQueue  *events;         // deferred: safe to process anytime
MultiQueue  *thread_events;  // posted from worker threads
MultiQueue  *fast_events;    // ok to process during os_breakcheck
```

`loop_poll_events` checks `uv` and `fast_events` whenever Nvim is idle, and also during `os_breakcheck` intervals. This is the mechanism that keeps the UI responsive during long-running work.

**MultiQueue is the interesting abstraction.** You can attach child queues that route into the parent. `do_os_system()` uses this — for every spawned process, a child queue is created so process events land on `main_loop.events` automatically:

```c
Process *proc = &uvproc.process;
MultiQueue *events = multiqueue_new_child(main_loop.events);
proc->events = events;
```

**Where the code lives:**

- `src/nvim/event/loop.{c,h}` — the Loop struct and iteration
- `src/nvim/event/multiqueue.{c,h}` — MultiQueue
- `src/nvim/event/proc.{c,h}` — process spawning
- `src/nvim/event/rstream.{c,h}`, `wstream.{c,h}` — non-blocking I/O
- `src/nvim/channel.c` — RPC channels ride on top of streams

**Practical implication (from `:help event-loop`):** raw `vim.uv.run()` from Lua is often not enough to yield. Use `vim.wait(0)` and check its result.

### 3. The API + RPC surface

The API is the boundary that makes Nvim composable. **Everything the outside world can do to Nvim goes through the API**, and every function is exposed identically to:

- Lua callers (`vim.api.nvim_*`)
- VimL callers (`v:` variables, `has()`, `expand()` — via the eval layer)
- Remote callers over msgpack-RPC (external UIs, [[client_libraries|clients]])

**Where the API surface lives:**

- `src/nvim/api/vim.c` — top-level `nvim_*` functions (miscellany)
- `src/nvim/api/buffer.c` — `nvim_buf_*`
- `src/nvim/api/window.c` — `nvim_win_*`
- `src/nvim/api/tabpage.c` — `nvim_tabpage_*`
- `src/nvim/api/command.c` — `nvim_command`, `nvim_create_user_command`, …
- `src/nvim/api/autocmd.c` — `nvim_create_autocmd`, `nvim_exec_autocmds`, …
- `src/nvim/api/extmark.c` — extmarks + decorations
- `src/nvim/api/ui.c` — the redraw stream sent to attached UIs
- `src/nvim/api/dispatch_deprecated.lua` — old names that alias to new ones (renames are eternal)

**The metadata contract:** `nvim --api-info` emits msgpack describing every API function, its parameters, its return type. Every [[client_libraries|client]] consumes this to auto-generate its bindings. `api_info().ui_events` similarly describes UI events.

### The `api-fast` sub-contract

An API function may be marked `|api-fast|`, meaning it's safe to call from Lua callbacks, autocmds, screen-refresh code, and other execution contexts where you *cannot* yield.

**The invariant:** a "fast" function must not trigger `os_breakcheck()`, because `os_breakcheck()` can yield to start a new execution — recursing into code that doesn't expect it, or letting global state change mid-callsite.

**Common non-fast operations:**

- Regexp matching
- Wildcard expansion
- Expression evaluation
- Anything that walks the runtimepath (`nvim__get_runtime` used to violate this until PR fixed it with `EW_NOBREAK`)

If you're implementing a new API function, ask: can I be called from an autocmd callback? If yes, aim for `api-fast`. If not, document it.

## Editor events vs UI events

Two event mechanisms exist today. **The long-term vision is to unify them.** For now, know the distinction:

| | Editor events (autocmds) | UI events |
|---|---|---|
| Consumer | Plugins, user config, Nvim itself | External UIs (TUI, GUIs, embeddings) |
| Trigger | `BufEnter`, `TextChanged`, `LspAttach`, … | `grid_line`, `mode_change`, `hl_attr_define`, … |
| Defined in | `src/nvim/auevents.lua` | `src/nvim/api/ui_events.in.h` (parsed at build time) |
| Transport | In-process callback dispatch | msgpack-RPC `redraw` notification |
| Fired by | `aucmd_defer()` (preferred) or `vim.api.nvim_exec_autocmds()` | `src/nvim/ui.c` handlers, forwarded by `src/nvim/api/ui.c` |
| Also observable in-process via | Normal autocmd registration | `vim.ui_attach()` |

**Both are just "events" conceptually.** The current split exists because UI events were designed for RPC delivery to external processes; autocmds predate that. The eventual unification (`:help dev-events`) would make both just "events" with configurable transports.

**Adding a new UI event** (from `:help dev-arch`):

1. Add it to `src/nvim/api/ui_events.in.h`. Generator scripts pick it up.
2. `src/gen/gen_api_ui_events.lua` produces the wrapper functions.
3. Bump `NVIM_API_LEVEL` if it wasn't already bumped this cycle.
4. Example commit: `d3a8e9217f39c59dd7762bd22a76b8bd03ca85ff` in the neovim repo.

**Adding a new editor event (autocmd):**

1. Register the name in `src/nvim/auevents.lua`. If Nvim-only, also update the `nvim_specific` table.
2. Fire it — either from C with `aucmd_defer()` (safe and predictable — see `do_markset_autocmd` for an example) or from Lua with `vim.api.nvim_exec_autocmds()`.
3. Define the `event-data` type (if any) in `runtime/lua/vim/_meta/events.lua`.
4. Document in `runtime/doc/autocmd.txt`.

## The Lua-first migration

New functionality should be implemented in Lua, not C. This is explicit contributor guidance. The core is being frozen in scope; growth happens in Lua.

**Three ways to implement a new Ex command or builtin function**, in order of preference (from `:help dev-arch § dev-new-excmd`):

1. **Full Lua** — the whole implementation is Lua. Bonus: when called from Lua (`vim.fn.xx()`), the fast path in `nlua_call()` skips the Vimscript ↔ Lua bridge entirely. Example: `f_hostname`.
2. **Partial Lua** — C shell that calls into Lua via `nlua_call_typval`, `nlua_call_excmd`, or (rarely) `nlua_exec` / `NLUA_EXEC_STATIC`. Examples: `ex_log`, `ex_lsp`, `f_serverlist`.
3. **Legacy full-C** — the whole thing in C. Examples: `f_chansend`, `f_flatten`, `f_searchpos`. This is where the older functions live; new work should not add to this pile.

**The Lua ↔ VimL bridge:** when VimL calls a Lua function, the `lua_wrapper` converts between VimL's `typval` and Lua's `Object`. When Lua calls a Lua-implemented function, the wrapper is skipped — a real performance win at hot callsites.

## The `runtime/lua/vim/` split

Four kinds of Lua code (`:help dev-arch`):

1. **Plugins** — `runtime/plugin/` (opt-out, auto-loaded) and `runtime/pack/dist/opt/` (opt-in via `:packadd`). Correct home for "autoload behavior," "user may disable," "primarily user-facing."
2. **Stdlib public** — `runtime/lua/vim/*.lua`. Compiled into `nvim` (via `VIM_MODULE_FILE` in `src/nvim/CMakeLists.txt`) so it works even when `VIMRUNTIME` is unset. Examples: `vim.iter`, `vim.fs`, `vim.lsp`.
3. **Stdlib private** — `runtime/lua/vim/_core/`. Same "compiled in" property but not for user consumption; `_`-prefix means "will change without notice."
4. **Internal Lua under `lua/nvim/`** — implementation of default plugins like `autoread.lua`, `matchparen.lua`, `spellfile.lua`. Not for `require`ing from user code.

**Compatibility with Vim's `if_lua` is explicitly a non-goal.** Nvim's Lua interface is its own thing.

## Data structures the whole codebase uses

Recurring shapes to internalize (also covered in [[learning_path#C data structures you'll see everywhere]]):

- **`kvec.h`** — the resizable vector macro. First reach for lists.
- **`garray_T`** — older generic array. Still around; kvec preferred for new code.
- **StringBuilder** (`src/nvim/strings.h`) — for growing strings.
- **`queue_defs.h`** — intrusive linked list. Only when you need list semantics.
- **`memline.c`** — buffer text as a tree of line segments; `ml_find_line` is the entry point.

Many editor concepts are defined in **Lua data files**, parsed at build time to generate C tables and `:help` pages:

- `src/nvim/auevents.lua` — every autocmd event
- `src/nvim/ex_cmds.lua` — every Ex command
- `src/nvim/options.lua` — every option
- `src/nvim/eval.lua` — every builtin Vimscript function
- `src/nvim/vvars.lua` — every `v:` variable

## Filesystem convention

One rule from `:help dev-arch § dev-filesystem` worth memorizing:

> Path separators are "/" internally, everywhere. Conversion to "\" (or whatever the platform wants) happens only at the *edges* — when we actually talk to the OS. Similar to how UTF-8 is used internally for all text, but buffers can read/write various encodings.

**Windows handles "/" separators just fine.** The docs are emphatic. Just use "/".

## Reading the code in this order

If you want to see the state machine + event loop + API surface all working together, the recommended sequence (from `:help dev-arch`):

1. **`state_enter()`** in `src/nvim/state.c` — the pushdown automaton
2. **`main()`** in `src/nvim/main.c` — startup, then `normal_enter()`
3. **`normal_enter()` + `normal_check()` + `normal_execute()`** in `src/nvim/normal.c` — one mode
4. **`vgetc()`** — where Vim waits for a character (also handles key mapping)
5. **`update_screen()`** in `drawscreen.c` — the screen refresh entry point
6. **`getcmdline()`** in `ex_getln.c` — command-line mode
7. **`do_cmdline()` → `do_one_cmd()`** in `ex_docmd.c` — Ex command dispatch
8. **`nv_cmds` table** in `normal.c` — the first-character → function map for normal mode
9. **`edit()`** in `insert.c` — insert mode

That's the full arc from input → dispatch → work → screen. Everything else is variations on those themes.

## Threading model (minimal)

Nvim is largely **single-threaded**. libuv provides async I/O without threads for most operations. Where threads exist:

- **`thread_events`** queue in `main_loop` — worker threads post events here; they're drained on the main thread.
- **The msgpack-RPC channel worker** — parses incoming messages off the network I/O thread, deferring the handler execution to the main loop.

The "everything happens on the main thread eventually" invariant is what makes API functions safe to call from any context — you don't need to think about locking, only about `api-fast` (yielding vs not).

## Startup flow (compressed)

1. `main()` in `main.c` parses argv, sets up globals, initializes subsystems (libuv, LuaJIT, terminfo, providers).
2. Runtime files load in `runtimepath` order (see [[runtime#What "runtime" actually means]]) — `plugin/*` files, filetype detection.
3. User config loads (`init.lua` or `init.vim`).
4. Attached UI(s), if any, get their initial `redraw` events.
5. `normal_enter()` starts the state machine.

## Where each abstraction is documented

| Concept | Authoritative reference |
|---|---|
| Overall design | `:help design-goals` (see [[design_goals]]) |
| Architecture | `:help dev-arch` (this note distills it) |
| Event loop | `:help event-loop` + `src/nvim/event/loop.h` |
| RPC | `:help msgpack-rpc` |
| API | `:help api` |
| UI protocol | `:help ui` + `:help ui-events` |
| State machine | `:help dev-arch § NVIM LIFECYCLE` |
| Fast-context contract | `:help api-fast` |
| Adding features | `:help dev-new-excmd`, `:help dev-new-event` |

## Related

- [[core]] — the physical layout that houses this architecture
- [[runtime]] — the Lua stdlib layer at the bottom of the diagram
- [[client_libraries]] — the top of the diagram: what talks RPC to the API
- [[editor_integrations]] — how UIs attach and consume the `redraw` stream
- [[design_goals]] — why the architecture is shaped this way
- [[getting_involved]] — how to contribute changes that respect this architecture
- [[learning_path]] — a sequenced reading order for internalizing all of this
- `:help dev-arch` — authoritative source (`runtime/doc/dev_arch.txt`)
