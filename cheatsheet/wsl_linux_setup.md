# WSL Linux Setup

## WSL installation

```shell
# List all Linux distributions 
wsl -l -o 
# Install one of them 
wsl --install -d `DistributionName`
```

ref: <https://learn.microsoft.com/zh-tw/windows/wsl/install>

## Install general tools

```shell
sudo apt update -y
sudo apt install vim curl wget dnsutils git ssh whois tmux -y
```

## Change visudo editor

```shell
update-alternatives --config editor
<username> ALL=(ALL) NOPASSWD:ALL
```

## Fish

### Install Fish in Debian 11

ref: <https://linuxhint.com/install_fish_shell_linux>

```shell
echo 'deb http://download.opensuse.org/repositories/shells:/fish:/release:/3/Debian_11/ /' | sudo tee /etc/apt/sources.list.d/shells:fish:release:3.list
curl -fsSL https://download.opensuse.org/repositories/shells:fish:release:3/Debian_11/Release.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/shells_fish_release_3.gpg > /dev/null
sudo apt update -y &&\
sudo apt install fish =-y
```

### Install Fish in Ubuntu-22.04

ref: <https://www.kwchang0831.dev/dev-env/ubuntu/fish>

```shell
sudo apt-add-repository ppa:fish-shell/release-3 &&\
sudo apt update -y &&\
sudo apt install fish -y &&\
chsh -s $(which fish)
```

### Install fisher and plugins

```shell
curl -sL https://raw.githubusercontent.com/jorgebucaran/fisher/main/functions/fisher.fish | source && fisher install jorgebucaran/fisher
fisher install IlanCosman/tide@v6
fisher install jethrokuan/z
fisher install andreiborisov/sponge
fisher install jorgebucaran/autopair.fish
```

### tide configuration

```shell
tide configure
```

### Install fzf

```shell
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install
```

### Install fd, bat, fzf.fish

```shell
# Install fd
sudo apt install fd-find -y
sudo ln -s $(which fdfind)/usr/local/bin/fd
# Install bat
sudo apt install bat -y
sudo ln -s $(which batcat) /usr/local/bin/bat
# Install fzf
fisher install PatrickF1/fzf.fish
```

### show fzf key bindings

```shell
fzf_configure_bindings --help
```

| COMMAND            |  DEFAULT KEY SEQUENCE         |  CORRESPONDING OPTION  |
| ------------------ | ----------------------------- | ---------------------- |
| Search Directory   |  Ctrl+Alt+F (F for file)      |  --directory           |
| Search Git Log     |  Ctrl+Alt+L (L for log)       |  --git_log             |
| Search Git Status  |  Ctrl+Alt+S (S for status)    |  --git_status          |
| Search History     |  Ctrl+R     (R for reverse)   |  --history             |
| Search Processes   |  Ctrl+Alt+P (P for process)   |  --processes           |
| Search Variables   |  Ctrl+V     (V for variable)  |  --variables           |

### Set fish alias

```shell
vim ~/.config/fish/config.fish
alias l='ls -al --color=always'
```

### Adjust prompt

```shell
set --universal tide_left_prompt_items pwd git newline character kubectl
```

### Install asdf

ref: <https://asdf-vm.com/guide/getting-started.html>

```shell
git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.14.0
echo "source ~/.asdf/asdf.fish" >> ~/.config/fish/config.fish
mkdir -p ~/.config/fish/completions; and ln -s ~/.asdf/completions/asdf.fish ~/.config/fish/completions
```

## Install poetry

```shell
# Install
curl -sSL https://install.python-poetry.org | python3 -

# autocompletion
poetry completions fish > ~/.config/fish/completions/poetry.fish

# configure venv inside project
poetry config virtualenvs.in-project true
```

ref: <https://blog.kyomind.tw/python-poetry>

## Vscode

```shell
# Terminal font: setting.json 
"terminal.integrated.fontFamily": "MesloLGS NF"
```

## Install zim

- install zim

```shell
curl -fsSL https://raw.githubusercontent.com/zimfw/install/master/install.zsh | zsh
```

## download powerline font

- <https://github.com/romkatv/powerlevel10k-media/blob/master/MesloLGS%20NF%20Regular.ttf>

## install module (powerlevel10k as example)

```shell
cat <<EOF >> ~/.zshrc
export TERM="xterm-256color"

if [ -f ~/.bash_aliases ]; then
    . ~/.bash_aliases
fi
EOF

cat <<EOF >> ~/.zimrc
# my install modules
zmodule romkatv/powerlevel10k"
EOF

zimfw install
```

- initial configuration

```shell
p10k configure
```

## install latest k9s

ref: <https://github.com/derailed/k9s?tab=readme-ov-file#installation>

```shell
# Linux
curl -sS https://webinstall.dev/k9s | bash
# Windows
curl.exe -A MS https://webinstall.dev/k9s | powershell
```

## Istall gcloud CLI

ref: <https://cloud.google.com/sdk/docs/install#deb>

```shell
sudo apt-get install apt-transport-https ca-certificates gnupg curl sudo -y
curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg
echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list
sudo apt-get update -y && sudo apt-get install google-cloud-cli -y 
```

## Install helm

ref: <https://helm.sh/docs/intro/install/>

```shell
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

## Github authentication

```shell
# Install github CLI
sudo apt install gh

# Create token and grant all permission for repo and read permission for the others
https://github.com/settings/tokens

# Login by token
gh auth login
```
