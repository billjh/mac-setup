# Mac Setup (Big Sur)

Steps to setup my new development environment on a Mac.

## System Preferences

Disable guest account
- Users & Groups > Guest User > Untick "Allow guests to log in to this computer"

Trackpad
- Trackpad > Point & Click > Tap to click
- Accessibility > Pointer Control > Mouse & Trackpad > Trackpad Options > Tick "Enable dragging - three finger drag"

Dock
- Dock & Menu Bar > Dock > Untick "Show recent applications in Dock"

Menu Bar
- Show Bluetooth
- Show Airdrop
- Hide Spotlight
- Clock > Tick "Display time with seconds"

Enable desktop file stacks
- Right click at desktop > Click "Use Stacks"

## Finder Preferences

- General > New Finder Windows show > Change to "billjh" (home directory)
- Sidebar > Remove unwanted entries like "Recents" and "Tags"
- Sidebar > Add my home directory "billjh" and "Projects"
- Advanced > Tick "Show all filename extensions"

## Chrome Browser

1. Open Safari and download Chrome for Mac. 
1. Open Chrome and confirm to set as system default browser.
1. Install LastPass for Chrome extension and login. 
1. Login my Google account and sync settings. 
1. Setup a few extensions in pop-up tabs.
1. Keep Chrome in Dock and remove Safari from Dock.

## Homebrew

Open Terminal and run the [setup script](https://brew.sh/).

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## iTerm2 + zsh + Oh My Zsh + Powerlevel10k

![iterm2_screenshot.png](/iterm2_screenshot.png)

Install iTerm2 from homebrew. Open iTerm2 and keep in dock.

```bash
brew install --cask iterm2
```

Download a `.itermcolors` color scheme file from [here](https://iterm2colorschemes.com/) ([github](https://github.com/mbadolato/iTerm2-Color-Schemes)) and apply to iTerm2 profile.
- iTerm2 Preferences > Profiles > Default > Color Presets > Import
- eg. [Elementary.itermcolors](https://raw.githubusercontent.com/mbadolato/iTerm2-Color-Schemes/master/schemes/Elementary.itermcolors)

Install Zsh from homebrew and [set as default shell](https://stackoverflow.com/a/44549662). Restart iTerm2 when needed.

```bash
brew install zsh
which zsh # expect /usr/local/bin/zsh
sudo sh -c "echo $(which zsh) >> /etc/shells"
chsh -s $(which zsh)
```

Install [Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh#basic-installation)

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

Fix folder permission "Insecure completion-dependent directories detected" by [this comment](https://github.com/ohmyzsh/ohmyzsh/issues/6835#issuecomment-390187157)

```bash
chmod 755 /usr/local/share/zsh
chmod 755 /usr/local/share/zsh/site-functions
```
Install [Powerlevel10k](https://github.com/romkatv/powerlevel10k#oh-my-zsh) theme for Oh My Zsh

```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

Set `ZSH_THEME="powerlevel10k/powerlevel10k"` in `~/.zshrc`.

Restart iTerm2 and configure Powerlevel10k theme.

```bash
p10k configure
```

Install [zsh-completions](https://github.com/zsh-users/zsh-completions#oh-my-zsh) plugin.

```bash
git clone https://github.com/zsh-users/zsh-completions ${ZSH_CUSTOM:=~/.oh-my-zsh/custom}/plugins/zsh-completions
```

Install [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions/blob/master/INSTALL.md#oh-my-zsh) plugin.

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

Install [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/INSTALL.md#oh-my-zsh) plugin.

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

Fix paste slowness with https://github.com/zsh-users/zsh-autosuggestions/issues/238#issuecomment-389324292.

```bash
# This speeds up pasting w/ autosuggest
# https://github.com/zsh-users/zsh-autosuggestions/issues/238
pasteinit() {
  OLD_SELF_INSERT=${${(s.:.)widgets[self-insert]}[2,3]}
  zle -N self-insert url-quote-magic # I wonder if you'd need `.url-quote-magic`?
}

pastefinish() {
  zle -N self-insert $OLD_SELF_INSERT
}
zstyle :bracketed-paste-magic paste-init pasteinit
zstyle :bracketed-paste-magic paste-finish pastefinish
```

Clear up the `~/.zshrc` file. It should look like

```bash
# ~/.zshrc

if [[ -r "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh" ]]; then
  source "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh"
fi

export ZSH="/Users/billjh/.oh-my-zsh"

ZSH_THEME="powerlevel10k/powerlevel10k"

plugins=(
  git
  golang
  zsh-completions
  zsh-autosuggestions
  zsh-syntax-highlighting
)

autoload -U compinit && compinit

source $ZSH/oh-my-zsh.sh

[[ ! -f ~/.p10k.zsh ]] || source ~/.p10k.zsh

# source <(kubectl completion zsh)

# This speeds up pasting w/ autosuggest
# https://github.com/zsh-users/zsh-autosuggestions/issues/238
pasteinit() {
  OLD_SELF_INSERT=${${(s.:.)widgets[self-insert]}[2,3]}
  zle -N self-insert url-quote-magic # I wonder if you'd need `.url-quote-magic`?
}

pastefinish() {
  zle -N self-insert $OLD_SELF_INSERT
}
zstyle :bracketed-paste-magic paste-init pasteinit
zstyle :bracketed-paste-magic paste-finish pastefinish
```

## Git and SSH key

Replace system git with homebrew one, and install git-lts.

```bash
brew install git
brew install git-lts
```

Follow the Github guides for [creating new SSH key](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) and [adding SSH key to Github account](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account).

Config git user indentity.

```bash
git config --global user.email "bill.jiangh@gmail.com"
git config --global user.name "Huan Jiang"
```

Set git pull with fast-forward only by default.

```bash
git config pull.ff only --global
```

## Visual Studio Code

Install Visual Studio Code and turn on settings sync by login to Github.

```bash
brew install --cask visual-studio-code
```

Install [Fira Code](https://github.com/tonsky/FiraCode/wiki/Installing#macos) font.

```bash
brew tap homebrew/cask-fonts
brew install --cask font-fira-code
```

## Docker Desktop and Kubernetes

Install docker desktop for mac.

```bash
brew install --cask docker
```

Enable the local Kubernetes cluster.
- Preferences > Kubernetes > Tick "Enable Kubernetes"

Enable kubectl zsh autocompletion by adding `source <(kubectl completion zsh)` to `~/.zshrc`.

Install [tilt](https://docs.tilt.dev/install.html#macos).

```bash
curl -fsSL https://raw.githubusercontent.com/tilt-dev/tilt/master/scripts/install.sh | bash
```

# Go

Install go.

```bash
brew install go
```

Go completions is included in zsh [golang](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/golang) plugin.
