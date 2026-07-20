---
aliases: ["Neovim editor integrations", "Neovim inside other IDEs", "VSNvim"]
tags: [neovim, ecosystem, integrations, reference]
---

# Editor integrations

Projects that embed Neovim inside *another* editor or IDE so you get Nvim's modal editing and Lua stdlib without leaving the host. The neovim org ships exactly **one** of these — [[#VSNvim]] — and it's a museum piece. Everything real happens in the community.

Companion to [[readme]] (ecosystem structural map) and [[client_libraries]] (the RPC clients that these integrations are built on top of).

## The one first-party integration

### VSNvim

**What it is:** an embedding of Nvim inside **Visual Studio 2017** (the Windows IDE, not VS Code). Written in C++ against the Visual Studio SDK. Talks to a running Nvim process, forwards keys, syncs text and selection state.

**Current state — abandoned.**

- **First commit:** 2018-07-30
- **Last commit:** 2018-09-04
- **Contributors:** 1 (`b-r-o-c-k`)
- **Total commits:** 20
- **Windows of active development:** roughly five weeks in mid-2018

The README still lists a long "not yet integrated" section (highlights, completion menus, command-line output, opening buffers, line numbers, status lines, window layout) — most of what an editor actually needs. Development stopped before any of it was addressed.

**The uglier catch:** the build instructions require a **fork of Neovim** at `b-r-o-c-k/neovim` on the `vsnvim` branch. That fork was never upstreamed. So even if you wanted to revive the project, step 1 is either resurrecting a private branch from 2018 or porting its changes onto modern Nvim yourself.

**Verdict:** treat VSNvim as a historical artifact. It documents what was attempted, not something you should install today.

## Why the neovim org doesn't ship more

Nvim was designed around the assumption that **the editor lives behind an msgpack-RPC API**, and *anyone* can build a UI or embedding on top of it. That means:

- The team doesn't need to maintain integrations — the API is the integration contract.
- Community projects can iterate faster than a first-party effort would (which VSNvim's five-week lifetime illustrates in reverse).
- Splitting effort across N editor integrations dilutes core work.

`:h ui` and `:h api` document the surface that every integration talks to. `nvim --embed` (Nvim as a subprocess with stdio RPC) is what most integrations spawn.

## Community integrations worth knowing about

Not in the neovim org, not "official," but this is where the real work happens. Listed for orientation, not endorsement — check each project's own health before adopting.

### VS Code

- **[vscode-neovim](https://github.com/vscode-neovim/vscode-neovim)** — the de facto Nvim-in-VSCode integration. Runs a real Nvim process, forwards keys and buffer state, lets VSCode own the UI (completions, LSP, git blame) while Nvim owns the *editing* (motions, macros, Ex commands, plugins).

The design point matters: **Nvim owns editing, VSCode owns everything else**. Highlights, minimap, source control, extensions — all still VSCode. You use Nvim for what Nvim is good at, and VSCode for the rest.

### JetBrains IDEs (IntelliJ, PyCharm, GoLand, Rider, WebStorm, …)

- **[IdeaVim](https://github.com/JetBrains/ideavim)** — JetBrains' own Vim *emulation*. **Not Neovim.** It reimplements the Vim command set in Kotlin. No `.nvimrc`, no Lua plugins, no `:help` integration.
- **[ideavim-neovim / nvim-as-ideavim experiments](https://github.com/JetBrains/ideavim/issues)** — periodic attempts to bolt real Nvim onto IdeaVim have never landed as a supported product.

If you want *real* Nvim inside a JetBrains IDE, the honest answer today is: run Nvim in a terminal buffer inside the IDE, or use IdeaVim and accept the emulation tradeoff.

### Emacs

- **[evil-mode](https://github.com/emacs-evil/evil)** — like IdeaVim, an *emulation* of Vim's keybindings and modal model inside Emacs. Not Nvim.
- No serious attempt at embedding real Nvim inside Emacs exists. The overlap in audience is thin — Emacs users who want Nvim tend to just use Nvim.

### Sublime Text

- **[NeoVintageous](https://github.com/NeoVintageous/NeoVintageous)** — emulation, not embedding.

### Browsers

- **[firenvim](https://github.com/glacambre/firenvim)** — embeds a real Nvim process inside any HTML `<textarea>` in Firefox or Chrome. Talks to Nvim over stdio via a browser-extension native messaging host. Community project, active.

### Standalone GUIs (Nvim *is* the editor, another program is just the shell)

Not strictly "editor integrations" — the "host" here is just a window and a font renderer — but conceptually adjacent:

- **[Neovide](https://github.com/neovide/neovide)** — Rust-based, GPU-accelerated Nvim GUI. Cursor animations, smooth scrolling, WSL support. The most popular Nvim GUI today.
- **[goneovim](https://github.com/akiyosi/goneovim)** — Go/Qt Nvim GUI.
- **[NyaoVim](https://github.com/rhysd/NyaoVim)** — Electron-based.
- **[fvim](https://github.com/yatli/fvim)** — F#/.NET GUI.

Each of these speaks the `nvim --embed` UI protocol (`:h ui-protocol`) and provides its own rendering. Choose based on your OS and how much bling you want.

## What "integration" actually means, on the wire

Every integration in this note — first-party or community — does one of two things:

1. **Spawn Nvim as a child process** (`nvim --embed`), speak msgpack-RPC over stdio, and render the UI events (`grid_line`, `grid_scroll`, `mode_change`, …). This is how the standalone GUIs and firenvim work.
2. **Attach to a running Nvim** via a Unix socket, TCP, or named pipe (`nvim --listen /tmp/nvim.sock`), and use it as a remote editing engine. This is how vscode-neovim works — the host UI already exists; Nvim is a headless brain.

Both paths use the same [[client_libraries|RPC surface]]. Which client library (if any) you use to build the integration depends on the host's language ecosystem — vscode-neovim uses the Node client, Neovide talks the wire protocol directly from Rust.

## When to pick which

| I want to… | Choice |
|---|---|
| Nvim editing inside VS Code | vscode-neovim (community) |
| Nvim editing inside Visual Studio (Windows) | Realistically, nothing. VSNvim is dead. Consider WSL + terminal Nvim. |
| Nvim editing inside a JetBrains IDE | IdeaVim (emulation, not real Nvim) |
| Nvim inside `<textarea>` in a browser | firenvim |
| A fancy standalone Nvim GUI | Neovide is the default recommendation in 2026 |
| Build my own integration | `nvim --embed`, pick a client from [[client_libraries]], read `:h ui-protocol` |

## Caveats

- **"Emulation" vs "embedding" is a real distinction.** IdeaVim, evil-mode, NeoVintageous are *reimplementations* of Vim's modal model — they don't run Nvim, so `.nvimrc`, Lua plugins, `:help`, LSP config, etc. do not apply. If you have a portable Nvim config you want to carry across editors, only *embedding* integrations preserve it.
- **VSNvim is not a maintained project.** Do not include it in "here are the ways to use Nvim in Visual Studio" — the honest answer is *there aren't good ones today*.
- **Community project health varies.** Neovide and vscode-neovim have real maintainers and issue triage; smaller GUIs may be single-person efforts. Check the last-commit date before adopting.

## Related

- [[readme]] — ecosystem structural map (lists VSNvim under "Editor integrations")
- [[client_libraries]] — the RPC clients most integrations build on top of
- [[core]] — the API and UI protocol these integrations consume
- [[activity_snapshot]] — VSNvim's tempo (dead since 2018) in context of the rest of the ecosystem
