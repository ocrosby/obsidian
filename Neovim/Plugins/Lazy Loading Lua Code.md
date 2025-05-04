
You may not always want to run code whenever Neovim starts or when a FileType event triggers. Instead you may want to run logic on-demand, either by calling a function directly or via keymap.

The simplest way to do that, is to place a single file in a `lua/` folder. This folder can be in any of the `runtimepath` or packages locations. For example, in `lua/foobar.lua` you would write:

```lua
-- Using `M` is a common Lua convention, `M` stands for module
-- It's used for a table that contains all exported functions and properties
-- (Exported because it's returned at the end of the file)
local M = {}

function M.do_something()
	print('Hello world')
end

return M
```

A user would then load it on-demand using `:lua require('foobar').do_something()`.

`require` is a Lua function to search a module, load and execute it. The first require call caches the module. This means a second `require('foobar')` call won't run all the logic again. Consider the following example:

```lua
local M = {}

function M.do_something()
	print('This is pinted whenever you call do_something()')
end

print('This is printed only once')

return M
```

The first require call will print "This is printed only once". The second wont. You can manually unload a module by setting `package.loaded[moduleName]` to `nil`. For example: `:lua package.loaded['foobar'] = nil`.

Thus, if your plugin needs to setup custom user commands or run other logic whenever the user starts a Neovim process, you'd place that logic in a `plugin/foobar.lua` file. The main bulk of the logic should go into a `lua/foobar.lua` file, which is lazy loaded when the user first uses it.

This approach works well for smaller plugins which are a couple hundred lines or less.

### Advantages

- Small startup footprint. Neovim sources `plugin/foobar.lua` on startup, but that's fairly cheap as long as the plugin doesn't run some expensive logic.
- Remaining functionality is lazy loaded by default. Neovim dosn't load `lua/foobar.lua` until you call require('foobar').
- Runtime of a single require is low.

### Disadvanteges

- Can get hard to navigate once the file becomes large.
