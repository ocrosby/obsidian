---
aliases: ["Neovim vendored code", "vendored deps", "libvterm", "libtermkey"]
tags: [neovim, ecosystem, dependencies, reference]
---

# Vendored code

Third-party sources that live *inside* the [[core|neovim/neovim]] tree rather than being fetched by [[deps|cmake.deps]] at build time. "Vendored" is the term of art — you copy the upstream sources into your repo, edit them in place, and take responsibility for keeping them in sync.

Distinct from **bundled deps** ([[deps]]) which are external tarballs downloaded on demand, and distinct from the ex-standalone-repo family described in the workspace CLAUDE.md (those are a *sub-category* of vendored code — see below).

Companion to [[core]] (this note is the detail behind its "Dependencies" section) and [[deps]] (the peer that covers everything vendored *outside* the source tree).

## Vendored vs bundled — the distinction that matters

| | Vendored | Bundled |
|---|---|---|
| Where the source lives | Inside `neovim/neovim` | Fetched at build time to `.deps/build/src/` |
| How it's updated | Copy new sources into the tree; commit | Bump URL + SHA256 in `cmake.deps/deps.txt` |
| Who edits it | Neovim maintainers can and do patch in place | Untouched — upstream owns the code |
| Where fixes ideally go | Upstream first if the project is alive, then re-vendor | Upstream — cannot patch a downloaded tarball |
| Blast radius of an upstream change | Zero — we're not tracking `master` | Full — pinned URL controls it |

Both are dependencies. The difference is control: vendoring trades "always up to date" for "cannot be broken by upstream."

## Two sub-kinds of vendored code

**A. Ex-standalone-repos that folded back into `neovim/`.** These used to be their own GitHub repos in the org (`neovim/libvterm`, `neovim/libtermkey`); the upstream repos are archived, and the vendored copy inside `neovim/` is *the* upstream now.

**B. Copies of live third-party projects.** The upstream still exists and is active; the vendored copy is a snapshot. Bug fixes should go upstream first.

Plus **generated artifacts checked into the tree** — not strictly "vendored code" but similar in that they live in-tree and can be regenerated on demand.

## A. Ex-standalone repos, folded into `src/nvim/`

### `src/nvim/vterm/` — the terminal emulator

- **Was:** `neovim/libvterm` (fork of Paul Evans' [libvterm](https://www.leonerd.org.uk/code/libvterm/))
- **Now:** 21 files, ~6,600 lines of C (`encoding`, `keyboard`, `mouse`, `parser`, `pen`, `screen`, `state`, `vterm.c` and headers) under `src/nvim/vterm/`
- **What it does:** the embedded terminal emulator that powers `:terminal`. Parses ANSI escape sequences, tracks screen state, handles input/output.
- **README:** literally *"Adopted from libvterm."* One line.
- **Fixes:** send them here. There is no live upstream to sync with.

### `src/nvim/tui/termkey/` — key event parser

- **Was:** [`neovim/libtermkey`](https://github.com/neovim/libtermkey) (fork of Paul Evans' [libtermkey](https://www.leonerd.org.uk/code/libtermkey/))
- **Now:** 9 files, ~3,200 lines of C (`driver-csi`, `driver-ti`, `termkey.c` and headers) under `src/nvim/tui/termkey/`
- **What it does:** parses terminal input into structured key events (function keys, modifiers, mouse). The TUI's keyboard-side counterpart to what libvterm does for output.
- **README:** *"Adapted from libtermkey: https://github.com/neovim/libtermkey"*
- **Fixes:** same as vterm — send here.

### The CLAUDE.md rule that matters

> Edit the vendored copies — the archived upstream repos are dead.

If you `git clone` the old `neovim/libvterm` looking to fix a bug, you'll find it archived. Go to `src/nvim/vterm/` in the core repo instead.

## B. Live third-party projects vendored as snapshots

Per `MAINTAIN.md § Vendored dependencies` in the core repo. Each has an upstream you should send fixes to *first*, then re-vendor.

### `src/mpack/` — msgpack-RPC C library

- **Upstream:** [libmpack/libmpack](https://github.com/libmpack/libmpack) + [libmpack/libmpack-lua](https://github.com/libmpack/libmpack-lua) for `lmpack.c`
- **Files:** `mpack_core.{c,h}`, `conv.{c,h}`, `object.{c,h}`, `rpc.{c,h}`, `lmpack.{c,h}` — MIT license (copyright Thiago de Arruda, one of Nvim's early founders — this is arguably as much "our" code as any dep)
- **Role:** the msgpack encode/decode layer that every [[client_libraries|client]] talks to
- **Update ritual:** send improvements upstream *first*, then sync back
- **Why vendored:** msgpack-RPC is on the hot path for every keystroke that comes from a UI or a script — pinning a specific version avoids protocol drift

### `src/xdiff/` — the diff engine from git

- **Upstream:** [git.git's `xdiff/` directory](https://github.com/git/git/tree/master/xdiff)
- **Files:** `xdiff.h`, `xdiffi.{c,h}`, `xemit.{c,h}`, `xhistogram.c`, `xinclude.h`, `xmacros.h`, `xpatience.c`, `xprepare.{c,h}`, `xtypes.h`, `xutils.{c,h}`
- **Role:** powers `vim.diff` (the Lua function) and `:diff` under the hood — Myers, patience, and histogram diff algorithms
- **Update ritual:** manual snapshot from git.git — the `README.txt` in the dir literally states *"The files were last updated August 31, 2021 from git release v.2.33.0"* — so the snapshot lags upstream by years and that is fine
- **Why vendored:** xdiff is not published as a standalone library; git carries it. Pulling it in as a dep would mean building git

### `src/cjson/` — JSON support for Lua

- **Upstream:** [openresty/lua-cjson](https://github.com/openresty/lua-cjson) — the `lua_cjson.c` file's header literally names the last-synced commit: `91ca29db9a4a4fd0eedaebcd5d5f3ba2ace5ae63`
- **Files:** `lua_cjson.{c,h}`, `fpconv.{c,h}`, `strbuf.{c,h}`
- **Role:** `vim.json.encode` / `vim.json.decode`
- **Update ritual:** manual sync when needed; update the commit hash comment when you do

### `src/klib/` — generic C container macros

- **Upstream:** [attractivechaos/klib](https://github.com/attractivechaos/klib) (MIT, copyright Attractive Chaos)
- **Files:** just `kvec.h` (the resizable array/vector macro)
- **Role:** used all over `src/nvim/` where a Go/Rust program would use a slice/Vec
- **Update ritual:** we only vendor `kvec.h`; the rest of klib (khash, kbtree, ksort, …) isn't currently needed

### `runtime/lua/vim/inspect.lua` — pretty-printer

- **Upstream:** [kikito/inspect.lua](https://github.com/kikito/inspect.lua) v3.1.0 (MIT, copyright Enrique García Cota)
- **Role:** `vim.inspect()` — the human-readable table dump you use in every `print(vim.inspect(x))` while debugging Lua
- **Update ritual:** copy the newer `inspect.lua`, update the `_VERSION` marker, done

## Generated artifacts checked into the tree

Not "vendored" in the strict sense — nobody hand-writes them — but they live in-tree instead of being generated at build time. Regenerable via a script.

### `src/nvim/tui/terminfo_defs.h` — compiled-in terminfo

- **Origin:** derived from the local system's `ncurses` terminfo database at generation time
- **Regenerate:** `scripts/update_terminfo.sh` (needs `ncurses` + `infocmp`)
- **Why checked in:** builds shouldn't depend on the *build machine's* terminfo — the compiled-in defaults ship with every Nvim binary
- **Sibling files:** `terminfo_builtin.h`, `terminfo_enum_defs.h`, `terminfo.{c,h}` in the same dir

### `runtime/lua/vim/lsp/_meta/protocol.lua` — LSP type annotations

- **Origin:** [Microsoft's LSP metaModel JSON](https://raw.githubusercontent.com/microsoft/language-server-protocol/gh-pages/_specifications/lsp/3.18/metaModel/metaModel.schema.json)
- **Regenerate:** `nvim -l src/gen/gen_lsp.lua --version 3.18`
- **Role:** LuaLS type annotations consumed by `lua-language-server` when editing Nvim's LSP code. Not loaded at Nvim runtime — pure tooling metadata
- **Bumping:** run the gen script with the new `--version`; commit the resulting file

### The `src/gen/*.lua` family (context, not vendored itself)

Adjacent to the LSP protocol case: 20+ generator scripts under `src/gen/` produce checked-in files from various inputs (API metadata, keycodes, help tags, ex commands, filetype detection). Same idea — the *output* is committed so builds don't require re-running generators every time, but the source of truth is the input + the script.

## The overall inventory

| Path | Upstream | Live? | LOC (rough) | Update mechanism |
|---|---|---|---:|---|
| `src/nvim/vterm/` | archived (was `neovim/libvterm`) | ❌ Fix here | 6,600 | Direct edits |
| `src/nvim/tui/termkey/` | archived (was `neovim/libtermkey`) | ❌ Fix here | 3,200 | Direct edits |
| `src/mpack/` | [libmpack/libmpack](https://github.com/libmpack/libmpack) | ✅ Send upstream | ~1,500 | Manual sync |
| `src/mpack/lmpack.c` | [libmpack/libmpack-lua](https://github.com/libmpack/libmpack-lua) | ✅ Send upstream | ~1,200 | Manual sync |
| `src/xdiff/` | [git.git `xdiff/`](https://github.com/git/git/tree/master/xdiff) | ✅ Snapshot | ~4,500 | Manual sync, last done 2021 |
| `src/cjson/` | [openresty/lua-cjson](https://github.com/openresty/lua-cjson) | ✅ Send upstream | ~2,000 | Manual sync; commit hash in header |
| `src/klib/` | [attractivechaos/klib](https://github.com/attractivechaos/klib) | ✅ Snapshot | ~200 (kvec.h only) | Manual sync |
| `runtime/lua/vim/inspect.lua` | [kikito/inspect.lua](https://github.com/kikito/inspect.lua) | ✅ Send upstream | ~340 | Manual sync |
| `src/nvim/tui/terminfo_defs.h` | ncurses terminfo | (generated) | 21 (header) | `scripts/update_terminfo.sh` |
| `runtime/lua/vim/lsp/_meta/protocol.lua` | LSP spec | (generated) | large | `src/gen/gen_lsp.lua --version <X>` |

## Working with vendored code in practice

| I want to… | Do this |
|---|---|
| Fix a terminal-emulation bug (colors, escape sequences) | Edit `src/nvim/vterm/` directly — libvterm upstream is archived |
| Fix a key-parsing bug (function keys, modifiers) | Edit `src/nvim/tui/termkey/` directly — libtermkey upstream is archived |
| Improve msgpack encoding/decoding | Send to [libmpack/libmpack](https://github.com/libmpack/libmpack) *first*, then re-vendor |
| Fix a JSON parsing bug | Send to [openresty/lua-cjson](https://github.com/openresty/lua-cjson) *first*, then re-vendor and bump the commit-hash comment |
| Improve `vim.inspect` output | Send to [kikito/inspect.lua](https://github.com/kikito/inspect.lua) *first*, then re-vendor |
| Improve diff algorithms | Send to git *first*, then re-sync `src/xdiff/` — but this is a very slow-cycling change |
| Update to a newer LSP spec version | `nvim -l src/gen/gen_lsp.lua --version <ver>` and commit the regenerated `protocol.lua` |
| Refresh terminfo definitions | `scripts/update_terminfo.sh` |

## Caveats

- **Don't treat archived upstreams as live.** `neovim/libvterm` and `neovim/libtermkey` still *exist* as archived repos on GitHub — the code is there, the git log is there. But they no longer receive changes. Any PR you'd open there is a PR into a dead repo. Send it here instead.
- **"Vendored" doesn't grant a license waiver.** Each dep keeps its own license — LICENSE-MIT lives next to `src/mpack/`, LICENSE next to `src/nvim/vterm/`, COPYING next to `src/xdiff/`. See `LICENSE.txt` at the repo root for the aggregate list.
- **Snapshot age is not a bug by itself.** `src/xdiff/` hasn't been updated since 2021 — that's fine because diff algorithms don't rot. But an old snapshot of `lua-cjson` might miss real security fixes; check upstream when in doubt.
- **The `_meta/protocol.lua` file is huge and looks scary.** It's generated. Do not hand-edit; regenerate.
- **Don't confuse `runtime/lua/vim/inspect.lua` with any user-installed `inspect` module.** Nvim uses its vendored copy via `vim.inspect`; user Lua code that `require`s a different `inspect` module gets whatever's on the runtimepath.

## Related

- [[core]] — where the vendored files sit within the source tree and how the build consumes them
- [[deps]] — the peer story for *external* dependencies (`cmake.deps/` and the [[readme|neovim/deps]] repo)
- [[readme]] — the workspace CLAUDE.md rule that documents the archived-upstream reality for vterm/termkey
- `LICENSE.txt` at the neovim repo root — aggregate license inventory
