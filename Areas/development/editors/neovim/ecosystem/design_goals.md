---
aliases: ["Neovim design goals", "Nvim philosophy", "design-goals"]
tags: [neovim, ecosystem, philosophy, reference]
---

# Neovim design goals

The five design goals that Nvim's contributors reach for when they disagree about a change. Sourced from `runtime/doc/dev.txt` under `*design-goals*` — the authoritative statement lives there; this note is a distillation with concrete examples of how each goal plays out in the codebase.

**The framing that matters:** *"Most important things come first (roughly). Some items conflict; this is intentional. A balance must be found."* — this is not a checklist. Two goals will regularly point in opposite directions and the resolution is judgment, not lookup.

Companion to [[core]] (the tree these goals shape), [[getting_involved]] (where these goals become PR review criteria), and [[learning_path#Stage 1 — Philosophy and constraints ~1 evening]] (where a learner first encounters them).

## The five goals, in canonical order

### 1. Improved (`*design-improved*`)

> The Neo bits of Nvim should make it a better Vim, without becoming a completely different editor.

**Rules:**

- In matters of taste, prefer Vim/Unix tradition. If there is no relevant tradition, consider the "common case."
- There is no hard limit on features. New features get in based on: (1) what users ask for, (2) how much effort it takes to implement, (3) someone actually implementing it.
- **Backwards compatibility is a feature.** The RPC API in particular should never break.

**How it shows up in the code:**

- The `nvim_*` API is versioned via `NVIM_API_LEVEL`. Renames go through `src/nvim/api/dispatch_deprecated.lua` — old names live forever.
- Vim script (`viml/`) is preserved, not deprecated, even though Lua is now preferred for new code.
- Filetype detection (`runtime/lua/vim/filetype.lua`) *matches Vim*; behavior changes go to Vim first (see [[getting_involved#Vim patches — for Vim experts]]).
- Modal editing, keybindings, buffer/window/tab semantics — all inherited faithfully.

**Where the tension bites:** "improve without becoming different" fights "no limit on features." Nvim's answer is a strong bias toward *lower-level* mechanisms (RPC API, Lua stdlib, tree-sitter, LSP) over *higher-level* opinions (particular themes, keymaps, plugin conventions). The core stays Vim-shaped; the plugin ecosystem provides the modernizations.

### 2. Well Documented (`*design-documented*`)

> A feature that isn't documented is a useless feature. A patch for a new feature must include the documentation.

**Rules:**

- Documentation must be comprehensive and understandable — use examples.
- Don't make text unnecessarily long. Less docs → easier to find what you need.

**How it shows up in the code:**

- `runtime/doc/*.txt` — [[runtime#Documentation `doc/`|136 :help pages]], many auto-generated from docstrings.
- Auto-generation: `make doc` regenerates docs from LuaCATS annotations (`runtime/lua/vim/`) and C doc comments (`src/nvim/api/`). See `src/gen/gen_vimdoc.lua`, `luacats_parser.lua`, `cdoc_parser.lua`.
- `make lintdoc` validates them.
- PR reviews check that `runtime/doc/news.txt` is updated for user-visible changes.
- `:help dev-doc` and `:help dev-lua-doc` are prescriptive about style.
- **A PR that adds behavior but not docs will bounce.** Not optional.

**Where the tension bites:** "comprehensive" vs "don't make it long." Nvim's answer: split into narrow topic files, use `:help` tags for cross-referencing, generate boilerplate from source so hand-written prose stays tight.

### 3. Fast and Small (`*design-speed-size*`)

> Keep Nvim small and fast. This directly affects versatility and usability.

**Rules:**

- Vim can grow, but no faster than computers grow. Keep it usable on older systems.
- Startup time must be short (many users launch from a shell often).
- Commands must be efficient. Common commands must be as fast as possible; less-common commands may take longer.
- Minimize communication overhead — some users are on slow connections.
- Compose with other programs; don't be a massive application.

**How it shows up in the code:**

- Startup: `vim.loader` (Lua bytecode cache), lazy-loading of built-in plugins via `vim.g.loaded_*` opt-outs, `--luamod-dev` for fast dev loops.
- Rendering: differential redraw via `drawscreen.c` + `drawline.c` — only what changed goes out.
- Data structures: `kvec.h` (contiguous, cache-friendly) is the default; linked lists only when you specifically need list semantics.
- RPC compactness: msgpack (binary) over JSON; UI events are compact grid deltas rather than full-screen dumps.
- Composability: providers ([[#Providers as the anti-kitchen-sink pattern|see below]]) delegate work to external processes rather than embedding interpreters.
- CI runs `test/benchmark/` to catch perf regressions.

**Where the tension bites:** "fast and small" vs "no limit on features." Nvim's answer: features that don't touch startup or the hot path are cheap; features that do get scrutinized. The Lua stdlib grows freely; the C core stays small.

### 4. Maintainable (`*design-maintain*`)

> The source code should not become a mess. It should be reliable code.

**Rules:**

- Comments should be *useful* — restating the function name is not useful. Explain *why*, not *what*.
- Porting to another platform should be easy — minimize non-portable code.
- Object-oriented spirit: put data and code together; minimize the knowledge spread across modules.

**How it shows up in the code:**

- **Module header comments.** Big C files (`buffer.c`, `terminal.c`, `memline.c`, `channel.c`) explain themselves in ~10–30 line preambles. See [[learning_path#Two "read the header comment" habits]] for two worth reading right now.
- **Frozen legacy modules.** `regexp.c` and `indent_c.c` are maintained by Vim upstream; changing them locally without Vim-first coordination is discouraged. This is a maintainability decision — divergence would double the work forever.
- **`src/uncrustify.cfg`** — a machine-enforced style config. `make format` applies it.
- **CI runs `-Werror`.** Compiler warnings fail the build.
- **`include-what-you-use`** — `make iwyu` auto-fixes `#include` bloat, so headers don't accumulate dead deps.
- **`.git-blame-ignore-revs`** — commits that are pure reformatting are annotated so `git blame` skips them.
- **Portability:** `src/nvim/os/` is the platform-abstraction layer (libuv wrappers, path handling, TTY). Platform-specific code stays there.

**Where the tension bites:** "maintainable" vs "improved" — every legacy Vim behavior is a maintenance tax, but removing it violates backwards-compat. Nvim's answer: keep it, but refactor when the opportunity comes (the `viml/parser/` rewrite is an example), and quarantine what can't be improved (frozen modules).

### 5. Not [an operating system] (`*design-not*`)

> Nvim is not an operating system; instead it should be composed with other tools or hosted as a component. Marvim once said: "Unlike Emacs, Nvim does not include the kitchen sink... but it's good for plumbing."

The shortest goal, and arguably the most consequential.

**How it shows up in the code:**

- **No embedded browser, email client, chat, calendar.** Traditional Emacs "everything in one" territory is out of scope.
- **Clipboard: shell out to `xclip`/`pbcopy`/`wl-copy`.** Vim's clipboard code was ~1k lines of C; Nvim delegates via `runtime/autoload/provider/clipboard.vim`.
- **Language support: providers, not embedding.** Nvim's Python support is a ~2k-line Python host talking msgpack-RPC; Vim's was ~9.5k lines of C linking `libpython` directly.
- **UI: separated over an API.** The [[client_libraries|client libraries]] and [[editor_integrations|external GUIs]] exist because the UI was extracted from the core over a stable protocol.
- **Package management: `vim.pack` is small.** It downloads git repos and adds them to `runtimepath` — it doesn't try to be Cargo or npm. Complex dep resolution is left to [[first_party_plugins|packspec]] (still ALPHA) and community managers.

**Where the tension bites:** users always want *one more* built-in. Nvim's answer: the RPC API and provider framework let anyone add "kitchen sink" features *outside* the core. The core stays plumbing.

## Providers as the anti-kitchen-sink pattern

The provider framework is the mechanism that lets goal 5 (*Not*) coexist with goal 1 (*no limit on features*). Rather than embedding an interpreter, Nvim delegates to an external script:

- **Clipboard** → shell command (`xclip`, `pbcopy`, `wl-copy`, OSC 52)
- **Python** → external `pynvim` host process (see [[client_libraries]])
- **Node**, **Ruby**, **Perl** — same pattern

Two functions in `eval.c` do the work:

- `eval_call_provider({name}, {method}, {args}, {discard})` — dispatches to `provider#{name}#Call`
- `eval_has_provider({name})` — checks `g:loaded_{name}_provider == 2`

Concrete impact: **7,500 fewer lines of C** in Nvim vs Vim just for Python support. See `:help dev-provider` for the authoritative writeup.

## The "some items conflict" corollary

The intro line — *"Some items conflict; this is intentional. A balance must be found."* — is the honest disclaimer. Concrete examples of the conflicts:

| Tension | Where you feel it |
|---|---|
| "No limit on features" ↔ "Fast and small" | Every new API function has to justify its startup and memory cost |
| "Backwards compatibility is a feature" ↔ "Reliable, maintainable code" | Legacy Vim behavior locked into `regexp.c`, `indent_c.c`, and much of `viml/` — cannot refactor freely |
| "Prefer Vim/Unix tradition" ↔ "The Neo bits should make it better" | Every departure from Vim needs a defense (`vim.pack` vs `packadd`; `vim.iter` vs `map()`/`filter()` vim builtins) |
| "Well documented" ↔ "Don't make it long" | Auto-generation from docstrings + tag-based cross-referencing to keep prose tight |
| "Composability" ↔ "Common commands must be fast" | Providers pay a subprocess round-trip cost; used only where the alternative is embedding an interpreter |

There is no formula that resolves these. Contributors argue them out per-change. This is why:

- Reviews sometimes go multiple rounds even for small PRs.
- Some features get rejected not because they're wrong but because the tradeoff doesn't land here.
- Reading `MAINTAIN.md` alongside these goals matters — it captures *procedural* answers to some of these tensions.

## How to use these goals as a contributor

The goals aren't decoration — they're PR review criteria. When you propose a change:

- **Adding a feature?** Which goal justifies it? "No limit on features" isn't a free pass — it presumes you also handle the docs, tests, and startup-cost concerns of the other four goals.
- **Refactoring?** Say which of maintainability / speed / clarity it improves, concretely. "Cleaner" is not a benefit.
- **Removing something?** Backwards-compat check first. If it's public API, the answer is almost always no.
- **Cross-cutting change?** Split it. One PR = one `type(scope)` pair (see the [[getting_involved#Contribution rules that trip newcomers up|contribution rules]]).
- **Doc-only?** Great — high leverage. Doc PRs are one of the fastest ways to earn credibility.

## How these goals connect to other notes in this vault

- [[core]] — the tree these goals shape (see especially the "frozen modules" discussion, which is `#4 Maintainable` in action)
- [[runtime]] — the Lua stdlib grows freely because Lua-side additions don't threaten `#3 Fast and small` on startup or the C hot path
- [[client_libraries]] and [[editor_integrations]] — exist because goal `#5 Not [an OS]` pushed the UI over an API
- [[deps]] and [[vendored_code]] — how goals `#3 Fast and small` and `#4 Maintainable` play against `#1 Improved` when it comes to third-party code
- [[getting_involved]] — where the goals become PR-review lore
- [[learning_path]] — the philosophy stage that makes the code make sense

## Related

- `:help design-goals` — authoritative source (`runtime/doc/dev.txt`)
- `:help dev-guidelines` — the practical extension of these goals into everyday coding rules
- `:help dev-provider` — the provider framework, exemplar of goal #5
- `MAINTAIN.md` at the neovim repo root — the *procedural* companion to these *philosophical* goals
