
# Steps to Apply a Macro

1. **Record the macro** into a register (e.g., a):

```vim
qa       " Start recording into register a
...      " Do your actions
q        " Stop recording
```

2. **Play it back** at the current cursor location:

```vim
@a       " Runs the macro stored in register a
```

3. **Repeat the last macro**:

```vim
@@       " Repeats the most recently used macro
```

4. **Apply the macro N times**:

```vim
10@a     " Run macro 'a' 10 times
```
