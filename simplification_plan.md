# Obsidian Vault Simplification Plan

## Current Issues
1. **Over-nesting**: 4-5 levels deep in some areas
2. **Duplicate structures**: Languages in both Projects and Resources
3. **Empty files**: Many 0-byte files cluttering the vault
4. **Inconsistent organization**: Mixed content types
5. **Scattered content**: College Recruiting files in wrong location

## Simplified Structure

### New Root Structure
```
.
├── Inbox/                    # Quick captures (keep as-is)
├── Projects/                 # Active projects with clear goals
│   ├── scout-sleuth/         # College Recruiting Intelligence Platform
│   ├── neovim-config/        # Neovim configuration project
│   ├── dotfiles/             # System configuration
│   └── study-roadmap/        # Learning projects
├── Areas/                    # Ongoing responsibilities (flattened)
│   ├── development/          # Programming languages, tools
│   ├── systems/              # OS, keyboard, tmux, etc.
│   └── learning/             # Educational resources
├── Resources/                # Reference materials (consolidated)
│   ├── cheatsheets/          # Quick reference guides
│   ├── tutorials/            # Step-by-step guides
│   └── documentation/        # Official docs and specs
├── Archives/                 # Completed/inactive (keep as-is)
├── Templates/                # Reusable formats (keep as-is)
└── Meta/                     # Vault documentation (keep as-is)
```

## Implementation Steps

### Step 1: Move College Recruiting to Projects
- Move all "College Recruiting -" files to `Projects/scout-sleuth/`
- This is clearly a project with goals and timeline

### Step 2: Consolidate Development Areas
- Merge `Areas/vim/`, `Areas/neovim-plugins/` into `Areas/development/editors/`
- Merge language folders into `Areas/development/languages/`
- Flatten deep nesting (max 2-3 levels)

### Step 3: Consolidate Systems
- Merge `Areas/mac/`, `Areas/keyboard/`, `Areas/tmux/` into `Areas/systems/`
- Keep related content together

### Step 4: Clean Empty Files
- Remove or consolidate 0-byte files
- Merge small related files

### Step 5: Update Links and References
- Update internal links to new structure
- Update README files

## Benefits of New Structure
1. **Shallow hierarchy**: Max 3 levels deep
2. **Logical grouping**: Related content together
3. **Clear separation**: Projects vs ongoing areas
4. **Easier navigation**: Fewer clicks to find content
5. **Better maintenance**: Less complexity to manage 