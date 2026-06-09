# Obsidian Vault Organization

This vault follows the [PARA method](https://fortelabs.com/blog/para/) — every note is classified by **actionability**, not by subject. Folders are ordered from most actionable (Projects) to least (Archives), with a few scaffolding folders alongside (Inbox, Daily, Templates, Meta).

For the rules Claude uses when creating, moving, or editing notes here, see [`../CLAUDE.md`](../CLAUDE.md). The decision tree, anti-patterns, and PARA workflow live there.

## Current structure

```
.
├── Projects/                 # Active, goal-bound work
│   ├── scout_sleuth/         # College Recruiting Intelligence Platform
│   ├── dotfiles/             # System configuration project
│   └── ai/                   # AI-related project work
├── Areas/                    # Ongoing responsibilities
│   ├── development/          # Engineering practice
│   │   ├── architecture/     # Patterns, hexagonal, etc.
│   │   ├── editors/          # Vim, Neovim
│   │   ├── frameworks/       # React, Next.js, HTMX
│   │   ├── languages/        # Go, Python, Rust, Lua, etc.
│   │   ├── llm/              # LLM practice (prompt engineering, training)
│   │   ├── prioritization/   # How to prioritize work
│   │   └── tools/            # Docker, Kubernetes, AWS, Argo, etc.
│   ├── scrum/                # Scrum practice
│   ├── systems/              # OS and tooling
│   │   ├── keyboard/         # Keyboard customization
│   │   ├── mac/              # macOS-specific
│   │   ├── shell/            # Zsh
│   │   ├── tmux/             # Terminal multiplexer
│   │   └── window_manager/   # Tiling window management
│   └── bible/                # Personal study
├── Resources/                # Reference material
│   ├── documentation/        # Tool docs (git, sed, vimium)
│   ├── llm/                  # LLM reference (cheatsheets, awesome lists)
│   └── references/           # AI tools, work platforms, systems thinking
├── Archives/                 # Completed or inactive — preserve, do not delete
├── Inbox/                    # Unprocessed captures
├── Daily/                    # Daily notes (YYYY-MM-DD.md)
├── Templates/                # Reusable note formats
├── Meta/                     # Vault documentation (this folder)
└── copilot-custom-prompts/   # Non-PARA: reusable AI prompt library
```

## PARA in one paragraph

**Projects** are short-term efforts driving toward a defined end state. **Areas** are the responsibilities you maintain over time with no end date. **Resources** are topics of interest you might use someday. **Archives** preserve everything that has dropped out of the first three categories. Subject-based folders (`Programming/`, `Health/`) are not used — they spread project material across the vault and obscure what's currently being worked on.

## Maintenance cadence

- **Weekly**: process `Inbox/` — route each capture into Projects, Areas, Resources, or Archives.
- **Monthly**: review `Projects/` — move completed or paused project folders to `Archives/`.
- **Quarterly**: review `Areas/` — drop any responsibility you no longer maintain a standard for; move its folder to `Archives/`.

## Filename and link conventions

- Notes: `snake_case.md`. Daily notes: `YYYY-MM-DD.md`. Per-directory index: `README.md`.
- Wikilinks: prefer bare `[[name]]`; use relative paths only when disambiguation is needed.
- Frontmatter: optional and minimal — only add it when a template calls for it.
