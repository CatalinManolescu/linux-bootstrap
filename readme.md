# setup new linux box

Debian setup

## create user

```bash
su
useradd -m -s /bin/bash catalin
```

## install and configure sudo

```bash
su
apt install sudo
usermod -aG sudo catalin
```

## enable non-free apps

Edit `/etc/apt/sources.list` and add `non-free` to the end of all lines.

## setup ssh server

```bash
sudo apt install openssh-server
```

Add ssh key to `~/.ssh/authorized_keys`.

Disable SSH password login by setting `PasswordAuthentication no` in `/etc/ssh/sshd_config`.

## install apps

```bash
sudo apt update
sudo apt install \
    apt-transport-https ca-certificates gnupg2 software-properties-common \
    curl nmap rsync joe vim mc tmux git
```
