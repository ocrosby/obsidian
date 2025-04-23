

This note documents custom Neovim keymaps organized by category. Leader key is set to `<Space>`, local leader to `\`.

---

## 🔀 Buffer Management

- `<leader>n` — Next buffer
- `<leader>p` — Previous buffer
- `<leader>x` — Close buffer
- `<Tab>` — Next buffer
- `<S-Tab>` — Previous buffer
- `<leader>bd` — Delete current buffer
- `<leader>ba` — Close all buffers except current

---

## 📁 NvimTree

- `<leader>e` — Toggle focus between NvimTree and editor
- `<leader>nf` — Create new file at NvimTree location
- `<leader>th` — Toggle hidden files in NvimTree

---

## 🪄 Visual Mode Enhancements

- `J` (visual) — Move selected lines down
- `K` (visual) — Move selected lines up

---

## 🧪 Testing

- `<leader>tf` — Run Plenary test file
- `<leader>tt` — Run nearest test (Neotest)
- `<leader>tT` — Run current test file
- `<leader>ts` — Toggle test summary
- `<leader>to` — Show test output
- `<leader>tw` — Toggle test watch

---

## 📜 Scrolling and Joining

- `J` — Join lines and keep cursor
- `<C-d>` — Scroll down and center
- `<C-u>` — Scroll up and center

---

## 💾 Save / Quit

- `<leader>w` — Save file
- `<leader>s` — Save file (quick)
- `<leader>q` — Quit window

---

## 🪟 Window Navigation / Splits

- `<leader>h/j/k/l` — Move to split
- `<leader>wn` — Move to next split (cycle)
- `<leader>wp` — Move to previous split (cycle)
- `<leader>ws` — Horizontal split
- `<leader>wv` — Vertical split
- `<C-Up/Down/Left/Right>` — Resize window

---

## 🔍 Search & Highlight

- `<leader>ps` — Grep for string (Telescope)
- `<leader>sc` — Toggle case-sensitive search
- `<leader>nh` — Clear search highlights

---

## 📋 Clipboard & Spell

- `<leader>y` — Yank entire buffer to clipboard
- `<leader>sp` — Toggle spell check

---

## 🧠 Tree-sitter Selection

- `gnn` — Init selection
- `grc` — Scope incremental
- `grm` — Node decremental

---

## 🚀 Git Commands

### Neogit

- `<leader>gg` — Open Neogit
- `<leader>gc` — Commit
- `<leader>gp` — Push
- `<leader>gl` — Pull
- `<leader>gn` — Create and checkout new branch
- `<leader>gb` — Git branches (Telescope)

---

## 🚨 Trouble Diagnostics

- `<leader>xx` — Toggle Trouble (doc+workspace)
- `<leader>xw` — Workspace diagnostics
- `<leader>xd` — Document diagnostics
- `<leader>xr` — LSP references
- `<leader>xl` — Location list
- `<leader>xq` — Quickfix list

---

## 📸 CodeSnap

- `<leader>cs` (visual) — Take CodeSnap screenshot

---

## 🤖 Copilot

- `<C-J>` — Accept suggestion
- `<C-K>` — Dismiss suggestion
- `<C-Space>` — Trigger completion
- `<leader>cp` — Toggle Copilot

---

## 🐞 DAP (Debugging)

- `<F5>` — Continue
- `<F10>` — Step over
- `<F11>` — Step into
- `<F12>` — Step out
- `<leader>db` — Toggle breakpoint
- `<leader>dr` — Open REPL

---

## ⚓ Harpoon

- `<leader>ha` — Add file
- `<leader>h1` through `<leader>h4` — Navigate to files

---

## 📝 Obsidian Integration

- `<leader>oo` — Open Obsidian note
- `<leader>ot` — Open today's note
- `<leader>on` — Create new note
- `<leader>os` — Search notes
- `<leader>ob` — Show backlinks
- `<leader>ol` — Link to note
- `<leader>of` — Follow link

---

## 🧘 Zen Mode

- `<leader>zz` — Toggle Zen Mode
- `<leader>zq` — Zen Mode + Quit

---

## 🍅 Pomodoro (pomo.nvim)

- `<leader>ps` — Start 25m work timer
- `<leader>pb` — Start 5m break
- `<leader>pl` — Start 15m long break
- `<leader>pr` — Repeat last timer
- `<leader>pe` — Stop current timer
- `<leader>pp` — Start Pomodoro session

---

## 📂 Session Management

- `<leader>ss` — Save session
- `<leader>sl` — Load session

---

## 🕵️ Telescope Leader Keymap Search

- `<leader>?` — Search all leader keymaps