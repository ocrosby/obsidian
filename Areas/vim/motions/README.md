---
aliases: ["Vim Motions"]
---
# Vim Motions

- [[visual-mode]]
- [[normal-mode]]
- [[range-motions]]
- [[macro-application]]
- [[motion-cheatsheet]]

`C-d` move down half the length of a screen
`C-u` move up half the length of a string

## Vertical Motions

### Line Jump

`#-gg`

For example

`10-gg` to move to line number 10
i
Move down six lines

`6j`

Move up 10 lines

`10k`

Center the screen to the line you are on

`zz`

## Horizontal Motions

### Word Movement

Move forward one word (normal mode)

`w`

Move forward two words (normal mode)

`2w`

Move backward one word (normal mode)

`b`

Move backwards two words

`2b`

### f jumps

Jump to the first occurrence of the specified character (',' for example)

`f,`

After performing an f jump ';' will go to the next occurrence and ',' will move back along the sequence of f jumps.

## Pattern Jumping

`vi(` will jump to the first occurrence of a parenthesis block with the text selected

Example:

`def some_cool_function(arg1, arg2, arg3) -> None:`

I believe you will still be in visual mode once this has occurred.

Think of it as "visual inside blah"

So `vi(` is essentially **visual inside parenthesis**