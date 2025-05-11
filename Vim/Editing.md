
- `i`: Enter insert mode before the cursor. 
- `I`: Enter insert mode at the beginning of the line. 
- `a`: Enter insert mode after the cursor. 
- `A`: Enter insert mode at the end of the line. 
- `o`: Open a new line below the current line and enter insert mode. 
- `O`: Open a new line above the current line and enter insert mode. 
- `x`: Delete the character under the cursor. 
- `X`: Delete the character before the cursor. 
- `dd`: Delete the current line. 
- `2dd`: Delete two lines.
- `yy`: Yank (copy) the current line. 
- `p`: Paste the yanked or deleted text after the cursor. 
- `P`: Paste the yanked or deleted text before the cursor. 
- `u`: Undo the last change. 
- `U`: return the line to it's original state.
- `Ctrl + r`: Redo the last undone change.
- `dw`: Delete a word
- `d$`: Delete to the end of the line

Note: `dd` deletes a line but it also stores it in a Vim register.

The Vim register can be then used by the put command `p` which will put the contents of the register on the line after the cursor.

## The Replace Command

Moving the cursor to a character that needs to be altered you type

`r<c>`

where `<c>` is the character you want to replace the current character with.

## The Change Operator

Moving the cursor to a character in a word you type

`ce`

this will allow you to change text until the end of the word. 

`ce` deletes the word and places you in Insert mode.
`cc` does the same for the whole line

The change operator works in the same way as delete.

`c [number] motion`

The motions are the same such as `w` (word) and `$` (end of line).


## Cursor Location and File Status

`C_G` shows your location in the file and the file status.
`<line_number>G` moves you to the specified line.

## The Search Command

Type `/` followed by a phrase to search for the the phrase.

### Repeating the search

After searching for a phrase you can enter `n` to repeat the search.

To search for the same phrase in the opposite direction enter `N`.

Initially to search for a phrase in the backward direction, use `?` instead of `/`.

To go back where you came from press `C_o`. Repeat to go back further. `C_i` goes forward.

## Matching Parenthesis Search

Type `%` to find a matching ),], or }


## The Substitute Command

Type :s/old/new/g to substitute `new` for `old` globally within the string.

This will replace all occurrences of `old` with `new`.

Type `:s/old/new/` to replace the first occurrence of `old` with `new`.

Type `:#,#s/old/new/g` to replace every occurrence of `old` with `new` between two lines.

Type `:%s/old/new/g` to change every occurrence in the whole file

Type `:%s/old/new/gc` to find every occurrence in the whole file with a prompt whether to substitute or not.


## Execute External Commands

Type `:!` followed by an external command to execute that command.


## Copy and Paste text

Use the `y` operator to copy text and the `p` operator to paste it

Use visual mode to select text and type `y` to yank (copy) the highlighted text.

Move the cursor to where you want to paste the information then type `p` to put (paste) the text.

Note: You can also use `y` as an operator: `yw` yanks one word, `yy` yanks the whole like, then p puts that line.



## [[Multi-line Editing]]