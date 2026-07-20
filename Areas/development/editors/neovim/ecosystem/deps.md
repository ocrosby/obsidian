---
aliases: ["Neovim dependencies", "neovim/deps", "cmake.deps"]
tags: [neovim, ecosystem, dependencies, build, reference]
---

# Neovim dependencies

"Deps" is ambiguous in the Neovim world — there are three separate things that all get called that:

1. **`neovim/cmake.deps/`** — a directory *inside* the core repo. Version pins + CMake build recipes. This is the source of truth for what version of libuv, LuaJIT, tree-sitter, etc. ships in the next release.
2. **[neovim/deps](https://github.com/neovim/deps)** — a *separate* GitHub repo, a write-only prebuilt cache of dependency sources. Exists so PPA builds without network access can still build Nvim.
3. **Vendored deps under `neovim/src/`** — small libraries inlined into the tree and updated by hand. Covered in [[core#Dependencies from MAINTAIN md]].

This note is the map to all three, plus the current pinned versions as of 2026-07-20.

Companion to [[core]] (which touches deps in passing) and [[readme]] (structural map of the ecosystem).

## The three layers, side by side

| Layer | Location | Updated by | Update mechanism |
|---|---|---|---|
| Version pins | `neovim/cmake.deps/deps.txt` | Maintainers | Bump URL + SHA256 in `deps.txt`; often auto-bumped by `scripts/bump_deps.lua` |
| Prebuilt source cache | [`neovim/deps`](https://github.com/neovim/deps) repo, `src/` | GitHub Actions | Nightly workflow packages what `cmake.deps` would fetch |
| Vendored (inlined) | `neovim/src/mpack/`, `src/xdiff/`, `src/cjson/`, `src/klib/`, `runtime/lua/vim/inspect.lua`, `src/nvim/tui/terminfo_defs.h`, `runtime/lua/vim/lsp/_meta/protocol.lua` | Maintainers | Copy from upstream manually; MAINTAIN.md tracks each |

## Layer 1: `cmake.deps/` — the source of truth

Lives at `neovim/cmake.deps/` inside the core repo:

```
cmake.deps/
├── deps.txt              ← version + SHA256 for every third-party dep
├── CMakeLists.txt        ← wires ExternalProject_Add for each
├── CMakePresets.json     ← build presets (Debug/Release/…)
└── cmake/                ← per-dep CMake modules (BuildLibuv.cmake, BuildLuaJIT.cmake, …)
```

`make deps` (or the equivalent CMake invocation) downloads each URL, verifies the SHA256, and builds it into `.deps/build/`. Set `USE_BUNDLED=OFF` to link against system copies instead.

Auto-bump helper: `scripts/bump_deps.lua` walks `deps.txt` and updates version-pinned deps that expose a `latest` tag on GitHub. Some deps (LuaJIT tracks a specific commit, not a tag; libuv requires a companion PR to the Zig build package) need manual handling — the top of `MAINTAIN.md § Third-party dependencies` calls these out.

### Currently pinned (2026-07-20, from `deps.txt`)

**Runtime + interpreters**

| Dep | Version / ref | Notes |
|---|---|---|
| LuaJIT | commit `14d8a7a2` | Tracks a specific commit rather than a tag — LuaJIT has no proper release cadence |
| Lua | 5.1.5 | Fallback interpreter for LuaJIT-hostile platforms |
| Lua-compat-5.3 | v0.13 | Shims for stdlib functions only in Lua 5.3+ |
| libuv | v1.52.1 | Async I/O, event loop, subprocess, filesystem |
| Luv | 1.52.1-0 | Lua bindings for libuv — the versioning tracks libuv |
| lpeg | 1.1.0 | Lua PEG parser — fetched from a pinned snapshot in the `neovim/deps` repo (`opt/lpeg-1.1.0.tar.gz`) |

**Locale + encoding**

| Dep | Version | Notes |
|---|---|---|
| gettext | 0.20.1 | Fetched from `neovim/deps`, not GNU FTP (which is flaky) |
| libiconv | 1.17 | Same — pinned tarball in `neovim/deps` |
| utf8proc | 2.11.3 | Unicode normalization + case folding |

**Terminal**

| Dep | Version | Notes |
|---|---|---|
| unibilium | v2.1.2 | The `neovim/unibilium` fork (upstream abandoned). Optional — `USE_BUNDLED_UNIBILIUM=OFF` skips it entirely |

**Tree-sitter**

| Dep | Version | Notes |
|---|---|---|
| tree-sitter | commit `d11d18f7` | The core parsing library |
| tree-sitter-c | v0.24.2 | Bundled parser |
| tree-sitter-diff | v0.1.0 | |
| tree-sitter-lua | v0.5.0 | Also ships a WASM build for the tree-sitter playground/dev |
| tree-sitter-vim | v0.8.1 | |
| tree-sitter-vimdoc | v4.1.0 | From the [[readme|neovim/tree-sitter-vimdoc]] ecosystem repo |
| tree-sitter-query | v0.8.0 | |
| tree-sitter-markdown | v0.5.3 | |

These are the *only* parsers shipped in-tree. Everything else (Python, Rust, Go, TS, …) is installed by the user via nvim-treesitter or `vim.pack`.

**WASM host**

| Dep | Version | Notes |
|---|---|---|
| wasmtime | v36.0.12 | Runtime for WASM-based tree-sitter parsers |

**Windows-only**

| Dep | Version | Notes |
|---|---|---|
| win32yank | v0.1.1 | Clipboard interop on Windows (x86_64) |

**Developer tooling (not shipped in the binary)**

| Dep | Version | Notes |
|---|---|---|
| uncrustify | 0.83.0 | C code formatter used by CI + `make format` |

## Layer 2: the `neovim/deps` ecosystem repo

A separate GitHub repo at `~/src/github.com/neovim/deps/`. Its own README calls it a **write-only** repo — humans don't hand-edit it; a workflow updates it whenever `neovim/cmake.deps/deps.txt` changes.

### What's in it

```
deps/
├── Makefile              ← builds/repackages the manually-managed deps
├── README.md             ← self-describing
├── src/                  ← auto-updated cache of dep sources (~30 folders)
│   ├── libuv/            ← extracted source of the pinned libuv version
│   ├── libuv-stamp       ← ExternalProject stamp file
│   ├── luajit/, luajit-stamp
│   ├── luv/, luv-stamp
│   ├── lpeg/, lpeg-stamp
│   ├── treesitter/, treesitter-stamp
│   ├── treesitter_{c,lua,vim,vimdoc,query,markdown,diff}/
│   ├── utf8proc/, utf8proc-stamp
│   ├── unibilium/, unibilium-stamp
│   └── lua_compat53/, lua_compat53-stamp
└── opt/                  ← manually-managed dep tarballs
    ├── gettext-0.20.1.tar.gz
    ├── libiconv-1.17.tar.gz
    └── lpeg-1.1.0.tar.gz
```

### Why it exists

The [Launchpad PPA](https://launchpad.net/~neovim-ppa/+archive/ubuntu/unstable) builds Nvim in a sandboxed environment with **no network access**. That means `cmake.deps` cannot fetch upstream tarballs during the build. The workaround:

1. GitHub Actions (see `.github/workflows/nightly.yaml` in the deps repo) periodically runs `make deps` in the neovim repo, then commits the resulting `.deps/build/src/` into `neovim/deps/src/`.
2. The PPA build fetches the `neovim/deps` repo *before* the build starts.
3. The Nvim CMake build is invoked with `USE_EXISTING_SRC_DIR=ON`, pointing at the deps repo's `src/`. No downloads needed.

`opt/` is separate: it holds tarballs from sources that are too unreliable to auto-fetch (GNU FTP has been flaky for gettext and libiconv for years). These are updated by a human running the `Makefile` in the deps repo.

### Recent activity

The last 7 days show 3 `deps src: Automatic update` commits — the workflow fires whenever the upstream `deps.txt` changes. Human commits to this repo are rare (usually only the `opt/` tarballs or the workflow config itself).

## Layer 3: vendored deps

Small enough that inlining is cheaper than a build-system dependency. Covered in detail in [[core#Dependencies from MAINTAIN md]], summarized here:

| Path | Upstream | Update ritual |
|---|---|---|
| `src/mpack/` | [libmpack/libmpack](https://github.com/libmpack/libmpack) | Copy from upstream; send bug fixes back |
| `src/mpack/lmpack.c` | [libmpack/libmpack-lua](https://github.com/libmpack/libmpack-lua) | Same |
| `src/xdiff/` | git's `xdiff/` | Copy from git.git; used for `:h vim.diff` |
| `src/cjson/` | [openresty/lua-cjson](https://github.com/openresty/lua-cjson) | JSON encode/decode in Lua |
| `src/klib/` | [attractivechaos/klib](https://github.com/attractivechaos/klib) | Generic C containers (khash, kvec, kbtree, …) — used all over `src/nvim/` |
| `runtime/lua/vim/inspect.lua` | [kikito/inspect.lua](https://github.com/kikito/inspect.lua) | `vim.inspect()` |
| `src/nvim/tui/terminfo_defs.h` | generated from ncurses' terminfo DB | Regenerate via `scripts/update_terminfo.sh` |
| `runtime/lua/vim/lsp/_meta/protocol.lua` | LSP spec | Copy from Microsoft's LSP spec repo |

## Frozen legacy modules

Not deps in the ExternalProject sense, but similar in spirit: parts of `src/nvim/` that are effectively maintained by Vim upstream and refactoring them is discouraged.

- `regexp.c` — Vim's regex engine
- `indent_c.c` — C indent logic

Behavioral fixes go to Vim first, then backport via `scripts/vim-patch.sh` (same as any other Vim patch — see [[core#Vim patches]]).

## Working with deps in practice

| I want to… | Do this |
|---|---|
| Bump libuv or LuaJIT | Edit `cmake.deps/deps.txt` (URL + SHA256), run `scripts/bump_deps.lua` if applicable, PR to core |
| Add a new tree-sitter parser to core | New `TREESITTER_<lang>_URL`/`_SHA256` in `deps.txt`, entry in `cmake.deps/CMakeLists.txt`, and queries under `runtime/queries/<lang>/` |
| Build offline / for the PPA | `make USE_EXISTING_SRC_DIR=ON DEPS_SRC=/path/to/neovim/deps/src` |
| Skip unibilium at build time | `cmake -DUSE_BUNDLED_UNIBILIUM=OFF` (details in `BUILD.md`) |
| Use system libs (Debian, Homebrew) | `cmake -DUSE_BUNDLED=OFF` — every dep uses the system version instead |
| Regenerate terminfo | `scripts/update_terminfo.sh` (vendored, needs `ncurses` + `infocmp` locally) |
| Update the LSP protocol types | Copy the latest spec into `runtime/lua/vim/lsp/_meta/protocol.lua`; regenerate anything downstream |

## Caveats

- **The `neovim/deps` repo is `master`-branch only.** Do not open PRs against it expecting review — it's automation output. Human PRs there should only touch the workflow config or `opt/`.
- **`deps.txt` SHA256 mismatch is the #1 build failure.** If a URL changes content without a version bump (e.g. GitHub retagged a release), the SHA guard trips. Fix by verifying the URL, downloading, recomputing the SHA, and updating both fields.
- **LuaJIT commit-tracking is intentional.** Do not "helpfully" replace the commit hash with a version tag — LuaJIT has no releases that Neovim can safely track.
- **Vendored ≠ bundled.** Anything under `src/nvim/`, `src/mpack/`, `src/xdiff/`, `src/cjson/`, `src/klib/` is *inside* the core repo and does not touch `cmake.deps`. Bumping them means committing new source files, not new URLs.

## Related

- [[core]] — where the vendored deps live and how the build consumes bundled ones
- [[readme]] — the `deps` repo as part of the ecosystem map
- [[activity_snapshot]] — the deps repo's commit tempo (mostly bot commits)
- MAINTAIN.md in the neovim repo — authoritative for the update ritual
