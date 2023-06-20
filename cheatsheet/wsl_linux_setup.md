# WSL Linux Setup

## WSL install Debian
```shell
wsl install -d debian
```

## Install general tools
```shell
sudo apt update -y
sudo apt install vim curl wget dnsutils -y
```

## Change visudo editor
```shell
update-alternatives --config editor
<username> ALL=(ALL) NOPASSWD:ALL
```

## Install Fish
```shell
echo 'deb http://download.opensuse.org/repositories/shells:/fish:/release:/3/Debian_11/ /' | sudo tee /etc/apt/sources.list.d/shells:fish:release:3.list
curl -fsSL https://download.opensuse.org/repositories/shells:fish:release:3/Debian_11/Release.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/shells_fish_release_3.gpg > /dev/null
sudo apt update
sudo apt install fish
# Install fisher and plugins
curl -sL https://raw.githubusercontent.com/jorgebucaran/fisher/main/functions/fisher.fish | source && fisher install jorgebucaran/fisher
fisher install IlanCosman/tide@v5
fisher install jethrokuan/z
fisher install andreiborisov/sponge
fisher install jorgebucaran/autopair.fish
```
ref: https://linuxhint.com/install_fish_shell_linux/
ref: https://www.kwchang0831.dev/dev-env/ubuntu/fish

## Set fish alias
```shell
vim vim ~/.config/fish/config.fish
alias l='ls -al --color=always'

```




## Install zim
- install zim
```shell
curl -fsSL https://raw.githubusercontent.com/zimfw/install/master/install.zsh | zsh
```

## download powerline font(MesloLGS NF as example)
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