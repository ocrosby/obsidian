---
aliases: ["Contributing to Neovim", "How to help with Neovim", "Neovim onboarding"]
tags: [neovim, ecosystem, contributing, onboarding, reference]
---

# Getting involved with Neovim

Two audiences, one note:

1. **You want to help.** You're happy to fix bugs, review PRs, backport Vim patches, or write docs — you just need to know where the work lives and how to send a change without it bouncing.
2. **You want to understand.** You want to actually grok how Nvim is built, not just poke at the edges. You need a reading order.

The two paths overlap: understanding the codebase makes the contributing part easier. This note gives you both — a pragmatic map for shipping your first PR, and a learning sequence for the deeper "how does this thing work?" journey.

Companion to [[core]] (source tree layout), [[runtime]] (the Lua stdlib), [[contributors]] (who else is working on this), and [[first_party_plugins]] (where nvim-lspconfig contributions go).

## Communication channels

Before touching code, know where the humans are:

| Channel | Use for |
|---|---|
| [Matrix chat: `#neovim:matrix.org`](https://app.element.io/#/room/#neovim:matrix.org) | Real-time questions, quick sanity checks with maintainers, general dev chat. The main gathering spot |
| [GitHub Discussions](https://github.com/neovim/neovim/discussions) | Longer-form questions about configuration and usage. "How do I…" belongs here, not in issues |
| [GitHub Issues](https://github.com/neovim/neovim/issues) | Bug reports, feature requests, tracked work. **Not** for support questions |
| [r/neovim](https://www.reddit.com/r/neovim/) | Community pulse — plugin announcements, config sharing, less formal dev discussion |
| Per-repo issue trackers | Each [[readme|ecosystem repo]] has its own — check the specific repo you're touching |

Blank issues are disabled on `neovim/neovim`; you pick from bug/feature/LSP-bug templates. If you have a question, the templates route you to Discussions.

## Where the work is

### For a first PR — `complexity:low`

`neovim/neovim` uses a `complexity:low` label to mark issues that don't require deep familiarity with the codebase:

- Filter: [`is:open is:issue label:complexity:low`](https://github.com/neovim/neovim/issues?q=is%3Aopen+is%3Aissue+label%3Acomplexity%3Alow)

These are the closest thing to "good first issue" here. Grab one, send a PR — the CONTRIBUTING.md is emphatic: **do not ask to be assigned**, just send a Draft PR when you have something.

### Bug fixes — Coverity defects

Neovim runs [Coverity Scan](https://scan.coverity.com/projects/neovim-neovim) against every master build. Fixing a Coverity finding is a well-scoped contribution:

1. [Request access to the project](https://scan.coverity.com/projects/neovim-neovim) — Coverity has no "public" view; a maintainer approves once they see the email
2. Pick a defect, fix it
3. Commit message format: `fix(coverity/{CID}): {description}` — where `{CID}` is Coverity's issue ID

Example: `git log --oneline --no-merges --grep coverity` shows the precedent.

### Vim patches — for Vim experts

Nvim continuously backports bug fixes and features from [Vim](https://github.com/vim/vim). This is high-throughput work — check [[contributors]]: `zeertzjq` and `Justin M. Keyes` both do a lot of this.

- Tooling: `scripts/vim-patch.sh` in the core repo helps fetch and apply a specific Vim patch
- Docs: [`:help dev-vimpatch`](https://neovim.io/doc/user/dev_vimpatch.html) is authoritative
- **Read the linked doc first** if you touch anything under `runtime/`:
  - Vimscript files there are (mostly) maintained by Vim — send changes to Vim first, then backport
  - Lua files under `runtime/lua/vim/` are maintained by Nvim — normal PR process
  - Filetype detection (`runtime/lua/vim/filetype.lua`) matches Vim, so filetype changes go to Vim first
- Commit prefix: `vim-patch:9.1.NNNN: <subject>`

Requires meaningful familiarity with Vim's codebase and conventions. Not a first-PR path.

### Docs — often the highest-leverage contribution

`:help` docs live in `runtime/doc/*.txt`. Many are auto-generated from C/Lua docstrings; others are hand-written. Bugs in docs mislead every future reader — fixing them is real work.

- Generate: `make doc`
- Lint: `make lintdoc`
- Style: `:help dev-doc` and `:help dev-lua-doc`
- Auto-generated pages start with a "DO NOT EDIT" comment — edit the source docstrings instead

### Sibling repos — often easier codebases to enter

`neovim/neovim` is 40k commits of C. If you want a first contribution but the core scares you:

- **[[first_party_plugins|nvim-lspconfig]]** — the LSP server catalog. Adding a new server (or improving an existing one) is well-scoped Lua work. Has 1,100+ historical contributors, so PR review is fast. See its own `CONTRIBUTING.md` — it has explicit criteria (server needs 100+ GitHub stars or comparable proof of an active user base) to keep spam out.
- **[[readme|neovim.github.io]]** — the website. Typo fixes, install-instruction updates, roadmap edits.
- **[[client_libraries|pynvim / node-client]]** — active clients that welcome contributions. Smaller codebases than core.

## The five-minute quickstart

From `:help dev-quickstart` — the fastest path from "cloned it" to "modified it and saw a test fail":

```bash
# 1. Clone
git clone https://github.com/neovim/neovim
cd neovim

# 2. Build (needs the prerequisites in BUILD.md — cmake, ninja, gcc/clang, gettext, etc.)
make
# Optional: run your fresh build
VIMRUNTIME=./runtime ./build/bin/nvim --luamod-dev

# 3. Run a single functional test — the example is deliberately simple
make functionaltest TEST_FILE=test/functional/example_spec.lua

# 4. Now modify the code the test exercises: src/nvim/insert.c
#    Find `insert_handle_key`, just after the `normalchar` label, add:
#      s->c = 'x';

# 5. Re-run the test — it should fail loudly with a screen diff
make functionaltest TEST_FILE=test/functional/example_spec.lua
```

If step 5 fails with a "Row 1 did not match" diff showing `xine1` instead of `line1`, you have a working dev loop. That's the whole prerequisite for contributing.

### Making that loop fast

- **Install `ninja`** — Nvim's build system uses it automatically if present. Much faster than plain Make.
- **Install `ccache` or `sccache`** — cached compiles. Disable with `cmake -B build -D CACHE_PRG=OFF` if it causes trouble.
- **Use `--luamod-dev`** — Lua modules under `runtime/lua/vim/_core/` are precompiled to bytecode; `--luamod-dev` + `VIMRUNTIME=./runtime` lets you edit-and-re-run without rebuilding.
- **Set `blame.ignoreRevsFile`** so `git blame` skips known noise commits:
  ```bash
  git config blame.ignoreRevsFile .git-blame-ignore-revs
  ```

## Contribution rules that trip newcomers up

Straight from `neovim/CONTRIBUTING.md`:

- **Don't ask to be assigned to an issue.** Send a Draft PR. Assignment is not how work gets claimed here.
- **Test coverage is required.** New behavior needs a test. See `:help dev-write-test`.
- **Rebase workflow, not merge.** After addressing review comments, force-pushing your feature branch is fine (encouraged, even).
- **Draft first, Ready when ready.** Use GitHub's Draft PR state — don't put `[WIP]` or `[RFC]` in the title. Convert to "Ready for review" when you're actually asking for review.
- **Conventional Commits, with a body.** The subject is `type(scope): summary`. The body should have `Problem:` and `Solution:` sections. Types: `build ci docs feat fix perf refactor revert test vim-patch`.
- **CI is `-Werror`.** Compiler warnings fail the build. `make lint` runs the linter locally — do this before pushing.
- **Formatter is `make format`** — uncrustify for C, stylua for Lua, tree-sitter formatter for queries.

### AI-assisted PRs — explicit rules

From `CONTRIBUTING.md § AI-assisted work` and `AGENTS.md`:

- **You review the output before marking Ready.** No requesting review on unreviewed AI output.
- **Remove verbosity.** AI generates blathering — strip it before you ship.
- **Deduplicate.** AI generates redundant code and tests — cut them.
- **Disclose.** Add an `AI-assisted: <tool name>` trailer to any commit that used AI. If you're committing manually, remind yourself.

The bar is not "did an AI help" — it's "is what you're shipping still your work in every meaningful sense."

## Reproducing bugs (for filing them well)

If you're not sure yet whether you want to *contribute* or just *report* — good bug reports are a real contribution.

- Check the [FAQ](https://neovim.io/doc/user/faq.html) and search existing (including closed) issues first.
- Update to the latest Nvim.
- Try `nvim --clean` (factory defaults).
- If a specific config is required to reproduce, use `contrib/minimal.lua` as a template:
  ```bash
  nvim --clean -u contrib/minimal.lua
  ```
- [Bisect your config](https://neovim.io/doc/user/starting.html#bisect) to narrow down the cause.
- If you can, `git bisect` the source to find the regression commit.
- For crashes: include a stacktrace (`:help dev-tools-backtrace`).
- Enable `$NVIM_LOG_FILE` — `:edit $NVIM_LOG_FILE` shows the current log.
- For segfaults or undefined behavior: build with ASAN/UBSAN (`CC=clang make CMAKE_FLAGS="-DENABLE_ASAN_UBSAN=ON"`) and reproduce.

## Learning path: actually understanding the implementation

If you want to move past "I can send a bug fix" to "I understand how Nvim is built," here is a reading + doing order that mirrors how the codebase is actually organized.

### Stage 1 — Philosophy and constraints (1 evening)

Read the design docs before the code. They explain *why* things are the way they are — you'll waste less time being surprised.

- **`:help design-goals`** (`runtime/doc/dev.txt`, ~line 20) — the core priorities. "Nvim is Improved / Well-Documented / Fast and Small / Maintainable / Not [an OS]" and the tradeoffs between them.
- **`MAINTAIN.md`** at the repo root — how the project is actually run: issue triage philosophy, release policy, dep bumping, label conventions, backport rules.
- **`README.md`** — the elevator pitch and feature list. Skip if you already use Nvim.

Key idea to internalize: **the RPC API never breaks**. That's a design promise. It shapes every decision about API surface.

### Stage 2 — Architecture and layout (a weekend)

- **`:help dev-arch`** (`runtime/doc/dev_arch.txt`) — the map. Where C lives, where Lua lives, what the four kinds of Lua code are (plugins, opt plugins, stdlib, `_core/`), event handling, UI events, filesystem conventions.
- **[[core]]** — the annotated tour of `src/nvim/`, `runtime/`, `test/`.
- **`src/nvim/README.md`** and the header comments at the top of major C files (`terminal.c`, `undo.c`, `buffer.c`, `screen.c`, …). The convention is that major modules explain themselves at the top. Not every file does, but the important ones try to.

Key ideas to internalize:
- **Editor events** (autocmds) and **UI events** are two separate mechanisms today, but the long-term vision is to unify them — see `:help dev-events`.
- **New functionality goes in Lua, not C.** Explicit contributor guidance. The C core is being frozen in scope; growth happens in the Lua stdlib.
- **The msgpack-RPC API is the source of truth for what Nvim can do.** UIs and [[client_libraries|clients]] both consume the same surface.

### Stage 3 — Data structures and key files (a few sessions)

You cannot skim these — you need to read them and try things.

**Core C data structures:**

- **`src/klib/kvec.h`** — the resizable vector macro used all over. Read the example at the top; understand `kvec_t(T)`, `kv_push`, `kv_A`.
- **`src/nvim/lib/queue_defs.h`** — intrusive linked list. Used when you specifically need list semantics (rare — reach for kvec first).
- **`src/nvim/memline.c`** — buffer text storage. The central function is `ml_find_line`. Buffer text is a tree of line segments; this is where.

**Data files that define editor concepts** (Lua, generated into C):

- `src/nvim/auevents.lua` — every autocmd event
- `src/nvim/ex_cmds.lua` — every Ex command
- `src/nvim/options.lua` — every option
- `src/nvim/eval.lua` — every builtin Vimscript function
- `src/nvim/vvars.lua` — every `v:` variable

Editing one of these files and rebuilding is often the first "real" change a contributor makes. The generator scripts under `src/gen/*.lua` turn them into the C tables and the auto-generated `:help` pages.

**The API layer:**

- `src/nvim/api/*.c` — every `nvim_*` function the outside world calls. Read `vim.c`, `buffer.c`, `window.c` for shape.
- `src/nvim/api/dispatch_deprecated.lua` — the rename ledger.

### Stage 4 — Pick a subsystem, own it (as long as it takes)

You cannot understand all of Nvim. Nobody does. Pick one:

| Interest | Read |
|---|---|
| Terminal handling | `src/nvim/vterm/` (embedded emulator) + `src/nvim/tui/` (TUI + termkey) + `:help dev-tools-tui` |
| The event loop | `src/nvim/event/`, `src/nvim/channel.c`, `src/nvim/msgpack_rpc/` |
| The Vim script interpreter | `src/nvim/viml/`, `src/nvim/eval/` |
| Buffer + text handling | `src/nvim/buffer.c`, `src/nvim/memline.c`, `src/nvim/mark.c`, `src/nvim/change.c` |
| LSP | `runtime/lua/vim/lsp/` (all Lua — much friendlier than C) |
| Tree-sitter | `runtime/lua/vim/treesitter/` + `src/nvim/lua/treesitter.c` |
| Windows/tabs | `src/nvim/window.c`, `src/nvim/win_config.c` |
| Diagnostics | `runtime/lua/vim/diagnostic.lua` and `runtime/lua/vim/diagnostic/` |

**How to actually read a subsystem:** trace one operation end-to-end. Pick something concrete — "what happens when I press `i`?" or "how does `:diffthis` set up a diff?" — and follow the path from input → dispatch → work → screen update. Skim files freely; read carefully only where the interesting logic lives.

### Stage 5 — The developer docs (reference, not sequential)

Once you have context, these become useful lookups rather than dense mysteries:

- **`:help dev`** — general developer guidelines, providers, docs
- **`:help dev-arch`** — architecture (revisit; now you'll understand more)
- **`:help dev-tools`** — logs, TUI debugging, performance, backtraces, gdb, ASAN
- **`:help dev-test`** — writing tests, running tests, filtering, fixing
- **`:help dev-style`** — the C style guide (headers, scoping, features, naming, comments, formatting)
- **`:help dev-theme`** — colorscheme guidelines
- **`:help dev-vimpatch`** — the Vim-patch backport process
- **`:help dev-ui`** — for UI developers (external GUIs, [[editor_integrations|integrations]])
- **`:help dev-api-client`** — for [[client_libraries|API client]] developers
- **`:help dev-doc`** and **`:help dev-lua-doc`** — how to write docs that generate correctly

## Debugging and tools

For when something goes wrong (yours or Nvim's):

- **Logs**: `:edit $NVIM_LOG_FILE`. Enable more with debug builds (`make CMAKE_BUILD_TYPE=Debug`).
- **ASAN/UBSAN**: build with `CC=clang make CMAKE_FLAGS="-DENABLE_ASAN_UBSAN=ON"`. Run with `ASAN_OPTIONS=log_path=/tmp/nvim_asan nvim …`. Check `/tmp/nvim_asan.<PID>` if it crashes.
- **Valgrind**: `VALGRIND=1 make test`.
- **gdb**: see `:help dev-tools-gdb`. `contrib/gdb/` has helpers.
- **`LOG_CALLSTACK()`** (Linux, in debug builds with `-no-pie`) logs a stacktrace from anywhere in C code.
- **Performance**: `:help dev-tools-perf` — perf, flamegraphs, benchmark suite under `test/benchmark/`.
- **Include-what-you-use**: `cmake --preset iwyu && cmake --build build` or `make iwyu` to fix `#include` bloat.

## Reviewing PRs (also a contribution)

Reviewing is high-leverage. From CONTRIBUTING.md:

```bash
gh pr checkout https://github.com/neovim/neovim/pull/1820
git log -p master..FETCH_HEAD          # commits in this PR
git log -pW master..FETCH_HEAD         # with surrounding function context
```

You don't need commit rights to review; leaving thoughtful comments helps maintainers triage faster.

## Roles beyond code

- **Triage**: reproducing bugs, minimizing repros, closing dupes.
- **Docs**: fixing `:help` errors, improving examples, updating outdated pages.
- **Website**: [[readme|neovim.github.io]] contributions — mostly Markdown + Hugo.
- **Distribution**: distro packagers, Snap ([[infrastructure|neovim-snap]]), Homebrew, AUR. Each has its own repo/maintainer.
- **Answering questions**: Matrix chat, Discussions, r/neovim. Reduces load on maintainers directly.

## Related

- [[core]] — the source tree you're contributing to
- [[runtime]] — the Lua stdlib where most new functionality now lives
- [[contributors]] — who else has done this work (for context on norms and volume)
- [[first_party_plugins]] — nvim-lspconfig, the other main entry point for contributions
- [[client_libraries]] — remote-plugin authoring in your language of choice
- [[activity_snapshot]] — which repos are actually alive and worth investing in
- [[../learning_guide]] — using Neovim (parent-folder note, distinct from this one which is about building it)
