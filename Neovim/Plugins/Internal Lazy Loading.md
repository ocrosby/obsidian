One way to decrease the cost of `require('foobar')` if using many modules is to ensure that the `foobar/init.lua` only loads other modules when needed.

Instead of having something like this at the start of your `foobar/init.lua`

```lua
local session = require('foobar.session')
local rpc = require('foobar.rpc')
```

You would call them on-demand:

```lua
function M.do_something()
	local session = require('foobar.session')
	-- ..
end
```

If you wanted to reduce the amount of repetition, you could use a metatable:

```lua
local lazy = semtable({}, {
	__index = function(_, key)
		return require('foobar.' .. key)
	end
})
```

This would then allow you to refer to other modules like this:

```lua
lazy.session.do_something()
```

