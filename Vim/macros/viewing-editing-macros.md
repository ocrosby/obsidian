---
aliases: ["Viewing or Editing Macros"]
---

# Viewing or Editing Macros

To **see what's in a register a**, type:

```plaintext
:reg a
```

You can also **edit the macro** like text:

1. Open a buffer with `:new`.
2. Paste the contents: `:put a`.
3. Edit as needed.
4. Yank back to the register: visually select it, then  `"ay`

