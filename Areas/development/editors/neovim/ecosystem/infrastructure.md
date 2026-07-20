---
aliases: ["Neovim infrastructure", "Neovim distribution", "neovim.io"]
tags: [neovim, ecosystem, infrastructure, distribution, reference]
---

# Infrastructure & distribution repos

The plumbing that turns a source tree into something users can install, and the plumbing that keeps the project's public artifacts durable. Five repos, all low-touch, most bot-driven — but each one owns a real piece of how Neovim reaches its users.

The [[readme|ecosystem map]] groups three of these under "Infrastructure & distribution" and two under "Core." This note treats all five together because their *function* is the same: they exist so `neovim/neovim` can stay focused on being an editor.

Companion to [[readme]] (ecosystem structural map), [[deps]] (build-time dependency plumbing — closely related and covered separately), and [[core]] (the editor itself).

## The five repos at a glance

| Repo | Role | Tempo (365d) | Notes |
|---|---|---:|---|
| neovim.github.io | The neovim.io website | 121 | Hugo static site; docs regenerated from vimdoc on every core release |
| neovim-snap | Snap package build | 427 | Auto-builds a `.snap` for six architectures; publishes to snapcraft.io |
| neovim-releases | Legacy / quirky binary hosting | 9 | Best-effort builds for old glibc, unusual platforms |
| unibilium | Terminfo parser library | 5 | Optional Nvim dep; upstream abandoned, this fork is now canonical |
| neovim-backup | Archival mirror of issues/PRs | 56 | Bot-driven backup via `github-backup` |

## `neovim.github.io` — the website

**What it is:** the source for [neovim.io](https://neovim.io) — the project's homepage, HTML-rendered docs, news, roadmap, and download instructions.

**Stack:** [Hugo](https://gohugo.io) static site.

```
neovim.github.io/
├── content/            ← markdown pages (about, charter, news, roadmap, doc/)
├── layouts/            ← Hugo templates (baseof, home, single, list, doc, news)
├── static/             ← assets (logos, screenshots, downloads)
├── gendoc.lua          ← Nvim -l script that converts vimdoc → HTML content
├── hugo.toml           ← Hugo config
└── algolia-docsearch-config.js  ← search index
```

**How the docs get there:** the 136 `.txt` files in [[runtime#Documentation `doc/`]] are the source of truth. `gendoc.lua` (a Nvim `-l` script — the site is generated *by* Nvim itself) invokes `src/gen/gen_help_html.lua` from the core repo to convert vimdoc to HTML, wraps each page in Hugo front matter (title, aliases, commit hash), and drops the output under `content/doc/user/*.html`. Hugo then applies the site templates and produces the final static output.

**Also converted:** `INSTALL.md` and `BUILD.md` from the core repo become `/doc/install/` and `/doc/build/`.

**Search:** Algolia DocSearch. The `algolia-docsearch-config.js` file is the crawler config.

**Tempo:** 121 commits in the last year, 116 distinct contributors ever. Most PRs are typo fixes, install-instruction updates, or the periodic doc regeneration after a core release.

## `neovim-snap` — the Snap package

**What it is:** the `snapcraft.yaml` recipe that builds `nvim` as a [Snap](https://snapcraft.io/nvim), Canonical's distribution-agnostic Linux package format. Six architectures: `amd64`, `i386`, `arm64`, `armhf`, `ppc64el`, `s390x`.

```
neovim-snap/
├── snap/
│   ├── snapcraft.yaml   ← the build recipe
│   └── local/            ← wrapper scripts + local overrides
├── README.md
└── LICENSE
```

**Config highlights:**

- `base: core22` — targets Ubuntu 22.04 as the runtime base.
- `confinement: classic` — Nvim needs full filesystem access to be useful, so it opts out of Snap's default sandbox.
- `adopt-info: nvim` — the version string is pulled from the built Nvim binary rather than hard-coded in the recipe.
- A `nvim-wrapper` script (in `snap/local/`) sets `HOME`, `VIM`, and related env vars before invoking the binary.

**Install (from the README):**

```bash
sudo snap install nvim --classic          # stable
sudo snap install nvim --edge --classic    # nightly
```

**Tempo:** 427 commits in the last year — most bot-driven (dep bumps, version-tracking commits). Two direct commits in the past 7 days.

## `neovim-releases` — the "unsupported" binary shelf

**What it is:** best-effort builds of Nvim for platforms the main [neovim/neovim releases](https://github.com/neovim/neovim/releases) don't cover.

**What's actually here:** just a README and a GitHub Actions workflow (`.github/workflows/release.yml`) — the artifacts live on GitHub Releases, not in the tree.

**What gets built:**

- **Linux with glibc 2.17** — the supported builds use a newer glibc, which breaks on RHEL/CentOS 7 and older systems. This repo re-builds against an ancient glibc so those users can still upgrade.
  - Formats: AppImage, `tar.gz`, `.deb`

**Framing (from the README):** *"best-effort, unsupported"* — the maintainers publish these but do not promise stability, timeliness, or bug fixes on them. If you're on a modern system, use the main repo's releases.

**Tempo:** 9 commits in the last year. This is a place where a workflow file lives, not where humans do work.

## `unibilium` — the terminfo parser fork

**What it is:** a small C library (~5000 lines) that reads ncurses-style terminfo files and interprets terminfo format strings. No dependency on curses, no globals, thread-safe.

**Why it's under `neovim/`:** the [original upstream unibilium](https://github.com/mauke/unibilium) [was abandoned](https://github.com/neovim/neovim/issues/10302). The neovim org forked it and now maintains it on the `master` branch as *the* upstream anyone should track.

**How Nvim uses it:** pinned in [[deps|cmake.deps/deps.txt]] as `UNIBILIUM_URL` at v2.1.2. Used at build time to compile a terminfo database into Nvim; you can also build Nvim *without* it via `cmake -DUSE_BUNDLED_UNIBILIUM=OFF` — Nvim has an internal fallback path.

**Age note:** the first commit is `2010-05-16`, predating Nvim (2014-01-31) by almost four years. The history came with the fork.

**Tempo:** 5 commits in the last year (the latest being a merge in June 2026 for bigger terminfo files). It's a stable parser, not a moving target.

## `neovim-backup` — the org archive

**What it is:** a bot-driven backup of issues, pull requests, and discussions across every repo in the neovim GitHub org. Not code — a snapshot of the project's *conversation history* stored outside GitHub in case GitHub loses it (or you do).

**How it works:**

- Uses [`github-backup`](https://pypi.org/project/github-backup/) (a Python tool).
- A GitHub Actions workflow (`backup.yml`) runs on a schedule, authenticated with a classic personal access token stored as `secrets.PAT`.
- Output committed to the `repositories/` directory + a `last_update` marker.

```
neovim-backup/
├── repositories/       ← per-repo JSON dumps of issues, PRs, comments
├── last_update         ← timestamp of the most recent backup run
├── README.md
└── .github/workflows/backup.yml
```

**Why it exists:** GitHub is a single point of failure for a decade of project context. If issues get lost, moved, or become inaccessible, this repo is the fallback. It is *not* a developer-facing artifact — nobody clones this to work on Nvim.

**Tempo:** 56 commits in the last year, essentially all bot commits.

## The invisible pieces (not in the org)

For completeness, some infrastructure and distribution channels are *not* first-party repos and don't appear on this list:

- **[Launchpad PPA](https://launchpad.net/~neovim-ppa/+archive/ubuntu/unstable)** — the `apt` source for Ubuntu users. Builds are driven from the [[deps|neovim/deps]] cache because the PPA sandbox has no network access.
- **Homebrew formula** — lives in [`Homebrew/homebrew-core`](https://github.com/Homebrew/homebrew-core/blob/main/Formula/n/neovim.rb), maintained by the Homebrew team.
- **Chocolatey, winget, scoop** — Windows package managers, community-maintained.
- **Arch AUR, Debian, Fedora, Alpine, void, nixpkgs, …** — every distribution has its own packager; the neovim org doesn't own any of them.
- **[repology.org tracker](https://repology.org/metapackage/neovim)** — the aggregator that answers "which distros are on which version."

## When to touch each

| I want to… | Repo |
|---|---|
| Fix a typo on neovim.io | `neovim.github.io` |
| Regenerate the HTML docs after a core release | `neovim.github.io` — run `./gendoc.lua path/to/neovim` |
| Change how the Snap is built | `neovim-snap/snap/snapcraft.yaml` |
| Add a legacy-platform build target | `neovim-releases` workflow + release process |
| Fix a terminfo parsing bug in Nvim | `unibilium` — then bump the pin in `neovim/cmake.deps/deps.txt` |
| Recover an issue GitHub lost | `neovim-backup/repositories/` |
| Update the Homebrew formula | Not here — send a PR to `Homebrew/homebrew-core` |
| Add a new distro packaging | Talk to that distro's packagers; not the org's turf |

## Caveats

- **The website's docs are a derived artifact.** Do not edit `content/doc/user/*.html` directly — regenerate from vimdoc in the core repo. Direct edits get overwritten on the next `gendoc.lua` run.
- **`neovim-releases` is "best-effort" for a reason.** Don't file bugs against it that would apply to the main releases; those go to `neovim/neovim`.
- **The Snap uses classic confinement.** That's a deliberate choice — a sandboxed editor is barely useful — but it means Snap's usual security posture doesn't apply. Users installing via Snap trust Nvim the way they'd trust any other package manager install.
- **`unibilium` is optional.** Removing it from a build doesn't break Nvim; it uses a smaller internal terminfo path instead. See `BUILD.md § Build without unibilium`.

## Related

- [[readme]] — ecosystem structural map
- [[deps]] — how `unibilium` and the Snap build consume the deps cache
- [[core]] — the editor that all of this exists to ship
- [[runtime#Documentation `doc/`]] — the source of truth that `gendoc.lua` converts to HTML
- [[activity_snapshot]] — commit tempo across all five in ecosystem context
