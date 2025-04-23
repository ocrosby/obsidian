My tmux configuration file `~/.tmux.conf looks like this:

```plaintext
# Set tmux prefix to Ctrl-g
set-option -g prefix C-g
bind-key C-g send-prefix

# ALSO bind Ctrl-b as an additional prefix
bind-key C-b send-prefix
```

It essentially sets the tmux prefix to `Ctrl-g` and binds `Ctrl-b` as an additional prefix. This allows you to use either key combination to access tmux commands.


## [[Navigation in Tmux]]
## [[Managing Tmux Sessions]]
