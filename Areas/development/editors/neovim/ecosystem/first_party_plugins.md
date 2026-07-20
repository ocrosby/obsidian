---
aliases: ["Neovim first-party plugins", "neovim org plugins"]
tags: [neovim, ecosystem, plugins, reference]
---

# First-party Neovim plugins

The plugins that live under [github.com/neovim](https://github.com/neovim/) rather than in a community org. Four repos, three distinct audiences, one hotly-active flagship and three specialist tools.

"First-party" in this note means **shipped from the neovim org as separate repos** — distinct from the *bundled* plugins under [[runtime|neovim/runtime/plugin]] and `runtime/pack/dist/opt/`, which are baked into `$VIMRUNTIME` and covered in [[runtime#`plugin/` — default plugins loaded at startup]].

Companion to [[readme]] (ecosystem structural map), [[activity_snapshot]] (tempo per repo), and [[core]] (the editor these plug into).

## The four repos at a glance

| Repo | Purpose | Language | Version-ish | Last commit | 365d commits | Verdict |
|---|---|---|---|---|---:|---|
| nvim-lspconfig | LSP server configs (407 servers) | Lua | rolling | 2026-07-17 | 648 | Actively developed flagship |
| tree-sitter-vimdoc | tree-sitter grammar for `:help` files | JS grammar + generated C | v4.1.0 | 2026-06-22 | 20 | Stable, low-tempo |
| nvimdev.nvim | Devtools for hacking on Neovim itself | Lua + Vim script | rolling | 2026-01-21 | 0 | Dormant but functional |
| packspec | Spec for cross-tool plugin manifests | JSON schema + Lua parser | ALPHA | 2026-05-10 | 0 | Design frozen; ecosystem uptake TBD |

## `nvim-lspconfig` — the LSP server config catalog

The most-used first-party plugin by a wide margin. If you have LSP working in Nvim, this is likely why.

**What it is:** a directory of ~407 server configurations, one per language server (rust-analyzer, gopls, pyright, tsserver, clangd, lua_ls, …). Each config knows the server's binary name, default filetypes, root-directory pattern, and any language-specific settings.

**Two eras:**

- **New (Nvim 0.11+):** configs live under `lsp/` and are auto-discovered by the built-in `vim.lsp.config` API. You never `require('lspconfig')`; you `vim.lsp.enable('rust_analyzer')` and the config in `lsp/rust_analyzer.lua` merges with any of your local overrides.
- **Legacy (Nvim ≤0.10):** configs lived under `lua/lspconfig/configs/` (~360 files still there for back-compat). Used via `require('lspconfig').rust_analyzer.setup{...}`. **Deprecated**; will warn today, error later, then be removed.

The maintainers are actively winding down the legacy framework — `require('lspconfig')` will be dropped. The catalog itself (the per-server configs) is *not* deprecated.

**Tempo:** 648 commits in the last 365 days, 1,102 distinct authors ever. This is the community's default entry point for adding new LSP support; PRs flow in constantly.

**Migrate to today:**

```lua
vim.lsp.config('rust_analyzer', {
  settings = { ['rust-analyzer'] = { cargo = { features = 'all' } } },
})
vim.lsp.enable('rust_analyzer')
```

Do *not* write new code that uses `require('lspconfig').xxx.setup{}`.

## `tree-sitter-vimdoc` — grammar for `:help` files

**What it is:** a tree-sitter grammar for vimdoc syntax — the format used by every `.txt` file under [[runtime#Documentation `doc/`]]. The grammar is pinned by [[deps|cmake.deps/deps.txt]] as `TREESITTER_VIMDOC_URL` at v4.1.0, so every Nvim build ships with the same parser version.

**Why it matters:**

- Powers syntax highlighting for `:help` pages inside Nvim (via the `vimdoc` parser + queries in `runtime/queries/vimdoc/`).
- Powers **HTML rendering** of the docs at [neovim.io/doc](https://neovim.io/doc/) — the [[readme|neovim.github.io]] site consumes the parse tree to emit HTML.
- Predictable *output* is the primary goal, per the README. The grammar is willing to reject weird vimdoc rather than accept it and produce bad HTML.

**Bindings:** ships bindings for C, Go, Node, Python, Rust, and Swift under `bindings/` — the tree-sitter cross-language convention, though the Neovim consumption path is only the generated C.

**Related maintenance:** if you change vimdoc conventions (a new heading style, a new tag syntax), the change flows: docs first → grammar update here → cut a release → bump the pin in `neovim/cmake.deps/deps.txt` → next Nvim release picks it up.

**Tempo:** 20 commits in the last year. Stable — this is a grammar, not a moving target.

## `nvimdev.nvim` — devtools for hacking on Neovim itself

**What it is:** a small Lua/Vim plugin that makes editing the [[core|neovim/neovim]] source tree less painful:

- Auto-detects when you're inside a Neovim source tree and `:cd`s to the root.
- Fast C linting via `clint.py` + `uncrustify`, wired into `vim.diagnostic` so you get inline squigglies on style violations.
- Filetype settings tuned for Neovim's C style (tab width, cinoptions, comment style).
- [vim-projectionist] hooks: alternate-file jumping between an nvim source file and its `.vim-src/` sibling (the pre-fork Vim version), plus a "diff against Vim" command.
- `:NvimTestRun` and `:NvimTestClear` — run a functional test straight from the buffer.

**Audience:** Nvim core contributors specifically. If you're not editing `neovim/neovim` itself, you don't need this.

**Tempo:** zero commits in the last year. Not abandoned in intent — the tool's scope is small and stable — but be aware it's not actively developed.

## `packspec` — a plugin-manifest spec

**What it is:** a proposal for a **cross-tool JSON manifest** (`pkg.json`) that plugins can ship to declare their dependencies. Modeled loosely on npm's `package.json`, but *very* stripped down.

**Shape of the manifest** (per the README):

```json
{
  "name": "lspconfig",
  "description": "Quickstart configurations for the Nvim-lsp client",
  "engines": { "nvim": "^0.10.0", "vim": "^9.1.0" },
  "dependencies": [
    { "url": "https://github.com/nvim-lua/plenary.nvim", "rev": "^v0.2.0" }
  ]
}
```

The idea: any plugin manager (packer, lazy, mini.deps, [[runtime|vim.pack]], vim-plug, Vundle, …) reads `pkg.json` to resolve deps. No plugin-manager-specific declaration format.

**What's actually in the repo:**

- `spec/spec.md` and `spec/client-spec.md` — the human-readable specification.
- `schema/packspec_schema.json` — JSON Schema for validating a `pkg.json`.
- `examples/packspec.1.json`, `examples/packspec.1.lua` — worked examples.
- `lua/` — a reference parser + `docs/` — background docs.
- `Makefile`, `scripts/` — spec generation and validation tooling.

**Status:** README labels it *"a wild-west \"package\" format"* — it's still marked **ALPHA**. Zero commits in the last year. The design conversation has cooled; ecosystem uptake is limited (some plugins ship one, most don't; not every plugin manager reads it). Treat it as a proposal you can *emit* if you author a plugin, but don't rely on the ecosystem *consuming* it.

**Related:** Nvim 0.12's built-in `vim.pack` is the closest thing to an official plugin manager, but it does not currently consume `pkg.json` for dep resolution. If packspec picks up steam again, that's the integration point.

## What's *not* in this note

- **Bundled runtime plugins** (`gzip`, `matchparen`, `netrw`, `editorconfig`, `osc52`, the ten `pack/dist/opt/*`) — those live inside [[core|neovim/neovim]] itself and are covered in [[runtime]].
- **[nvim-treesitter](https://github.com/nvim-treesitter/nvim-treesitter)** — a *community* project (separate GitHub org). Not first-party. The parsers Nvim actually ships with are pinned in [[deps|cmake.deps]]; nvim-treesitter is the community catalog for everything else.
- **Client-library remote-plugin hosts** (pynvim, node-client, …) — those *host* plugins in their language but the clients themselves are covered in [[client_libraries]].

## When to reach for which

| I want to… | Use |
|---|---|
| Add LSP support for a language | `nvim-lspconfig` — check if `lsp/<server>.lua` exists; if yes, `vim.lsp.enable`; if no, write one and PR it |
| Contribute to Nvim's C sources | `nvimdev.nvim` for the linting/`:cd`/test-runner ergonomics |
| Change `:help` file syntax rules | `tree-sitter-vimdoc` grammar + queries in `runtime/queries/vimdoc/` |
| Publish a plugin with declared deps | Ship a `pkg.json` per `packspec` — but be aware most managers still don't read it |

## Related

- [[readme]] — ecosystem structural map
- [[runtime]] — bundled (not first-party-repo) plugins under `$VIMRUNTIME/plugin/` and `pack/dist/opt/`
- [[core]] — the editor these plugins target
- [[deps]] — where `tree-sitter-vimdoc` is pinned into Nvim's build
- [[activity_snapshot]] — commit tempo comparison across all four
- [[../plugins/readme]] — my personal plugin notes (usage, not authoring)
