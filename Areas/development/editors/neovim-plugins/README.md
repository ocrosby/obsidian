---
aliases: ["Introduction to Neovim Plugins"]
---

I keep having ideas of things I would like to add to my Neovim PDE but I can't seem to find a good reference out there on how to generally create plugins so this area is where I intend on making notes I'm taking based on the plugins I've seen.

I really like the plugins I've seen from [Tim Pope](https://github.com/tpope) on GitHub so my approach is going to be to analyze what I see in his plugins and try to create a set of notes that illustrate a good procedure for plugin development for Neovim. Wish me luck.


- Learn to develop Neovim plugins using lua
- Customize and extend Neovim's functionality
- Seamlessly integrate with existing Neovim features and plugins
- Create and share your own powerful plugins

## When to use Plugins

- Macros/Subsitution are good first idea
- Custom lua modules
- Test, deploy, share with the community

## What is a plugin?

- A plugin is just a `lua` module(s)
- We can load it like any other `lua` module
- Remote plugins vs lua plugins

Plugins are about packaging, deployment, and sharing


## Creating a plugin repository

There are a few plugin templates but this is the best IMO

[Plugin Template](https://github.com/nvimdev/nvim-plugin-template)


## Development Tools

- nvim-dap (for debugging)
- vusted (for testing)
- one-small-step-for-vimkind (for dap lua adapter)




## References

- [Build Your Own Neovim Modules Library](https://www.youtube.com/watch?v=R1ecY30YBVk)
- [How to Develop Neovim Plugins in Lua](https://www.youtube.com/watch?v=yN04HCeOjmo)
- [Cloud-Native Corner Dotfiles](https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqazZOYWVaYmN1YVU4UHRBbEVfaHNwN3ZmMUlSZ3xBQ3Jtc0tuVkdRQy1xdmxQLS10WV9kY2RMWUhGQzVsejhIMS1SdU1XUnFTQ1BYeldzWlhmRWlPbjZ4aFVlUndGRGh3NEpEWjQtYzhmZUxIcXZyNXVRRERQOWo5clQzNG9MN0t1YkZuYmpFdURNNFYyNDFpaldRaw&q=https%3A%2F%2Fgithub.com%2FPiotr1215%2Fdotfiles%2Ftree%2Fmaster%2F.config%2Fnvim&v=yN04HCeOjmo)
- [Luapad](https://github.com/rafcamlet/nvim-luapad)
- [LazyDev](https://github.com/folke/lazydev.nvim)
- [nvim-luaref](https://github.com/emiasims/nvim-luaref)
- [Neovim Lua Plugin from Scratch](https://www.youtube.com/watch?v=n4Lp4cV8YR0)
- [How to Build a Simple Neovim Plugin](https://adam-drake-frontend-developer.medium.com/how-to-build-a-simple-neovim-plugin-0763e7593b07)
- [Neovim Plugins to get Started](https://vonheikemen.github.io/devlog/tools/neovim-plugins-to-get-started/)
- [This week in Neovim](https://dotfyle.com/this-week-in-neovim)
- [Neovimcraft](https://neovimcraft.com/)
- [Awesome Neovim](https://github.com/rockerBOO/awesome-neovim)
- [Lua-Guide](https://neovim.io/doc/user/lua-guide.html#lua-guide)
- [Develop](https://neovim.io/doc/user/develop.html)
- [Neovim Macros](https://www.youtube.com/watch?v=ERMmTs4AVA4)
- 
- 