+++
categories = ["linux"]
date = "2021-11-17"
description = ""
featured = ""
featuredalt = ""
featuredpath = ""
linktitle = ""
slug = "covert-hiec-to-jpg-in-ubuntu"
title = "Convert HEIC to JPG in Ubuntu"
type = ["posts","post"]
[ author ]
  name = "Captain Snowball"
+++

## My Fresh Linux Installation Setup
After installing a Linux sysytem I make many changes to ensure maximum productivity and ensuring it is ready for development, this page contains list of everything

- Installing zsh, tmux: `sudo apt install zsh tmux`
- Installing oh my zsh
- Installing oh my tmux in tmux
- Installing Fira Code fonts
- Installing Insomnia as Flatpak
- Install nvm for NodeJS
- Installing thefuck zsh plugin and thefuck
- Installing zsh-autosuggestions plugin
- Adding helpers
- Setting paths


### Adding thefuck to ZSH
Add these lines to .zshrc in the beginning (Beginning part is important)
```bash
pip install --user thefuck
```
```
export PATH=$PATH:$HOME/.local/bin # For TheFuck
...
plugins=(command-not-found git thefuck zsh-autosuggestions)
```

### Adding ZSH Hook for NVM
```
autoload -U add-zsh-hook
load-nvmrc() {
  local node_version="$(nvm version)"
  local nvmrc_path="$(nvm_find_nvmrc)"

  if [ -n "$nvmrc_path" ]; then
    local nvmrc_node_version=$(nvm version "$(cat "${nvmrc_path}")")

    if [ "$nvmrc_node_version" = "N/A" ]; then
      nvm install
    elif [ "$nvmrc_node_version" != "$node_version" ]; then
      nvm use
    fi
  elif [ "$node_version" != "$(nvm version default)" ]; then
    echo "Reverting to nvm default version"
    nvm use default
  fi
}
add-zsh-hook chpwd load-nvmrc
load-nvmrc
```

### Adding helpers
```
how_in()
{
  where="$1"; shift
  IFS=+ curl "https://cht.sh/$where/$*"
}
```


### Setting paths for custom installed programs
```bash
# Generic
INSTALL_DIR=~/.Installation

# Android
export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
export ANDROID_HOME=$INSTALL_DIR/Android/SDK
export ANDROID_SDK_ROOT=$ANDROID_HOME
PATH=$INSTALL_DIR/Android/SDK/platform-tools:$PATH
PATH=$PATH:$ANDROID_HOME/bin

# Flutter
PATH=$PATH:$INSTALL_DIR/Flutter_SDK/bin

# PHP Stuff
PATH=$PATH:$INSTALL_DIR/Composer
PATH=$PATH:~/.config/composer/vendor/bin
```