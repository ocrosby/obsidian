---
aliases: ["Neovim Ecosystem", "Neovim source workspace"]
---

# Neovim Ecosystem

Notes and research about the **Neovim source and ecosystem** — the C core, Lua runtime, official client libraries, and first-party plugins from the [neovim GitHub org](https://github.com/neovim).

This is distinct from the parent [[../readme]], which is about *using* Neovim (config, keymaps, plugin workflows). Notes here are about *how it's built and how the pieces relate*.

## Local workspace

All non-archived repos in the neovim org are cloned locally at:

```
~/src/github.com/neovim/
```

That directory is **not** a git repo; each subfolder is. The workspace has its own `CLAUDE.md` at the top level orienting future sessions.

## Repository map

### Core
- **neovim** — the editor. C core + Lua runtime.
- **deps** — third-party C dependencies built for Nvim CI (libuv, luajit, msgpack, treesitter).
- **neovim-releases** — legacy/unsupported release binaries.
- **neovim-backup** — archival mirror of issues, comments, discussions.

### Client libraries & plugin hosts
Each drives Nvim over msgpack-RPC and hosts remote plugins in that language:

| Repo | Language |
|---|---|
| pynvim | Python |
| node-client | Node.js |
| go-client | Go |
| neovim-ruby | Ruby |
| nvim.net | .NET |

### Editor integrations
- **VSNvim** — Nvim inside Visual Studio 2017.

### First-party plugins & specs
- **nvim-lspconfig** — quickstart LSP server configs (Lua).
- **nvimdev.nvim** — plugin for people hacking on Neovim itself.
- **tree-sitter-vimdoc** — tree-sitter grammar for `:help` files.
- **packspec** — ALPHA spec for declaring plugin/package dependencies.

### Infrastructure & distribution
- **unibilium** — terminfo parser library ([third-party dep](https://github.com/neovim/neovim/blob/master/MAINTAIN.md#third-party-dependencies)).
- **neovim-snap** — Snap package build config.
- **neovim.github.io** — the neovim.io website.

## Vendored (not separate repos)

These *used to* be standalone repos in the org but now live inside `neovim/` and are the source of truth:

- `neovim/src/nvim/vterm/` ← was `libvterm`
- `neovim/src/nvim/tui/termkey/` ← was `libtermkey`

## Cross-repo dependencies

- Every client library speaks msgpack-RPC to nvim; the protocol contract lives in `neovim/src/nvim/api/` with generated `api_metadata`. API changes ripple to all clients.
- `deps/` is what nvim's CMake pulls when building from source.
- `nvim-lspconfig` and `nvimdev.nvim` consume the Lua API in `neovim/runtime/lua/vim/`.

## Related notes in this vault

- [[../readme]] — using Neovim (parent folder)
- [[../learning_guide]] — the learning path
- [[../keymaps_reference]] — my keymap conventions
- [[../Plugins/readme]] — plugin usage notes
- [[../../languages/lua/readme]] — Lua, the runtime language of nvim's config and much of its runtime

## What goes here

Add notes to this folder when the topic is:

- How a piece of nvim's C or Lua code works
- Reading/writing an official client library
- Cross-repo behavior (e.g. how a client feature maps to an api function)
- Ecosystem history, deprecations, or repo-level decisions
- Build system, deps, or release plumbing

For plugin *usage* or personal config, keep it in the parent folder next to `learning_guide.md`.
