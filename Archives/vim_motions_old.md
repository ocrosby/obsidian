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

## Important Notes on Vim Motions

#### modes: normal, insert, visual, replace

normal mode - you can move around text but not edit it
insert mode - you can edit text
visual mode - you
#### hjkl / HJL

In Normal mode

h - move left
j - move down
k - move move up
l - move right

Note: Try out vim adventures (the game)

#### insert

Press the i key to go into insert mode

To get out of insert mode and back to normal mode <ESC>

I have remapped jk to <ESC> to not leave the home road.

Press the `I` key to go to insert mode at the beginning of the current line.

#### Esc



#### append

You can inter insert mode in the direction of writing by typing `a` from normal mode.

Note: `a` takes you into insert mode after the current position.

You can enter insert mode at the end of a line by by typing `A` from normal mode.

Note: `A` takes you into insert mode at the end of the current line.

#### 0^$

In normal mode typing `0` takes you to the beginning of the line keeping you in normal mode.

In normal mode typing `^` takes you to the first non-space character at the beginning of the line keeping you in normal mode.

In normal mode typing `$` takes you to the end of the line keeping you in normal mode.

#### word

To jump forward a word at a time (to the start of the next word) in normal mode type `w`.


To jump forward 5 words you can type `5w` in normal mode.

#### end

To move to the end of a word in normal mode type `e`.

#### back

To move backwards a word at a time in normal mode type `b`.

#### find

In normal mode, typing `f<char>` will jump your character to the first occurrence of the specified character.

Note: typing `;` after a find command repeats it.

Note: typing `,` after a find command will take you to the previous occurrence.

In normal mode, typing `F<char>` will search backwards for the specified character.


You can also utilize counts with the find command:

    In normal mode, typing `4ft` will find the fourth occurrence of the character 't'.

#### ==

Typing a double equal sign will get vim to try to indent properly to the best of it's ability.

#### gg

Typing `gg` in normal mode moves to the top of the document.

#### G

Typing `G` in normal mode moves to the bottom of the document.

#### Shift+M

Typing `<Shift>M` in normal mode should take you to the middle of the visual screen.

#### delete / dd

The deletion command is `d`.

To delete a word enter `dw`.

To undo the last operation type `u` in normal mode.

The deletion command puts the deleted text into a register.

To paste the contents of a register type `p` in command mode (for paste).

To delete an entire line, from normal mode type `dd`.

To delete in a word, from normal mode type `diw`.  This takes the entire object regardless of position.


Note: with text objects 

both the delete and the closing range word an be replaced by any combination.

so `di{` would me delete in brackets

here the motion is 'in' and the range is '{'

and `dif` is delete in function (this is a bit advanced and my require some lsp support)


Note Vim also understands the motion 'around' or 'a'

When 'a' is used as the motion (example `daw`) it will delete around a word including spaces at the end or beginning.


Change In Open Parenthesis

`ci(` this will allow you to change in the following parenthesis

`di"` will allow you to delete in the following quote

Note: The dot operator will repeat the previous action so 

# "abc", "def", "hij"

entering `di"` from the beginning of the line followed by the dot operator twice will empty out all quoted text


Note: To start a new line below the current line type `o` from normal mode.

Note: To start a new line above the current line type `O` from normal mode.



#### yank / yy

Copying is done using the yank command and it is assigned the character `y`.

To copy a word, from normal mode type `yw`.  This will put that word in a register and it should be available to paste using `p`.

To yank an entire line, from normal mode type `yy`.


#### paste

To paste text from the register, from normal mode type `p`.

Note: `p`, unlike `y` acts after the cursor.

The counterpart of `p` is `<shift>P`, this will paste before the cursor.

For an entire yanked line `p` will paste it after the current line, and `<shift>P` will paste it before the current line.

#### zz

In normal mode typing `zz` will make the current line the visual center.

#### /

In normal mode the `/` operator starts a search

To scroll results we use `n` and `<shift>N` to search backwards

If you want to search backwards enter `<shift>/`

Search and replace can be done by searching for a string then from insert mode changing it then in command mode hitting `n` for next to find the next one and using the dot operator to repeat the last insert mode operation.

#### pairs operator

`%` is an extremely useful operator that will always take you to the matching counter part of the pair.

#### High

#### Medium

#### Low

#### replace

To enter replace mode, from normal mode type `<shift>R`

#### undo

#### C-R : redo

To redo a previous operation, in normal mode type `<CTRL>R`.

#### visual : V

To enter visual mode, from normal mode type `v`.


Review <C-V> to mark visual blocks.



