# macos_zsh_configurations

## zimfw install
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