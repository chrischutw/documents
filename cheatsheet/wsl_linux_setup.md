# WSL Linux Setup

## WSL installation
```shell
# List all Linux distributions 
wsl -l -o 
# Install one of them 
wsl install -d `DistributionName`
```
ref: https://learn.microsoft.com/zh-tw/windows/wsl/install

## Install general tools
```shell
sudo apt update -y
sudo apt install vim curl wget dnsutils git ssh whois -y
```

## Change visudo editor
```shell
update-alternatives --config editor
<username> ALL=(ALL) NOPASSWD:ALL
```

## Fish
### Install Fish in Debian 11
ref: https://linuxhint.com/install_fish_shell_linux
```shell
echo 'deb http://download.opensuse.org/repositories/shells:/fish:/release:/3/Debian_11/ /' | sudo tee /etc/apt/sources.list.d/shells:fish:release:3.list
curl -fsSL https://download.opensuse.org/repositories/shells:fish:release:3/Debian_11/Release.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/shells_fish_release_3.gpg > /dev/null
sudo apt update
sudo apt install fish
```

### Install Fish in Ubuntu-22.04
ref: https://www.kwchang0831.dev/dev-env/ubuntu/fish
```shell
sudo apt-add-repository ppa:fish-shell/release-3
sudo apt update
sudo apt install fish
chsh -s $(which fish)

```

### Install fisher and plugins
```shell
curl -sL https://raw.githubusercontent.com/jorgebucaran/fisher/main/functions/fisher.fish | source && fisher install jorgebucaran/fisher
fisher install IlanCosman/tide@v5
fisher install jethrokuan/z
fisher install andreiborisov/sponge
fisher install jorgebucaran/autopair.fish
```

### Set fish alias
```shell
vim vim ~/.config/fish/config.fish
alias l='ls -al --color=always'
```

### Adjust prompt
```shell
set --universal tide_left_prompt_items pwd git newline character kubectl
```

## Install asdf 
ref: https://asdf-vm.com/guide/getting-started.html
```shell
git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.13.0
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
ref: https://blog.kyomind.tw/python-poetry

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
- https://github.com/romkatv/powerlevel10k-media/blob/master/MesloLGS%20NF%20Regular.ttf


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