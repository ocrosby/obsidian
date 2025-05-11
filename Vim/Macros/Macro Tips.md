
# {{title}}


- Macros can include **normal, insert, visual, and command-line mode actions**.
- Macros are **session-local**, unless you store them in your .vimrc.
- To apply a macro to a block of lines, combine it with a visual selection or `:normal`

```vim
:'<, '>normal @a
```

To apply a macro across a **visual range of lines**, use:

```vim
:'<,'>normal @a
```

Or across a line range:

```vim
:10,20normal @a
```

