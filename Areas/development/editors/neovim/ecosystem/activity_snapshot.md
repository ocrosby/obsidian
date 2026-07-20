---
aliases: ["Neovim ecosystem activity", "Neovim repo pulse"]
tags: [neovim, ecosystem, reference]
---

# Neovim ecosystem activity snapshot

A per-repo pulse across the 17 non-archived clones under `~/src/github.com/neovim/` — companion to the structural map in [[readme]] (which explains *what* each repo is; this note tracks *how alive* each is).

## Provenance

- Source: local clones at `~/src/github.com/neovim/*`
- Captured: **2026-07-20**
- Metrics: `git log --all` per repo — total commits, distinct author emails, first/last commit dates, and commit windows (7d / 30d / 365d).
- Regenerate: the shell one-liner used to gather this data is in the session that produced the note; rerun over `~/src/github.com/neovim/*/` and sort by column 2.

## The whole ecosystem, at a glance

| Repo | Language | Total commits | Distinct authors | First commit | Last commit | 7d | 30d | 365d |
|---|---|---:|---:|---|---|---:|---:|---:|
| neovim | C + Lua | 40,005 | 1,533 | 2014-01-31 | 2026-07-20 | 111 | 443 | 4,733 |
| nvim-lspconfig | Lua | 4,384 | 1,102 | 2019-11-13 | 2026-07-17 | 7 | 30 | 648 |
| neovim-ruby | Ruby | 961 | 8 | 2014-05-12 | 2026-06-29 | 0 | 0 | 3 |
| neovim.github.io | JS/HTML/CSS | 825 | 116 | 2014-02-21 | 2026-07-13 | 0 | 7 | 121 |
| neovim-snap | Snap YAML | 814 | 12 | 2020-09-04 | 2026-07-15 | 7 | 33 | 427 |
| node-client | TypeScript | 743 | 40 | 2015-05-30 | 2026-07-14 | 1 | 1 | 59 |
| pynvim | Python | 673 | 70 | 2014-05-07 | 2026-04-10 | 0 | 0 | 21 |
| go-client | Go | 455 | 19 | 2015-08-25 | 2025-03-29 | 0 | 0 | 0 |
| deps | C build recipes | 408 | 12 | 2014-04-16 | 2026-07-20 | 2 | 7 | 52 |
| neovim-backup | archival | 291 | 6 | 2023-07-22 | 2026-07-20 | 1 | 4 | 56 |
| unibilium | C | 161 | 17 | 2010-05-16 | 2026-06-30 | 0 | 5 | 5 |
| tree-sitter-vimdoc | JS grammar + C | 161 | 11 | 2021-08-13 | 2026-06-22 | 0 | 0 | 20 |
| nvim.net | C# | 87 | 6 | 2018-05-14 | 2026-07-18 | 3 | 3 | 21 |
| nvimdev.nvim | Lua | 85 | 9 | 2017-01-28 | 2026-01-21 | 0 | 0 | 0 |
| packspec | spec doc | 72 | 9 | 2022-02-06 | 2026-05-10 | 0 | 0 | 0 |
| neovim-releases | binary hosting | 49 | 6 | 2023-09-08 | 2026-05-18 | 0 | 0 | 9 |
| VSNvim | C# | 20 | 1 | 2018-07-30 | 2018-09-04 | 0 | 0 | 0 |

## What the numbers reveal

### One repo dominates

`neovim/neovim` is 40k of ~50k total commits (~80%). Everything else combined — every client, plugin, website, and dep — is smaller than the core repo by an order of magnitude. When someone says "contributing to Neovim," they almost always mean `neovim/neovim`; the ecosystem repos are essentially side gardens for specific integrations.

### Two active codebases, everything else on maintenance

Sorted by the last 365 days:

- **Very active (>100 commits/year):** `neovim` (4,733), `nvim-lspconfig` (648), `neovim-snap` (427), `neovim.github.io` (121)
- **Actively maintained (10–100/year):** `node-client`, `deps`, `neovim-backup`, `pynvim`, `nvim.net`, `tree-sitter-vimdoc`
- **Dormant (<10/year):** `go-client`, `neovim-ruby`, `nvimdev.nvim`, `packspec`, `unibilium`, `neovim-releases`
- **Abandoned:** `VSNvim` — last commit **2018-09-04**, single contributor. Effectively a museum piece for Visual Studio 2017 integration.

### The client-library divide

The five official RPC clients are on radically different heartbeats:

| Client | Language | Last commit | 365d commits | Reading |
|---|---|---|---:|---|
| node-client | TypeScript | 2026-07-14 | 59 | Alive, low-tempo maintenance |
| pynvim | Python | 2026-04-10 | 21 | Slow but breathing |
| nvim.net | C# | 2026-07-18 | 21 | Solo maintainer, but recent |
| go-client | Go | **2025-03-29** | **0** | ~16 months idle — treat as unmaintained |
| neovim-ruby | Ruby | 2026-06-29 | 3 | Long-tail bugfix only |

If you're picking a language to script Neovim from **new** work, node-client and pynvim are the safe picks; the others carry risk that a protocol change won't get a timely client update.

### First-party plugins

Only `nvim-lspconfig` is on a real development pace (648 commits/365d, ~1,100 contributors). `nvimdev.nvim` (85 commits, dormant) and `packspec` (72 commits, on hold — the spec discussion cooled) are best treated as reference points rather than living projects.

### Contributor concentration

`neovim/neovim` has 1,533 distinct author emails; `nvim-lspconfig` has 1,102. Every other repo is in the low double digits or less. The ecosystem is two crowd-sourced repos and a handful of small-team ones — most repos have fewer contributors than a typical company team.

See [[contributors]] for the full ranking of who those 1,533 core-repo authors are.

### Age

The oldest commit anywhere in the ecosystem is `unibilium` at **2010-05-16** — predating Neovim itself (Neovim's first commit is 2014-01-31). `unibilium` was adopted from Rich Felker's original terminfo work and vendored later; the history came with it.

## What this note is not

- **Not a leaderboard.** Commit count says nothing about code quality or the size of individual changes.
- **Not a health score.** A dormant repo can be dormant because it's done, not because it's dying (see: `unibilium`, which is a stable terminfo parser that doesn't need weekly changes).
- **Not a snapshot of GitHub metadata.** Stars, issue counts, PR velocity — all of that lives on GitHub, not in the local clones. Fetch via `gh` if you need it.

## Related

- [[readme]] — structural map of the ecosystem (what each repo is, cross-repo dependencies)
- [[contributors]] — full ranking of the 1,568 `neovim/neovim` author identities by commit count
- [[../readme]] — parent Neovim area (config, keymaps, usage)
