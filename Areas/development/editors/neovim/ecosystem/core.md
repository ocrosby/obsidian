---
aliases: ["neovim/neovim", "Neovim core repo", "Neovim source"]
tags: [neovim, ecosystem, reference]
---

# `neovim/neovim` — the core repo

The editor itself: C core, Lua runtime, and the msgpack-RPC API that every [[client_libraries|client library]] speaks to. This note is the practical field guide to the source tree — what lives where, how it builds, and which parts you can actually change vs which are frozen upstream Vim code.

Companion to [[readme]] (structural map of all 17 repos), [[contributors]] (who wrote it), [[activity_snapshot]] (how alive it is), and [[client_libraries]] (how the outside world talks to it).

## What it is

Neovim is Vim, aggressively refactored to:

- Simplify maintenance (drop dead platforms, modernize the C, extract the UI over a socket)
- Split work across contributors (Vim was ~one-person; Neovim's ranking shows 1,500+ authors)
- Enable external GUIs and API clients without modifying the core
- Maximize extensibility — hence msgpack-RPC and first-class Lua

Local clone: `~/src/github.com/neovim/neovim` (part of the [[readme|ecosystem workspace]]).

## Provenance snapshot

- **Version at capture (2026-07-20):** `v0.12.4` released `2026-07-05`; HEAD `09a4cc8c50` on `master`.
- **First commit:** 2014-01-31 (fork of Vim). ~12.5 years of history.
- **Total commits:** ~40,000 across ~1,568 distinct author identities.
- **Release cadence (recent):** stable minor every ~6 months, patches every 4–8 weeks.

Recent releases for scale:

| Tag | Date |
|---|---|
| v0.12.4 | 2026-07-05 |
| v0.12.3 | 2026-06-11 |
| v0.12.2 | 2026-04-22 |
| v0.12.1 | 2026-04-06 |
| v0.12.0 | 2026-03-29 |
| v0.11.0 | 2025-03-26 |
| v0.10.0 | (mid-2024) |

`stable` and `nightly` tags track the current release and master, respectively.

## Language mix by file count

| Language | Files | Where |
|---|---:|---|
| C source (`.c`) | 210 | `src/nvim/` — editor core |
| C headers (`.h`) | 288 | `src/nvim/` and `src/nvim/*/` |
| Lua (runtime) | 198 | `runtime/lua/vim/*` — user-facing stdlib |
| Lua (source-side) | 39 | `src/nvim/*.lua` — build-time generators, event tables |
| Vim script (runtime) | 1,761 | `runtime/{plugin,ftplugin,indent,syntax,colors,…}` |
| Zig | 8 | `build.zig`, `runtime/gen_runtime.zig`, and the emerging Zig build path |

The eye-catching number is 1,761 `.vim` files — these are the filetype detection, syntax highlighting, indent rules, and default plugins inherited (and continuously updated) from Vim. Most changes happen in C and Lua; the Vim script bulk is mostly maintained via [Vim-patch backports](#vim-patches).

## Top-level tour

```
neovim/
├── src/nvim/          ← the editor: C core + a bit of Lua glue
├── runtime/           ← what nvim loads at runtime (Lua stdlib, ftplugins, docs)
├── test/              ← Lua/Busted tests: unit, functional, benchmark
├── deps/              ← thin wrapper around cmake.deps for third-party deps
├── cmake/             ← CMake modules, presets, packaging config
├── cmake.deps/        ← version pins + build recipes for bundled deps
├── contrib/           ← editor plugins for the editor (vscode, treesitter grammar hooks)
├── scripts/           ← maintainer tooling (bump_deps.lua, gen_help_html.lua, …)
├── AGENTS.md          ← guidance for AI agents editing this repo
├── BUILD.md           ← how to build from source
├── CONTRIBUTING.md    ← PR + commit conventions
├── MAINTAIN.md        ← maintainer-only ops: releases, deps, backports
└── CMakeLists.txt     ← top-level build
```

## `src/nvim/` — the C core

Roughly 500 `.c`/`.h` files. Notable subdirectories:

- **`api/`** — the msgpack-RPC surface. `vim.c`, `buffer.c`, `window.c`, `tabpage.c`, `command.c`, `extmark.c`, `autocmd.c`, `ui.c`, `options.c`, `vimscript.c`, `win_config.c`, `events.c`. Every `nvim_*` function you call from Lua or a client library is defined here. `dispatch_deprecated.lua` lists renames.
- **`lua/`** — the Lua ↔ C boundary (bindings, luaref, luajit setup).
- **`os/`** — libuv wrappers, filesystem, process, tty, environment.
- **`tui/`** — the terminal UI. `termkey/` is vendored [[readme|libtermkey]].
- **`vterm/`** — embedded terminal emulator. Vendored [[readme|libvterm]].
- **`msgpack_rpc/`, `event/`, `channel.c`** — the RPC transport and event loop.
- **`viml/`** — the Vim script interpreter (parser, ex commands, expr eval).
- **`buffer.c`, `window.c`, `screen.c`, `edit.c`, `normal.c`, `insexpand.c`, `mark.c`, `search.c`** — the classic Vim guts, gradually being tamed.

## `runtime/lua/vim/` — the user-facing Lua stdlib

This is what `:help lua-stdlib` documents. The modules users actually reach for:

| Module | Purpose |
|---|---|
| `vim.lsp` | Built-in LSP client |
| `vim.diagnostic` | Unified diagnostic display + navigation |
| `vim.treesitter` (via `queries/`) | Syntax trees, highlighting, folding |
| `vim.fs` | Path manipulation, `find`, `dir`, `parents` |
| `vim.iter` | Lua iterator API (`vim.iter(t):map(...)`) |
| `vim.pack` | Native plugin manager (new in v0.12) |
| `vim.net` | HTTP client (new-ish) |
| `vim.keymap` | Ergonomic keymap definitions |
| `vim.loader` | Faster Lua module loading with bytecode cache |
| `vim.health` | `:checkhealth` framework |
| `_meta/` | LuaLS type definitions consumed by editor tooling |

The `_`-prefixed modules (`_async`, `_comment`, `_inspector`, `_watch`) are internal — do not rely on them from plugins.

## `test/` — how it's tested

- **`test/unit/`** — pure C unit tests, compiled and run against libnvim internals.
- **`test/functional/`** — the biggest and most-run suite. Spawns a real nvim process, drives it over msgpack-RPC, asserts on the screen state. This is where you write tests for API changes.
- **`test/benchmark/`** — perf regression sensors.
- **`test/old/`** — legacy Vim script tests carried over from Vim; `make oldtest`.
- **Framework:** Busted (Lua BDD). `harness.lua`, `runner.lua`, `testutil.lua` are the plumbing.

Common invocations from the repo root:

```bash
make functionaltest                                   # full functional suite
make functionaltest TEST_FILE=test/functional/api     # scoped
make unittest                                         # C-level
make oldtest                                          # inherited Vim tests
```

## Build system

Primary: **CMake**. Secondary path in progress: **Zig** (`build.zig`, `build.zig.zon`) — new but not yet the default.

Minimal build from source:

```bash
cd neovim
make CMAKE_BUILD_TYPE=Debug        # or Release
sudo make install                  # optional; nvim is at build/bin/nvim otherwise
```

`BUILD.md` is authoritative. Notable knobs:

- `CMAKE_INSTALL_PREFIX=~/.local` — user-scope install
- `CMAKE_BUILD_TYPE=RelWithDebInfo` — release + backtraces
- Third-party deps auto-fetch via `cmake.deps/` unless you set `USE_BUNDLED=OFF`

## Dependencies (from `MAINTAIN.md`)

### Bundled (fetched by cmake.deps, version-pinned in `cmake.deps/deps.txt`)

- **LuaJIT** — the runtime that `require` loads Lua code into
- **Lua 5.1** — fallback interpreter for LuaJIT-hostile platforms
- **libuv** — async I/O, event loop, subprocess
- **Luv** — Lua bindings for libuv (`vim.uv`)
- **gettext, libiconv** — locale + encoding
- **lua-compat-5.3** — shims for 5.3-only stdlib
- **tree-sitter** + a set of parsers (C, Lua, Vim, vimdoc, markdown, query, python, …)
- **unibilium** *(deprecated, optional)* — terminfo parser fork under [[readme|neovim/unibilium]]; build works without it

Auto-bump via `scripts/bump_deps.lua`.

### Vendored (inlined in the source tree, updated by hand)

- `src/mpack/` — libmpack; send improvements upstream
- `src/mpack/lmpack.c` — libmpack-lua
- `src/xdiff/` — xdiff from git
- `src/cjson/` — lua-cjson
- `src/klib/` — Klib generic containers
- `runtime/lua/vim/inspect.lua` — kikito's inspect.lua
- `src/nvim/tui/terminfo_defs.h` — generated terminfo (regenerate via `scripts/update_terminfo.sh`)
- `runtime/lua/vim/lsp/_meta/protocol.lua` — LSP spec types

### Frozen legacy modules

Owned by Vim upstream; do not refactor for style or structure:

- `regexp.c`
- `indent_c.c`

Behavioral fixes to these should ideally be sent to Vim first, then backported here as a [Vim-patch](#vim-patches).

## Vim patches

A large chunk of `neovim/neovim` commits are **Vim patch backports** — Vim bug fixes and feature additions replayed against Neovim's tree. Look for commit messages like `vim-patch:9.1.0123: <subject>`. This is why `zeertzjq` and `Justin M. Keyes` dominate the [[contributors]] ranking: they do a lot of the mechanical Vim-patch work.

Tooling: `scripts/vim-patch.sh` fetches a specific Vim patch and helps apply it.

## Documentation

- **`runtime/doc/`** — 136 `.txt` files, the canonical `:help` pages. Editing these is a normal PR type.
- **`runtime/doc/news.txt`** — user-visible changes; kept up-to-date by convention.
- Rendered HTML at [neovim.io/doc](https://neovim.io/doc/) is generated by [[readme|neovim.github.io]] from these files.

## Contribution conventions (from `CONTRIBUTING.md`)

- Commits follow **Conventional Commits** (`fix(window):`, `feat(lsp):`, `docs:`, `refactor(api):`).
- One PR = one logical change.
- Functional tests are usually required for API changes.
- The `AGENTS.md` file at the repo root documents rules specifically for AI-agent contributors — read it before letting an agent commit here.

## When to actually edit which layer

| I want to… | Edit here |
|---|---|
| Add a new `nvim_*` API function | `src/nvim/api/` — corresponding `.c` file, header, and a functional test |
| Add a Lua stdlib helper | `runtime/lua/vim/<module>.lua` + doc in `runtime/doc/lua.txt` + test in `test/functional/lua/` |
| Fix a keymap default | `runtime/plugin/*.vim` or `runtime/keymap/*` |
| Add a filetype detection rule | `runtime/lua/vim/filetype.lua` (data-driven) |
| Bump a bundled dep | `cmake.deps/deps.txt` — or run `scripts/bump_deps.lua` |
| Fix an inherited Vim bug | Send patch to Vim first, then backport via `scripts/vim-patch.sh` |
| Fix `regexp.c` or `indent_c.c` behavior | Same — Vim first, backport second |
| Add a `:checkhealth` provider | `runtime/lua/vim/health/` + register via `vim.health` |

## Related

- [[readme]] — where this repo sits in the ecosystem
- [[client_libraries]] — the five language clients that call this repo's msgpack-RPC API
- [[contributors]] — full author ranking of this repo
- [[activity_snapshot]] — commit tempo here vs the rest of the ecosystem
- [[../learning_guide]] — using Neovim (not building it)
