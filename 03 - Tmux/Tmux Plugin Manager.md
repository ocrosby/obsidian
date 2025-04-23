tmux has a plugin manager called tpm that you can find here [tpm repository](https://github.com/tmux-plugins/tpm).

## Setting up TPM

```shell
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```


Put this at the bottom of `~/.tmux.conf`

```plaintext
# List of plugins
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'catppuccin/tmux'

# Other examples:
# set -g @plugin 'github_username/plugin_name'
# set -g @plugin 'github_username/plugin_name#branch'
# set -g @plugin 'git@github.com:user/plugin'
# set -g @plugin 'git@bitbucket.com:user/plugin'

# Initialize TMUX plugin manager (keep this line at the very bottom of tmux.conf)
run '~/.tmux/plugins/tpm/tpm'
```

## Installing Plugins

1. Add new plugin to `~/.tmux.conf` with `set -g @plugin '...'`
2. Press `prefix`+`I` (capital i, as in Install) to fetch the plugin.

Plugins are cloned to `~/.tmux/plugins` dir and sourced.

## Uninstalling Plugins

1. Remove (or comment out) plugin from the list.
2. Press `prefix`+`alt`+`u` (lowercase u as in uninstall) to remove the plugin.

All the plugins are installed to `~/.tmux/plugins/` so alternatively you can find the plugin directory there and remove it.