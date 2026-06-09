# Obsidian Vault — Claude Project Instructions

This repository is **Omar's primary Obsidian vault**, not a software project. It is a tree of plain markdown notes organized by the [PARA method](https://fortelabs.com/blog/para/) and tracked in git (`github.com/ocrosby/obsidian`).

## What this repo is (and isn't)

- It **is** a personal knowledge base. Files are read by Obsidian.app and by humans.
- It **is not** a software project. There is no build, no test suite, no CI, and no public API.
- Rules that target software repos do not apply here. In particular:
  - `rules/readme-standard.md` (badges, workflows, "Installation / Usage / Configuration" sections) — **do not apply** to any README in this vault. The root `README.md` is a vault overview, not a project README.
  - `rules/feature-skeptic.md` does not apply to note creation — adding a note is not adding product surface.
- A companion skill exists at `~/.claude/skills/obsidian/SKILL.md`. It documents folder layout, daily-notes workflow, templates, and search recipes. Treat it as authoritative; this file only adds repo-local context.

## PARA: the organizing model

PARA classifies every note by **actionability**, not by subject. Subject-based taxonomies ("Psychology", "Programming") are explicitly rejected — they spread project material across multiple folders and make it impossible to see what's currently being worked on.

The four categories, ordered from most to least actionable:

| Folder | Holds | Defining test |
|---|---|---|
| `Projects/` | Short-term efforts with a defined goal and end state | "Will this be done when X happens?" — if yes, it's a project |
| `Areas/` | Ongoing responsibilities Omar maintains a standard for | "Am I responsible for this indefinitely, with no end date?" — if yes, it's an area |
| `Resources/` | Topics of interest, reference material, things that might be useful later | "Am I interested in this but not actively responsible for maintaining a standard?" — if yes, it's a resource |
| `Archives/` | Inactive items from the first three categories worth keeping for reference | "Is this completed, paused, or no longer relevant?" — if yes, it's archived (do not delete) |

Two extra folders sit alongside PARA:

- `Inbox/` — unsorted captures (default home when classification is ambiguous)
- `Daily/` — daily notes (`YYYY-MM-DD.md`)
- `Meta/` — vault documentation (READMEs about the vault itself)
- `Templates/` — note templates (currently empty)
- `copilot-custom-prompts/` — non-PARA AI prompt library; do not move it under PARA

Current top-level layout (some folders may not exist on disk yet — create them only when first needed, do not bulk-bootstrap):

```
Projects/       Active, goal-bound (e.g., scout_sleuth, dotfiles, ai)
Areas/          Ongoing responsibilities (development, systems, scrum, bible)
Resources/      Reference material (documentation, llm, references)
Archives/       Completed / inactive — preserve, do not delete
Inbox/          Unprocessed captures
Daily/          Daily notes
Templates/      Note templates
Meta/           Vault docs
copilot-custom-prompts/   Non-PARA prompt library
```

## PARA classification — where does this note go?

Apply this decision tree in order. Stop at the first match.

1. **Has this work got a defined end state Omar is driving toward?**
   → `Projects/<project_name>/`. Examples: shipping a feature, writing a specific document, completing a survey. The folder name is `snake_case`, sub-notes inside it.
2. **Is this an ongoing responsibility Omar maintains a standard in?**
   → `Areas/<area>/`. Existing areas: `development/` (his work), `systems/` (his tooling), `scrum/`, `bible/`. New areas need explicit user confirmation — adding an area is structural.
3. **Is this reference material — something interesting or potentially useful, but not under active maintenance?**
   → `Resources/<topic>/`. Existing categories: `documentation/`, `llm/`, `references/`.
4. **Is the note unclassifiable right now?**
   → `Inbox/`. The Inbox is the safe default; a later review will route it.
5. **Is the note about a completed or abandoned project / dropped area / consumed resource?**
   → `Archives/`. Preserve, do not delete.

**Common ambiguities in this vault:**

- A language / framework note (`python`, `react`, `htmx`) — Omar treats `Areas/development/languages/` and `Areas/development/frameworks/` as **Areas** (his ongoing responsibility as an engineer). Do not move them to `Resources/` unless he says to.
- A cheatsheet or tutorial (`vimium_cheat_sheet`, `awesome_llm_resources`) — **Resource** (interest / reference, not a maintained responsibility).
- A note about how he runs sprints / scrum — **Area** (`Areas/scrum/`); it's a process he maintains.
- A one-off plan for a specific release email — **Project** (`Projects/`), not an Area; once shipped it should move to `Archives/`.

When the answer isn't obvious, **ask** — don't guess and move existing material around.

## PARA workflow — moving notes between categories

PARA assumes notes flow over time. The expected transitions:

- **Project completes or is abandoned** → move the whole project folder to `Archives/<YYYY>-<project_name>/`. Do not delete.
- **Area is dropped** (Omar no longer maintains that standard) → move to `Archives/`.
- **Resource consumed and no longer useful** → move to `Archives/` (or delete only if explicitly told).
- **Archived item becomes relevant again** → move it back to `Projects/`, `Areas/`, or `Resources/` as appropriate.
- **Inbox capture gets classified** → move from `Inbox/` to its real home.

Do not perform these moves proactively. Surface candidates ("this looks done; should it move to Archives?") and let Omar decide.

## Anti-patterns to avoid

These violate PARA and create the drift the method exists to prevent:

- **Subject-based folders under the root.** Do not create `Programming/`, `Health/`, or `Knowledge/` as top-level folders. PARA is the only top-level taxonomy.
- **Splitting one project across categories.** All material for a single project lives inside its `Projects/<project_name>/` folder — notes, drafts, reference scraps used for that project, attachments. Do not scatter project material into `Resources/`.
- **Deep nesting "just in case."** Do not pre-create empty category trees. Create folders only when a note needs them.
- **Treating Areas as broad subject buckets.** "Hiring" or "Engineering" as a single Area hides the real workload. Break ongoing responsibilities into the projects they generate; the Area folder collects the standing reference material that outlives any one project.
- **Discarding instead of archiving.** Default to archiving completed projects rather than deleting — old material informs future decisions.

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
- When creating a new note, follow the conventions above and pick the PARA folder using the decision tree above. Ask if unsure.

## Git discipline

Standard Conventional Commits. Suggested types for this repo:

- `docs(<area>):` — new or substantive edits to notes (`docs(daily): 2026-06-09 capture`, `docs(scout_sleuth): refine project plan`)
- `chore(vault):` — structural changes (renames, moves, gitignore, attachments cleanup, PARA reclassification)
- `fix(links):` — broken wikilink repairs
- `feat(template):` — adding a new template

Group related note edits into one logical commit rather than one commit per file.

Do not commit on the user's behalf unless explicitly asked. Surface the diff and let the user drive the commit.

## Known inconsistencies (do not fix in passing)

These are documented and tracked separately — fix only on explicit request:

- Documented structure in `README.md` and `Meta/README.md` references folders that don't exist on disk (`Inbox/`, `Archives/`, `Templates/`, `Resources/{cheatsheets,tutorials,notes}/`) and uses hyphenated names where disk uses underscores (`scout-sleuth`, `window-manager`).
- 8 PascalCase note filenames (`Intern.md`, `Hostname.md`, `MiniKube.md`, several under `Areas/development/editors/neovim/Keymaps/`).
- 3 Title-Case subdirectories under `Areas/development/editors/neovim/` (`Keymaps/`, `Plenary/`, `Plugins/`) — the only Title-Case dirs in the entire `Areas/` tree.
- Two `Pasted image *.png` files at vault root referenced from `Areas/development/architecture/hexagonal/{adapters,application}.md` — works today via global resolution, fragile to attachment cleanup.
- Several PARA borderline cases: loose files at `Areas/` root (`agents.md`, `ollama.md`, `pathway.md`, `pyoutulan.md`, `vercel.md`) are unclassified — they may belong under `Areas/development/`, in `Resources/`, or in `Inbox/`. Surface but do not move.

## When in doubt

- Confirm before renaming or moving any existing note — wikilinks across the vault may depend on the current name.
- Confirm before deleting a note, even a tiny stub — empty stubs are often deliberate placeholders for outlined-but-unwritten topics.
- Confirm before moving a note between PARA categories — classification is a judgment call only Omar can make.
- Confirm before committing.
