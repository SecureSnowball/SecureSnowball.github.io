+++
categories = ["tmux"]
date = "2021-12-19"
description = ""
featured = ""
featuredalt = ""
featuredpath = ""
linktitle = ""
slug = "my-tmux-setup"
title = "My Tmux setup"
type = ["posts","post"]
[ author ]
  name = "Captain Snowball"
+++

## Understanding Tmux terminology
Tmux is really easy to understand.
- First we create session
- Sessions have Windows
- Windows have panes

When you create a new session a window with pane is also created, you always work in panes.
`CTRL + b` is default prefix key, you can change it but this cheatsheet uses `CTRL + b`.

## Install Tmux in Ubuntu
Update command according to your distro
```bash
sudo apt install tmux # Works on Debian, Ubuntu, Linux Mint, Pop!_OS, Elementary OS, other Ubutnu/Debian derivatives.
```

Possible commands for other distros
```bash
sudo dnf install tmux # Fedora
sudo pacman -S tmux # Arch, Manjaro
```

## Install OH My Tmux
This is not a must have requirement but it is recommended unless you want a full DIY setup, You can also follow official installation instructions on the repo REAME

Link: https://github.com/gpakosz/.tmux

```bash
cd
git clone https://github.com/gpakosz/.tmux.git
ln -s -f .tmux/.tmux.conf
cp .tmux/.tmux.conf.local .
```

## Add the following lines to tmux config
Open `.tmux.conf.local` file and add the following line, it will allow input sync in all panes using `CTRL + x`
```txt
bind -n C-x setw synchronize-panes
```

### How to use tmux
Start a new unnamed session
```bash
tmux new # Not recommended but works fine for all user cases
```

Start a new named session
```bash
tmux new -s session_name # Don't start it with number
```

Detach session and come back to origin shell
```txt
CTRL + b
```

List all running sessions
```bash
tmux ls
```
Sample output
```txt
0: 1 windows (created Thu Jan  6 10:57:04 2022)
```

Attach to a detached session
```bash
tmux attach-session -t 0 # Session number, check output of `tmux ls`
tmux attach-session -t session_name # Only works with names sessions
```

## Splitting panes
- Horizontal split: `CTRL + b, %`
- Vertical split: `CTRL + b, "`
- Navigate between panes: `CTRL + b, j, k, h, l` or `CTRL + b, <arrow keys>`

## Organizing panes
- Toggle zoom: `CTRL + b, z`
- Resize pane: `CTRL + b, <arrow keys>` # Hold control while pressing arrow keys
- Arranging panes: `CTRL + b, <space>`
- Toggle panel zoom: `CTRL + b, z`
- Sync panel, you can type in all panels at same time: `CTRL + b, x`

## Working with Windows
- Create a new Window: `CTRL + b, c`
- Rename a Window: `CTRL + b, ,(Comma)`
- Go to Window: `CTRL + b, <1-9>`

### Plugins
Tmux can be extended with plugins. I only use tmux resurrect to restore panes and windows after restarting,
It is recommended that we use tpm (tmux plugin manager) to install plugins, tpm can handle installation of other plugins.

#### Install tpm and tmux-resurrect
Repo Link: https://github.com/tmux-plugins/tpm
```bash
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```

Open `~/.tmux.conf.local` and add the following line
```txt
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-resurrect'
```

Now open tmux new session and run `CTRL + b, i` to install plugins.

### Restoring panel
Ensure you follow last step and you have `tmux-resurrect` installed.
Now you can save and restore your plugin even after system restart
- Save: `CTRL + b, CTRL + s`
- Restore: `CTRL + b, CTRL + r`