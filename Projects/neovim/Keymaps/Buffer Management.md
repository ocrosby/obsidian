
- `<leader>n` — Next buffer
- `<leader>p` — Previous buffer
- `<leader>x` — Close buffer
- `<Tab>` — Next buffer
- `<S-Tab>` — Previous buffer
- `<leader>bd` — Delete current buffer
- `<leader>ba` — Close all buffers except current


## Split the screen horizontally

```plaintext
:split
```

or

```plaintext
:sp
```

## Split the screen vertically

```plaintext
:vsplit
:e anotherfile.txt
```

or combined

```plaintext
:vsp anotherfile.txt
```

## Optional Keymap

```lua
vim.keymap.set("n", "<leader>sv", ":vsplit | enew<CR>", { desc = "Vertical split and new file" })
```

## Open a new file in the split

```plaintext
:e newfile.txt
```

