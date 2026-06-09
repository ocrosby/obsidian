# Obsidian Vault — Claude Project Instructions

This repository is **Omar's primary Obsidian vault**, not a software project. It is a tree of plain markdown notes organized by the [PARA method](https://fortelabs.com/blog/para/) and tracked in git (`github.com/ocrosby/obsidian`).

## What this repo is (and isn't)

- It **is** a personal knowledge base. Files are read by Obsidian.app and by humans.
- It **is not** a software project. There is no build, no test suite, no CI, and no public API.
- Rules that target software repos do not apply here. In particular:
  - `rules/readme-standard.md` (badges, workflows, "Installation / Usage / Configuration" sections) — **do not apply** to any README in this vault. The root `README.md` is a vault overview, not a project README.
  - `rules/feature-skeptic.md` does not apply to note creation — adding a note is not adding product surface.
- A companion skill exists at `~/.claude/skills/obsidian/SKILL.md`. It documents folder layout, daily-notes workflow, templates, and search recipes. Treat it as authoritative; this file only adds repo-local context.

## Folder layout (PARA)

Top-level folders use Title Case. Everything under them uses `snake_case`.

```
Inbox/           # Unprocessed quick captures (may not exist yet — create on demand)
Daily/           # Daily notes: YYYY-MM-DD.md (may not exist yet)
Projects/        # Active projects (e.g., scout_sleuth, dotfiles, ai)
Areas/           # Ongoing responsibilities (development, systems, scrum, bible)
Resources/       # Reference material (documentation, llm, references)
Archives/        # Completed / inactive (may not exist yet)
Templates/       # Reusable note formats (empty until adopted)
Meta/            # Vault documentation
copilot-custom-prompts/   # Reusable AI prompt library — exempt from PARA
```

If a note has no clear home, put it in `Inbox/` and let a later review move it. Never invent a new top-level folder without asking.

## Note conventions

- **Filenames**: `snake_case.md`. Daily notes use `YYYY-MM-DD.md`. Do not introduce Title Case, PascalCase, kebab-case, or date-prefixed filenames. Several legacy files violate this — leave them unless explicitly asked to rename.
- **README per directory**: `README.md` (uppercase). A handful of legacy `readme.md` and `index.md` files exist; do not match those when creating new ones.
- **Wikilinks**: prefer bare `[[name]]` — Obsidian resolves case-insensitively across the whole vault. Relative-path links (`[[../readme]]`, `[[macros/applying_macros]]`) are also common and acceptable when disambiguation is needed. Do not "normalize" an existing link's style.
- **Markdown links**: not used for internal references. Use wikilinks.
- **Frontmatter**: most notes have none. When editing a note that has frontmatter, preserve it exactly. Do not add frontmatter to notes that lack it.
- **Attachments**: pasted images live at the vault root today (e.g., `Pasted image 20250619054930.png`). There is no `_attachments/` convention yet — do not invent one without asking.

## Editing rules

- Preserve existing wikilink style as written.
- Preserve frontmatter exactly (do not reorder, recase, or add fields).
- Do not silently fix typos or rephrase prose in notes you're not editing.
- When creating a new note, follow the conventions above and pick the PARA folder by intent — ask if unsure.

## Git discipline

Standard Conventional Commits. Suggested types for this repo:

- `docs(<area>):` — new or substantive edits to notes (`docs(daily): 2026-06-09 capture`, `docs(scout_sleuth): refine project plan`)
- `chore(vault):` — structural changes (renames, moves, gitignore, attachments cleanup)
- `fix(links):` — broken wikilink repairs
- `feat(template):` — adding a new template

Group related note edits into one logical commit rather than one commit per file.

Do not commit on the user's behalf unless explicitly asked. Surface the diff and let the user drive the commit.

## Known inconsistencies (do not fix in passing)

These are documented and tracked separately — fix only on explicit request:

- Documented structure in `README.md` and `Meta/README.md` references folders that don't exist on disk (`Inbox/`, `Archives/`, `Templates/`, `Resources/{cheatsheets,tutorials,notes}/`) and uses hyphenated names where disk uses underscores (`scout-sleuth`, `window-manager`).
- 8 PascalCase note filenames (`Intern.md`, `Hostname.md`, `MiniKube.md`, several under `Areas/development/editors/neovim/Keymaps/`).
- 3 Title-Case subdirectories under `Areas/development/editors/neovim/` (`Keymaps/`, `Plenary/`, `Plugins/`) — the only Title-Case dirs in the entire `Areas/` tree.
- ~23 remaining broken wikilinks: stub TODOs in `fastapi/README.md`, `zsh/README.md`, `tmux/quick_reference.md`, plus cross-vault references to `~/src/github.com/ocrosby/notes`.
- Two `Pasted image *.png` files at vault root referenced from `Areas/development/architecture/hexagonal/{adapters,application}.md` — works today via global resolution, fragile to attachment cleanup.

## When in doubt

- Confirm before renaming or moving any existing note — wikilinks across the vault may depend on the current name.
- Confirm before deleting a note, even a tiny stub — empty stubs are often deliberate placeholders for outlined-but-unwritten topics.
- Confirm before committing.
