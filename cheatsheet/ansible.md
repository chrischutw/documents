# Ansilbe

## Install ansible in local user by python

```shell
# Install pip
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python3 get-pip.py --user

# Install ansible
python3 -m pip install --user ansible

# Upgrading ansible
python3 -m pip install --upgrade --user ansible

# Confirming your installation
## Core version
ansible --version
## Package version
python3 -m pip show ansible
```

## Install in system wide

```shell
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```

## Directory structure

```shell
# First layer
mkdir {inventory,playbooks,roles}

# Roles layer
mkdir {tasks,handlers,templates,files,vars,defaults}  

```
