At a certain size it makes sense to start splitting up a plugin into separate files.

For example you might have a structure like this:

```plaintext
/plugin/foobar.lua
/lua/foobar/init.lua
/lua/foobar/session.lua
/lua/foobar/rpc.lua
```

Here `require('foobar')` will load `foobar/init.lua`, which is the same as:

```plaintext
/plugin/foobar.lua
/lua/foobar.lua
/lua/foobar/session.lua
/lua/foobar/rpc.lua
```

Tip: Try to avoid creating a `foobar/util.lua`, it often ends up as a collection of unrelated functions.

## Advantages

- Can improve readability if you separate components in a meaningful way - e.g. by their responsibilities.
- Gives more flexibility to how you structure code

## Disadvantages

- Can lead to a higher startup footprint, especially if the main entry point (`foobar.lua` or `init.lua`) eagerly loads all other modules. 
- Creating lots of tiny modules can reduce readability due to the increased surface and indirection.
- You can't have "plugin private" functions across modules. To use a function of `foobar/session.lua` in `foobar/init.lua` you need to return it in `foobar/session.lua`. That exposes it to users too. A common convention is to prefix private functions with an underscore to indicate it's considered private. Some plugins consider any undocumented functions as private.