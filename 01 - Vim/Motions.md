Many commands that change text are made from an operator and a motion.

For example, the format for the delete command with the `d` or delete operator is as follows:

```plaintext
d motion
```

Where:
- `d` is the delete operator
- `motion` is what the operator will operate on

A short list of motions:
- `w` until the start of the next word, EXCLUDING its first character.
- `e` to the end of the current word, INCLUDING the last character.
- `$` to the end of the line, INCLUDING the last character.

Thus typing `de` will delete from the cursor to the end of the current word.

NOTE: Pressing just the motion while in Normal mode without an operator will move the cursor as specified.

## Using a count for a motion

Typing a number before a motion repeats it that many times

Examples:

1. `2w` moves the cursor two words forward
2. `3e` mvoes the cursor to the end of the third word forward
3.  `0` moves the cursor to the start of the line

## Using a count to delete more

In the combination of the delete operator and a motion you insert a count before the motion to delete more:

```plaintext
d number motion
```

`d2w` deletes two words from the cursor

