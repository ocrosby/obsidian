
# What's a Lua Plugin

neovim added support for Lua plugins, which is basically the same as a vim plugin, except the file extension is `.lua` instead of `.vim` and the file contains Lua code instead of vimscript. Neovim executes the logic when it starts in the case of a global plugin, or when a FileType event triggers in case of a filetype plugin.
