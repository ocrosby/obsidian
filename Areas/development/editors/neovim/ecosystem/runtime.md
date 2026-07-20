---
aliases: ["Neovim runtime", "VIMRUNTIME", "runtime/"]
tags: [neovim, ecosystem, reference]
---

# The Neovim runtime

Everything Nvim loads *at runtime* rather than compiles in. Lives at `neovim/runtime/` in source, installed to `$VIMRUNTIME` (usually `/usr/share/nvim/runtime/` or `$prefix/share/nvim/runtime/`). This is the layer between the C editor and your `~/.config/nvim/` — the built-in Lua stdlib, filetype detection, syntax files, docs, and the small set of bundled plugins.

Companion to [[core]] (the C side and how the runtime is built + installed) and [[readme]] (where this fits in the ecosystem).

## What "runtime" actually means

At startup, Nvim assembles a **runtime path** (`:h 'runtimepath'`, aka `rtp`) — an ordered list of directories. When you `:runtime some/file.vim`, `:colorscheme habamax`, or open a `.py` buffer that needs an ftplugin, Nvim walks the rtp in order and loads the first match.

Default rtp order (roughly):

1. `~/.config/nvim/` — user config (`XDG_CONFIG_HOME`)
2. `~/.local/share/nvim/site/` — user runtime (`XDG_DATA_HOME`)
3. Plugin packages under both, from `pack/*/start/*/` (auto-loaded) and `pack/*/opt/*/` (loaded via `:packadd`)
4. **`$VIMRUNTIME`** — this repo's `runtime/` folder, shipped with Nvim
5. Then `after/` directories in reverse

The bundled runtime is the *last* Nvim-owned entry, so user config always wins. `:h 'runtimepath'` and `:h load-plugins` document the full order.

## Top-level layout

```
runtime/
├── lua/                      ← Lua stdlib (`vim.*`) + internal Lua
├── plugin/                   ← default plugins auto-loaded at startup
├── pack/dist/opt/            ← bundled optional plugins (loaded via :packadd)
├── ftplugin/                 ← per-filetype settings (458 files)
├── syntax/                   ← syntax highlighting (787 files)
├── indent/                   ← per-filetype indent rules (188 files)
├── compiler/                 ← `:compiler` definitions (137 files)
├── autoload/                 ← lazy-loaded Vim script functions
├── colors/                   ← 29 built-in color schemes
├── keymap/                   ← 88 language keymaps for :h mbyte-keymap
├── spell/                    ← default spell files (English UTF-8)
├── queries/                  ← tree-sitter queries per language
├── doc/                      ← 136 :help pages
├── tutor/                    ← :Tutor content (en, ja, zh)
├── scripts/                  ← misc helper scripts (mswin.vim, optwin.lua, less.vim)
├── filetype.lua              ← main filetype detection
├── example_init.lua          ← the config template shown in :h nvim
├── embedded_data.zig         ← Zig glue to embed runtime files (WIP)
└── menu.vim, delmenu.vim     ← GUI menu definitions
```

## `lua/vim/` — the Lua stdlib users actually touch

This is `:help lua-stdlib` — every `vim.*` module you require from Lua config.

### Top-level modules

| Module | What it does |
|---|---|
| `vim.lsp` | Built-in LSP client (folder: `lsp/` — client, protocol, handlers, semantic tokens, inlay hints, completion, folding, codelens, watchfiles) |
| `vim.diagnostic` | Unified diagnostic display, floats, navigation, severity (folder: `diagnostic/`) |
| `vim.treesitter` | Syntax trees, highlighters, folds, queries (folder: `treesitter/`) |
| `vim.fs` | Path manipulation — `find`, `dir`, `parents`, `normalize` |
| `vim.iter` | Lua iterator API — `vim.iter(t):map(...):filter(...):totable()` |
| `vim.pack` | Native plugin manager (v0.12) |
| `vim.net` | HTTP client — replaces the need for external `curl` wrappers |
| `vim.keymap` | Ergonomic keymap definitions (`vim.keymap.set('n', 'j', ...)`) |
| `vim.loader` | Bytecode cache for faster Lua module loading |
| `vim.health` | `:checkhealth` framework |
| `vim.snippet` | Built-in snippet expansion |
| `vim.text` | Text utilities |
| `vim.ui` | `select` and `input` — pluggable via lazy assignment |
| `vim.uri` | URI parsing (used heavily by LSP) |
| `vim.version` | Semver comparison + Nvim version info |
| `vim.hl` / `vim.re` / `vim.glob` | Highlights, LPeg regex, glob matching |
| `vim.fs` / `vim.func` / `vim.pos` / `vim.range` | Small utility surfaces |
| `vim.log` | Log-level constants (`INFO`, `WARN`, `ERROR`) |
| `vim.provider` | Python/Node/Ruby/Perl remote-plugin providers |
| `vim.secure` | `trust`-file management for exec.d-style config sourcing |
| `vim.filetype` | Filetype detection helpers, add/match callable from user config |
| `vim.tty` | Terminal capability queries |
| `vim.inspect` | Vendored kikito/inspect.lua |
| `vim.F` | Function combinators |
| `vim._meta` | LuaLS type annotations for editor tooling — do not require at runtime |

### `_`-prefixed modules are internal

`_async`, `_comment`, `_inspector`, `_watch`, `_init_packages` — implementation details. Plugins that require them will break without notice. If you need something they provide, open an issue asking for it to be promoted.

### `lua/vim/_meta/` — type definitions for tooling

Generated `.gen.lua` files (`api.gen.lua`, `options.gen.lua`, `vimfn.gen.lua`, `vvars.gen.lua`, `api_keysets.gen.lua`) plus hand-maintained meta files (`builtin.lua`, `events.lua`, `misc.lua`, `regex.lua`, `spell.lua`, `tui.lua`, `json.lua`, `mpack.lua`, `lpeg.lua`, `base64.lua`). These are consumed by `lua-language-server` for autocomplete and by users' LuaLS setups — not loaded by Nvim itself.

### `lua/nvim/` and `lua/uv/`

- `lua/nvim/` — internal Lua backing default plugins (`autoread.lua`, `dir.lua`, `matchparen.lua`, `spellfile.lua`, `tutor.lua`). Do not require from user code.
- `lua/uv/` — one file, `_meta.lua`, the LuaLS type stub for `vim.uv` (libuv bindings). The bindings themselves are compiled in from Luv.

## `plugin/` — default plugins loaded at startup

Loaded automatically before `~/.config/nvim/init.lua`. Ship-with-Nvim behaviors you probably use without realizing:

| File | What it enables |
|---|---|
| `autoread.lua` | `:h 'autoread'` — reload files changed outside Nvim |
| `dir.lua` | Directory buffer listing |
| `editorconfig.lua` | `.editorconfig` support built-in — no plugin needed |
| `gzip.vim` | Transparent gzip file editing |
| `man.lua` | `:Man` — the built-in man-page viewer |
| `matchit.vim` | Extended `%` matching for languages beyond parens |
| `matchparen.lua` | Highlight the matching bracket under the cursor |
| `net.lua` | Fetch `http(s)://` and `scp://` buffers via `vim.net` |
| `netrwPlugin.vim` | The legacy netrw file browser (Vim-inherited) |
| `osc52.lua` | OSC 52 clipboard integration (copy from remote shells) |
| `rplugin.vim` | Remote-plugin (msgpack) glue |
| `shada.lua` | ShaDa file support — `:h shada` |
| `spellfile.lua` | Auto-download spell files when a language is missing |
| `tarPlugin.vim`, `zipPlugin.vim` | Browse archive contents as directories |
| `tutor.vim` | `:Tutor` command |

Disable individually via `vim.g.loaded_<name> = 1` in `init.lua` — most respect the standard opt-out variable.

## `pack/dist/opt/` — bundled optional plugins

Not loaded until you `:packadd <name>` (or add them to `runtimepath` some other way):

- `cfilter` — filter quickfix/loclist by regex
- `justify` — text justification
- `matchit` — richer `%` behavior (also autoloaded via `plugin/matchit.vim`)
- `netrw` — the file browser (usually already active via `netrwPlugin.vim`)
- `nohlsearch` — auto-clear search highlights after cursor movement
- `nvim.difftool` — `:DiffTool` command
- `nvim.tohtml` — `:TOhtml` — export buffer as HTML with syntax colors
- `nvim.undotree` — `:UndoTree` visualization
- `swapmouse` — swap left/right mouse buttons
- `termdebug` — `:Termdebug` — gdb integration inside a terminal buffer

The three `nvim.*`-prefixed plugins are new (Lua-native) replacements for older Vim-script tools.

## Filetype system

`filetype.lua` at the runtime root is the main entry point. It reads user overrides from `vim.filetype.add(...)` and matches by extension, filename pattern, or content.

Once a filetype is set:

1. **`ftplugin/<ft>.vim`** or `.lua` — buffer-local settings (indent width, commentstring, wrap behavior). **458 files** covering just about every language you'll open.
2. **`indent/<ft>.vim`** or `.lua` — the `indentexpr` for auto-indent. **188 files**.
3. **`syntax/<ft>.vim`** — traditional regex-based highlighting. **787 files**. Superseded by tree-sitter for the languages that have queries (see below), but still the default for the long tail.
4. **`compiler/<ft>.vim`** — `:compiler <name>` sets `makeprg` and `errorformat` for that toolchain. **137 files**.

User overrides go in `~/.config/nvim/after/ftplugin/<ft>.lua`, which runs *after* the bundled ftplugin so you win.

## Tree-sitter queries

`queries/<language>/` holds `highlights.scm`, `indents.scm`, `folds.scm`, and `injections.scm` — the query files that turn a tree-sitter parse tree into highlights/folds/indents. Only a handful ship with core:

- `c`, `lua`, `markdown`, `markdown_inline`, `query`, `vim`, `vimdoc`

For other languages, you install a parser (`:TSInstall <lang>` via nvim-treesitter, or `vim.pack.add({...})` a parser package) and it drops queries into your user runtime.

## Documentation (`doc/`)

136 `.txt` files, the canonical `:help` corpus. Notable pages:

- `api.txt`, `api-ui-events.txt` — the msgpack-RPC API surface (consumed by [[client_libraries]])
- `lua.txt`, `lua-guide.txt`, `luaref.txt` — Lua stdlib + LuaJIT
- `lsp.txt`, `diagnostic.txt`, `treesitter.txt` — the big three subsystems
- `dev_*.txt` — contributor-facing (`dev_arch`, `dev_style`, `dev_test`, `dev_theme`, `dev_tools`, `dev_vimpatch`)
- `news.txt` — user-visible changes per release
- `deprecated.txt` — what's on the way out
- `faq.txt` — the classic FAQ

`:helptags ALL` builds the `tags` file that `:help <topic>` uses. HTML at [neovim.io/doc](https://neovim.io/doc/) is regenerated from these by the [[readme|neovim.github.io]] repo.

## Colors, keymap, spell, tutor

- **`colors/`** — 29 schemes. `default.vim` is the OG Vim default; `habamax`, `retrobox`, `sorbet`, `unokai`, `vague`(-ish), `wildcharm`, `catppuccin` are newer additions. `vim.lua` is the shim that maps to the Vim-style loader.
- **`keymap/`** — 88 language keymaps (Arabic, Armenian, Belarusian, Portuguese, …). Activate with `:set keymap=<name>`.
- **`spell/`** — only `en.utf-8.spl` ships. Other languages are auto-downloaded on demand via `plugin/spellfile.lua`.
- **`tutor/`** — `:Tutor` content in English, Japanese, and Chinese. `.tutor` files.

## Build glue (`gen_runtime.zig`, `embedded_data.zig`)

The runtime is normally installed to a system path, but Nvim can also **embed** the runtime into the binary (useful for portable/single-file distribution). `gen_runtime.zig` and `embedded_data.zig` are the pipeline that packs `runtime/` into the executable when building with certain Zig options — part of the ongoing Zig build path mentioned in [[core]].

## How to override any of this

Anywhere the runtime provides something, user config can shadow it:

| Override | User path |
|---|---|
| Filetype detection | `~/.config/nvim/filetype.lua` (via `vim.filetype.add`) |
| Ftplugin extension | `~/.config/nvim/after/ftplugin/<ft>.lua` |
| Syntax extension | `~/.config/nvim/after/syntax/<ft>.vim` |
| Colorscheme | `~/.config/nvim/colors/<name>.lua` |
| A default plugin | Set `vim.g.loaded_<name> = 1` in `init.lua` before startup |
| A `vim.*` module | Not supported — monkey-patch at your own risk |

The `after/` convention exists so both the bundled runtime and user config can define the same file without collision; the `after/` version runs later and layers on top.

## Related

- [[core]] — the C editor that loads this runtime
- [[readme]] — ecosystem structural map
- [[client_libraries]] — remote-plugin providers live under `vim.provider` here
- [[../learning_guide]] — using Neovim
- [[../keymaps_reference]] — my personal keymaps built on top of `vim.keymap`
