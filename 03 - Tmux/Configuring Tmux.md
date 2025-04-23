
Create a `~/.tmux.conf`.

```plaintext
# bind r to reload the tmux configuration
unbind r
bind r source-file ~/.tmux.conf

# set the tmux leader key to CTRL-g
set -g prefix C-g

# enable the mouse in tmux
set -g mouse on

# bind the h,j,k,h keys to navigate panes in tmux
bind-key h select-pane -L
bind-key j select-pane -D
bind-key k select-pane -U
bind-key h select-pane -R

set-option -g status-position top

# List of plugins
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'catppuccin/tmux'

# Other examples:
# set -g @plugin 'github_username/plugin_name'
# set -g @plugin 'github_username/plugin_name#branch'
# set -g @plugin 'git@github.com:user/plugin'
# set -g @plugin 'git@bitbucket.com:user/plugin'

```

Since I'm using tokyonight in neovim I'm going to try to use [Tokyo Night Tmux Theme](https://github.com/fabioluciano/tmux-tokyo-night).
