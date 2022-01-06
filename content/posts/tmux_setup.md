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

## Install PHP in Ubuntu
```bash
sudo apt install tmux # Update according to your distro
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
tmux new -s session_name
```

Detach session and come back to origin shell
```txt
CTRL + b
```
