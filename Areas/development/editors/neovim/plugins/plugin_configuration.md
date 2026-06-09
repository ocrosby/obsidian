
# Plugin Configuration

## A Setup Method

With the rise of Lua plugins it became popular to have a single setup function to configure plugins. The plugin exports a setup function which users can call to configure it.

One the plugin side it looks like this:

```lua
local M = {}

local _config = {}

function M.setup(config)
	-- Simple variant; could also be more complex with validation, etc.
	_config = config
end

function M.do_something()
	local option_x = _config.option_x or 'some_default_value'
	-- ...
end

return M
```

And from the user perspective it looks like this:

```lua
require('foobar').setup({
	option_x = true,
	option_y = 'bar'
})
```

This approach is useful if the plugin performs expensive initialization or if what it initializes depends on the configuration. It gives the user full control over when the initialization happens and it's explicit which options are used.

An example use-case is if your plugin creates autocmds based on configuration values.

On the other hand, if the plugin doesn't require initialization and has good defaults, a setup method may be the wrong choice. It has the disadvantage that it forces users to require the plugin to configure it. This means if the user wants to lazy load the plugin's modules they'd have to defer the setup call into a keymap or similar.

If they don't  do that and eagerly require the plugin to call setup in their `init.lua` they may unnecessarily increase their startup time. This can become a problem if a plugin has many modules and it's main module eagerly imports all its other modules.

## A Global Setting Variable

Consider an alternative approach: Using a single global configuration table. From the plugin side:

```lua
local M = {}

function M.do_something()
	local settings = vim.g.foobar_settings or {}
	local option_x = settings.option_x or 'some_default_value'
	-- ...
end

return M
```

From the user perspective, changing settings would look like this:

```lua
vim.g.foobar_settings = {
	option_x = true,
	option_y = 'bar'
}
```

The advantage of this approach is that users don't need to load the plugin using require up-front. They can configure the plugin in their init.lua without loading the plugin.

If you plugin uses many modules, each module will be able to access this variable. With the setup approach, plugins often end up re-creating an effective global in a more complex way:

```plaintext
foobar/config.lua
foobar/init.lua
foobar/xy.lua
```

Where the communication between modules looks like this:

```text
  ┌───────────┐
  │ init.lua  ├──────────────────────────────┐
  ├───────────┤        ┌────────────┐     ┌──┴─────┐
  │ M.setup() ├───────►│ config.lua │◄────│ xy.lua │
  └───────────┘        └────────────┘     └────────┘
```

The global settings variable approach has its downsides though:

- It's harder to do validation of the settings
- A chance for name conflicts, but this is about the same as a name clash for the plugin and module names

This - or using flat settings - used to be the default way to configure plugins written in vimscript.

