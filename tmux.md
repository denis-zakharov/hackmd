# Tmux

## prefix
The default prefix key is `Ctrl+b`.

## session
Create a new session
```
tmux new -s session-name
tmux new -s session-name -n window-name
```

Detach from a session `<prefix>-d`

List sessions `tmux ls`

Attach to a session `tmux attach -t session-name`

Kill a session `tmux kill-session -t session-name`

## windows (tabs)
Create a window `<prefix>-c`

Rename a window `<prefix>-,`

Moving between windows `<prefix>`
```
n, p - next, previous
0-9 - to a win#N
w - select
f <text> - search a window by text
```

Kill a window `<prefix>-&`

## panes (splits)
Create and move `<prefix>`
```
% - a vertical split
" - a horizontal split
o - cycle through the panes
arrows - move between the panes
```

Change default layouts `<prefix>-<space>`
- even-horizontal
- even-vertical
- main-horizontal (main at the top)
- main-vertical (main on the left)
- tiled (evenly)

Kill a pane `<prefix>-x`

## command mode
`<prefix>-:`

Examples
```
new-window -n console
new-window -n processes "top"
```

## help
`<prefix>-?`

## scroll
`<prefix>-PgUp/PgDown`

## copy mode and search
`<prefix>-[` enters copy mode
`Enter` exits copy mode
`Ctrl-Space` select
`Ctrl-w` copy
`<prefix>-]` paste

`?` search upwards; `n` - next, `N` - previous
`/` search downward; `n` - next, `N` - previous

## copy visible pane text
`:capture-pane`

Save to a file `:capture-pane,save-buffer buffer.txt`

## copy buffers
```
list-buffers
choose-buffer 2
# paste
<prefix>-]
```

## configuration
Reload `source-file ~/.tmux.conf`

Configuration example
```
# -g sets globally

# change the prefix
set -g prefix C-a
# ensure we can send Ctrl+A to other apps
bind C-a send-prefix

# unbind the orignal prefix (for reuse)
# unbind C-b

# colored terminal
set -g default-terminal "screen-256color"
# set the status line's colors
set -g status-style fg=white,bg=black
# set the color of the window list
setw -g window-status-style fg=cyan,bg=black
# set colors for the active window
setw -g window-status-current-style fg=white,bold,bg=red
# colors for pane borders (a thick yellow border for an active pane)
# setw -g pane-border-style fg=green,bg=black
# setw -g pane-active-border-style fg=white,bg=yellow

# update status line every minute
set -g status-interval 60

# setting the delay between prefix and command
set -s escape-time 1

# enable mouse
# set -g mouse on

# starting the windows index from 1
set -g base-index 1
# starting the pane index from 1
setw -g pane-base-index 1

# bind reload conf
bind r source-file ~/.tmux.conf \; display "tmux.conf is reloaded"

# remap movement keys
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R

# a repeatable resize by 5 units (press the prefix once)
bind -r H resize-pane -L 5
bind -r J resize-pane -D 5
bind -r K resize-pane -U 5
bind -r L resize-pane -R 5

# enable vi keys in copy mode
setw -g mode-keys vi
```
