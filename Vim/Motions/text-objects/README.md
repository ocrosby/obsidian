---
aliases: ["Text Objects"]
---
# Text Objects

## [[a-and-i]]

## [[vim/motions/text-objects/quotes]]

## [[vim/motions/text-objects/functions]]

## [[vim/motions/text-objects/quotes]]

## [[vim/motions/text-objects/brackets]]

## [[vim/motions/text-objects/paragraphs]]

## [[vim/motions/text-objects/custom-objects]]

---

Custom text objects extend Vim's native motions for more powerful editing.

## 🔌 Plugins

- `[wellle/targets.vim](https://github.com/wellle/targets.vim)`: Adds intuitive text objects for surrounding characters and arguments.
    
- `[nvim-treesitter/nvim-treesitter-textobjects](https://github.com/nvim-treesitter/nvim-treesitter-textobjects)`: Syntax-aware objects like functions, blocks, loops.
    

---

## 🧠 General Usage Pattern

```
d[a|i]{object}    " delete
c[a|i]{object}    " change
y[a|i]{object}    " yank
v[a|i]{object}    " visually select
```

---

## ⟳ With `targets.vim`

### Argument Objects

- `daa` – delete around argument
    
- `via` – visually select inside argument
    

### Quotes & Pairs (works with any delimiter)

- `da'`, `di'`, `da"`, `di"`, `da)`, `di)` – around/inside quotes, parentheses, etc.
    

### Blocks and Tags

- `dat`, `dit` – around/inside HTML tag
    
- `dab`, `dib` – around/inside block `{}`
    

---

## 🌲 With Treesitter Textobjects

> Make sure your Treesitter config includes the right mappings.

### Example Treesitter Config

```
require'nvim-treesitter.configs'.setup {
  textobjects = {
    select = {
      enable = true,
      lookahead = true,
      keymaps = {
        ["af"] = "@function.outer",
        ["if"] = "@function.inner",
        ["ac"] = "@class.outer",
        ["ic"] = "@class.inner",
        ["ab"] = "@block.outer",
        ["ib"] = "@block.inner",
        ["al"] = "@loop.outer",
        ["il"] = "@loop.inner",
        ["aa"] = "@parameter.outer",
        ["ia"] = "@parameter.inner",
      },
    },
  },
}
```

### Examples

|Command|Description|
|---|---|
|`daf`|Delete around function (includes sig)|
|`dif`|Delete inside function body|
|`vic`|Visually select inner class body|
|`dac`|Delete around class|
|`vil`|Visual inner loop|
|`daa`|Delete argument at cursor|
|`cia`|Change inner argument|

---

## 🛠️ Advanced Combinations

- `g@af` – Apply operator (e.g. formatting) to function
    
- `yif` + `p` – Copy one function and paste elsewhere
    
- `ci"` + `@r` – Replace contents of a string with register `r`
    

---

## ✅ Best Practices

- Use `:map af` to preview mappings
    
- Combine text objects with macros and repeat commands
    
- Tailor Treesitter queries per language (Rust, JS, etc.)
    

---

## 🔗 See Also

- [[Motions/text-objects/functions]]
    
- [[Motions/text-objects/quotes]]
    
- [[Motions/text-objects/a-and-i]]
