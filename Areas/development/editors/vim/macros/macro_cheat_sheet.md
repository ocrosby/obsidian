# 📜 Vim Macro Cheat Sheet

Macros in Vim are powerful for automating repetitive tasks. This guide covers how to record, run, and apply macros effectively.

---

## 🎥 Recording a Macro

```vim
qa         " Start recording into register 'a'
...        " Perform your actions
q          " Stop recording
```

- Use any letter `a`–`z` to name the register.
    
- Uppercase (e.g. `QA`) appends to an existing macro in the register.
    

---

## ▶️ Playing Back a Macro

```vim
@a         " Play macro in register 'a'
@@         " Replay the last macro used
5@a        " Repeat macro in register 'a' 5 times
```

- Works in normal mode at the cursor location.
    

---

## 📌 Applying a Macro to a Range

### Visual Mode

1. Select lines in visual mode (e.g., `Vjj`)
    
2. Run:
    
    ```vim
    :normal @a
    ```
    

### Line Range

```vim
:10,20normal @a   " Applies macro 'a' from line 10 to 20
:%normal @a       " Applies macro to all lines in the file
```

---

## 🔍 Viewing or Editing a Macro

### View

```vim
:reg a            " Show contents of register 'a'
```

### Edit

```vim
:new              " Open a new buffer
:put a            " Paste macro
" Edit as needed
"ay               " Yank selection back into register 'a'
```

---

## 🗜️ Tip: Use Black Hole Register to Avoid Clobbering Macros

```vim
"_d               " Deletes without affecting other registers
```

---

## 🧠 Best Practices

- Record slowly and deliberately—mistakes will be replayed.
    
- Test macros on small inputs first.
    
- Save useful macros in `.vimrc` using `:normal` or mappings if reusable.
    

---

Happy automating! 🚀

---
↑ [[../README]]
