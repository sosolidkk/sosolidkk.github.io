---
layout: post
title: Dotfiles and config
date: 2022-02-14 23:04 -0300
tags: config
---
So, it's not big news that more often than not we need to wipe our workstations due to a large number of files already being created or just for the sake of doing so. My biggest problem with re-installing my Linux distro is the time that takes to configure everything all over again. So, instead of googling everything, I've chosen to register most of the programs that I need to install after a fresh wipe of the OS and how I deal with the configuration of all of them and some of my `dotfiles` for an easier copy and paste ~~for future wipes~~.

#### **Programs list**
- [zsh](https://github.com/zsh-users/zsh)
- [oh-my-zsh](https://ohmyz.sh/)
- [asdf](http://asdf-vm.com/)
- [vscode](https://code.visualstudio.com/)
- [1password](https://1password.com/pt/)
- [postgres](https://www.postgresql.org/)
- terminal

Just to make sure we have everything we'll need before hand, run this:

```sh
$ sudo pacman-mirrors --fasttrack && sudo pacman -Syyu
$ sudo pacman -S base base-devel git yay lm_sensors
$ yay -S google-chrome visual-studio-code-bin
```

After you system is updated, let's install the remaining tools.

---
#### **Zsh**
Zsh is a shell designed for interactive use, although it is also a powerful scripting language. Many of the useful features of `bash`, `ksh`, and `tcsh` were incorporated into zsh; many original features were added.

```sh
$ sudo pacman -S zsh
$ chsh -s $(which zsh)
$ echo $SHELL
```

#### **Oh my zsh**
I install this because it's easier to setup some themes on my shell.

`sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`

#### **SpaceShip**
Spaceship is a minimalistic, powerful and extremely customizable zsh prompt. It combines everything you may need for convenient work, without unnecessary complications, like a real spaceship.

```sh
git clone https://github.com/spaceship-prompt/spaceship-prompt.git "$ZSH_CUSTOM/themes/spaceship-prompt" --depth=1

ln -s "$ZSH_CUSTOM/themes/spaceship-prompt/spaceship.zsh-theme" "$ZSH_CUSTOM/themes/spaceship.zsh-theme"

# Set ZSH_THEME="spaceship" in your .zshrc.
```

#### **Zinit**
Zinit is a flexible and fast Zshell plugin manager that will allow you to install everything from GitHub and other sites.

```sh
bash -c "$(curl --fail --show-error --silent --location https://raw.githubusercontent.com/zdharma-continuum/zinit/HEAD/scripts/install.sh)"

# Add the next three lines after this part: '### End of ZInit's installer chunk'
zinit light zdharma/fast-syntax-highlighting
zinit light zsh-users/zsh-autosuggestions
zinit light zsh-users/zsh-completions
```

#### **Github SSH**

```sh
$ ssh-keygen -t ed25519 -C "your_email@example.com"
$ eval "$(ssh-agent -s)"
$ ssh-add ~/.ssh/id_ed25519

# Then just 'cat' the id_ed25519.pub and add it in the github ssh settings
```

#### **VSCode**
Visual Studio Code is pretty straight forward. You just need to click on the `Login to Sync config` option and it's pretty much all done after you logged in successfully.

---
#### **Dotfiles**
> .zshrc

```sh
# export PATH=$HOME/bin:/usr/local/bin:$PATH
. $HOME/.asdf/asdf.sh

export ZSH="$HOME/.oh-my-zsh"
ZSH_THEME="spaceship"
plugins=(git)
source $ZSH/oh-my-zsh.sh

# User configuration
export MANPATH="/usr/local/man:$MANPATH"
export LANG=en_US.UTF-8

# Preferred editor for local and remote sessions
if [[ -n $SSH_CONNECTION ]]; then
  export EDITOR='vim'
else
  export EDITOR='nvim'
fi

alias zshconfig="code ~/.zshrc"
alias ohmyzsh="code ~/.oh-my-zsh"

SPACESHIP_PROMPT_ORDER=(
  user          # Username section
  dir           # Current directory section
  host          # Hostname section
  git           # Git section (git_branch + git_status)
  exec_time     # Execution time
  vi_mode       # Vi-mode indicator
  jobs          # Background jobs indicator
  exit_code     # Exit code section
  char          # Prompt character
)
SPACESHIP_USER_SHOW=always
SPACESHIP_PROMPT_ADD_NEWLINE=false
SPACESHIP_CHAR_SYMBOL="$"
SPACESHIP_CHAR_SUFFIX=" "

### Added by Zinit's installer
if [[ ! -f $HOME/.local/share/zinit/zinit.git/zinit.zsh ]]; then
    print -P "%F{33} %F{220}Installing %F{33}ZDHARMA-CONTINUUM%F{220} Initiative Plugin Manager (%F{33}zdharma-continuum/zinit%F{220})…%f"
    command mkdir -p "$HOME/.local/share/zinit" && command chmod g-rwX "$HOME/.local/share/zinit"
    command git clone https://github.com/zdharma-continuum/zinit "$HOME/.local/share/zinit/zinit.git" && \
        print -P "%F{33} %F{34}Installation successful.%f%b" || \
        print -P "%F{160} The clone has failed.%f%b"
fi

source "$HOME/.local/share/zinit/zinit.git/zinit.zsh"
autoload -Uz _zinit
(( ${+_comps} )) && _comps[zinit]=_zinit

# Load a few important annexes, without Turbo
# (this is currently required for annexes)
zinit light-mode for \
    zdharma-continuum/zinit-annex-as-monitor \
    zdharma-continuum/zinit-annex-bin-gem-node \
    zdharma-continuum/zinit-annex-patch-dl \
    zdharma-continuum/zinit-annex-rust

### End of Zinit's installer chunk
zinit light zdharma/fast-syntax-highlighting
zinit light zsh-users/zsh-autosuggestions
zinit light zsh-users/zsh-completions
```

> `settings.json (vscode)`

```json
{
    "workbench.tree.indent": 12,
    "files.exclude": {
        "**/*.pyc": {
            "when": "$(basename).py"
        },
        "**/__pycache__": true,
    },
    "workbench.iconTheme": "vscode-icons",
    "workbench.editorAssociations": {
        "*.ipynb": "jupyter-notebook"
    },
    // Python rules
    "python.linting.pylintEnabled": false,
    "python.linting.flake8Enabled": true,
    "python.linting.enabled": true,
    // Python formatting rules
    "python.formatting.provider": "black",
    "python.formatting.blackArgs": [
        "--line-length",
        "120"
    ],
    "python.linting.flake8Args": [
        "--max-line-length=120",
        "--ignore=E402,F841,E302,E305",
    ],
    // Editor font
    "editor.fontFamily": "JetBrains Mono",
    "editor.fontSize": 14,
    "editor.fontLigatures": true,
    // Editor Rules
    "editor.rulers": [
        88,
        121
    ],
    "editor.formatOnSave": true,
    "editor.tabSize": 4,
    "[ruby]": {
        "editor.tabSize": 2
    },
    // HTML Rules
    "[html]": {
        "editor.formatOnSave": false
    },
    "html.format.wrapLineLength": 0,
    "html.format.wrapAttributes": "aligned-multiple",
    "html.format.indentHandlebars": true,
    "html.format.preserveNewLines": false,
    "workbench.colorTheme": "Dracula",
    // Terminal Rules
    "terminal.integrated.fontSize": 14,
    "terminal.integrated.lineHeight": 1.1,
    "terminal.integrated.fontFamily": "NotoSansMono Nerd Font",
    "terminal.integrated.fontWeightBold": "normal",
    "terminal.integrated.tabs.enabled": true,
    "editor.renderWhitespace": "trailing",
    "files.maxMemoryForLargeFilesMB": 8192,
    "python.defaultInterpreterPath": "/bin/python",
    "notebook.cellToolbarLocation": {
        "default": "right",
        "jupyter-notebook": "left"
    },
    "solargraph.diagnostics": true,
    "vscode-pets.position": "explorer",
    "vscode-pets.theme": "forest",
    "vscode-pets.petSize": "medium",
    "vscode-pets.petType": "rubber-duck",
    "security.workspace.trust.untrustedFiles": "open",
}
```
