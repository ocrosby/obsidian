In Vim, **registers** are storage locations for text and commands. Each register is identified by a **single character key**, and different types of registers serve different purposes:

## Named Registers ("a-"z)

- a-z: You can explicitly yank/delete/record into these using "a, "b, etc.
- Uppercase ("A-"Z) appends to the corresponding lowercase register.

## Unnamed Register (")

- The default register used when you yank, delete, or paste without specifying another.
- Accessed with just " or implicitly (e.g., p, P)

## Read-Only Registers

- ". - Last inserted text
- "0 - Last yank (not delete)
- "1-"9 - Delete history ("1 is the most recent delete)
- ": - Last command-line command
- "/ - Last search pattern
- "% - Current file name
- "# - Alternate file name
- "= - Evaluate expression (e.g., :put =2+2)
- ". - Last inserted text

## Selection Registers

- "* - System clipboard (X11 primary selection)
- "+ - System clipboard (clipboard selection)

Note: Requires Vim to be compiled with clipboard support (+clipboard or +xterm_clipboard).


## Black Hole Register ("_)

- Discards content: use when you want to delete/yank without overwriting other registers.

```vim
"_d
```


## Recording Registers

- You record macros into a-z registers with `q{register}`
- For example: `qa` starts recording into register `a`.


## The Quote Character


In Vim, the **quote character (") is the prefix used to specify a register**. It tells Vim which register to use for an operation like yank (y), delete (d), paste (p), or put (:put).

### Why the Quote Character?

Because commands like y, d, p, etc., operate on **registers**, the quote character explicitly selects which one:

- " (double quote) says, "I'm going to name a register now".

#### Examples

|**Command**|**Meaning**|
|---|---|
|"ayy|Yank current line into register a|
|"ap|Paste the contents of register a|
|"0p|Paste the most recently yanked text|
|:put "a|Put contents of register a below the line|
|"*p|Paste from system clipboard (X11 or Windows)|


If you run `yy`, Vim uses the **unnamed register** (" implicitly), meaning:

- You don't need to type " unless you're using a **specific** register.
- So `yy` is shorthand for `"yy`.



