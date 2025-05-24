
This document summarizes the **tmux operations**, **defined keybindings**, and **common commands** for working with tmux. It serves as a complete quick-reference guide.

---

## 🚀 Core Settings

|Setting|Description|
|---|---|
|**Mouse support**|Enabled. Use the mouse to scroll, resize panes, and click to select windows/panes.|
|**History limit**|100,000 lines of scrollback history available per pane.|
|**Bell action**|Disabled (no beeps when errors or notifications happen).|
|**True color & 256-color support**|Enabled using `tmux-256color` and true color override for xterm-compatible terminals.|

---

## 🔑 Leader (Prefix) Key

|Key|Description|
|---|---|
|`Ctrl-g`|**Primary prefix key**. Use this before all other tmux shortcuts.|
|`Ctrl-g Ctrl-g`|Sends a literal `Ctrl-g` to programs (useful in bash, vim, etc.).|

---

## 🪟 Window Management

|Shortcut|Action|
|---|---|
|`Ctrl-g c`|Create a new window.|
|`Ctrl-g &`|Kill (close) the current window.|
|`Ctrl-g ,`|Prompt to rename the current window interactively.|

---

## 🗄️ Splitting Panes

|Shortcut|Action|
|---|---|
|`Ctrl-g|`|
|`Ctrl-g -`|Split the current pane **horizontally** (stacked).|

---

## 🧱 Navigating Between Panes (Vim-style)

|Shortcut|Action|
|---|---|
|`Ctrl-g h`|Move to the pane on the **left**.|
|`Ctrl-g j`|Move to the **pane below**.|
|`Ctrl-g k`|Move to the **pane above**.|
|`Ctrl-g l`|Move to the pane on the **right**.|

_(Repeatable: hold the key for continuous movement.)_

---

## 🫤 Resizing Panes (Vim-style, Shifted)

|Shortcut|Action|
|---|---|
|`Ctrl-g H`|Resize pane **5 cells left**.|
|`Ctrl-g J`|Resize pane **5 cells down**.|
|`Ctrl-g K`|Resize pane **5 cells up**.|
|`Ctrl-g L`|Resize pane **5 cells right**.|

_(Repeatable: hold the key for continuous resizing.)_

---

## 📋 Copy Mode (Vi-style)

|Shortcut|Action|
|---|---|
|`Ctrl-g [`|Enter **copy mode** (scroll & navigate with Vim keys).|
|`v` (in copy mode)|Start selection (visual mode).|
|`y` (in copy mode)|Copy the selected text and exit copy mode.|

---

## 🛠️ Miscellaneous

|Shortcut|Action|
|---|---|
|`Ctrl-g r`|**Reload the tmux config file** and display a message confirmation.|

---

## 🎨 Status Bar

|Section|Content|
|---|---|
|**Left side**|`Session name` (bold).|
|**Right side**|`Date (yellow)` + `Time (green)`.|
|**Refresh interval**|Every 5 seconds.|

---

## 🔌 Plugins

**Plugin Manager:**

- **TPM (Tmux Plugin Manager)** is located at:  
    `~/dotfiles/tmux/plugins/tpm`
    

**Installed Plugins:**

|Plugin Name|Purpose|
|---|---|
|`tmux-plugins/tpm`|Plugin manager to install/manage tmux plugins.|
|`ghifarit53/tokyonight-tmux`|Applies the **TokyoNight** theme to tmux’s status bar.|

_(Install plugins: `Ctrl-g I` after starting tmux.)_

---

## 🛠️ Managing Sessions (Inside tmux)

Use the **command prompt** (press `Ctrl-g :`) to run these commands:

|Command|Description|
|---|---|
|`new-session`|Create a new tmux session.|
|`rename-session`|Rename the current session.|
|`kill-session`|Kill the current session.|
|`kill-session -t <name>`|Kill a specific session by name.|
|`switch-client -t <name>`|Switch to a different session.|
|`list-sessions`|List all active tmux sessions.|
|`detach`|Detach from the current session (keep it running in the background).|

**Example: Renaming a session interactively**

- Press `Ctrl-g :` and type:  
    `command-prompt -I "#S" "rename-session '%%'"`
    

---

## 💻 Managing Sessions (From the Terminal)

|Command|Description|
|---|---|
|`tmux`|Start a new tmux session (or attach to an existing one).|
|`tmux new -s <name>`|Start a new session named `<name>`.|
|`tmux ls` or `tmux list-sessions`|List all active tmux sessions.|
|`tmux attach -t <name>`|Attach to an existing session by name.|
|`tmux kill-session -t <name>`|Kill a specific session by name.|
|`tmux kill-server`|Kill **all** tmux sessions and stop the server.|
|`tmux rename-session -t <old> <new>`|Rename an existing session from `<old>` to `<new>`.|
|`tmux detach`|Detach from tmux (from inside tmux).|

---

## 📑 Notes & Best Practices

- **Leader key:** Using `Ctrl-g` for ergonomic access.
    
- **Session naming:** Keep sessions meaningful when working with multiple projects.
    
- **Copy mode:** Vi-style keybindings simplify navigation and copying text.
    
- **True color:** Best experience when using terminals that support `tmux-256color`.
    
- **Dotfiles caution:** If using GNU Stow, avoid symlinking the `plugins/` folder to your home directory (use `.stow-local-ignore` or place plugins outside the stowed folder).
    

---

# ✅ End of Reference