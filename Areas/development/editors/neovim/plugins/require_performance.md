`require` is a Lua function to load and run modules. `reuire` traverses part of the filesystem to find a module, and loads it. It caches the module, which means only the first call is expensive and later calls are cheaper.

To see where it looks for modules, you can try loading a module that doesn't exist. If you run `lua: require('does-not-exist')` you'll get an error telling you that the `does-not-exist` module wasn't found. The message will include a list of paths.

The system traversal and initial caching adds some cost. That means splitting functionality over many modules will introduce overhead compared to putting the same functionality into a single module.
