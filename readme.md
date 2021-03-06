# set up new linux box

Debian box setup

Additional config:

- [development box](dev.md)

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

## set up ssh server

```bash
sudo apt install openssh-server
```

Add ssh key to `~/.ssh/authorized_keys`.

Disable SSH password login by setting `PasswordAuthentication no` in `/etc/ssh/sshd_config`.

## set up ssh key

```bash
ssh-keygen -t ed25519 -a 100
```

*the `-a` option specifies the number of KDF (key derivation function) rounds used.  Higher numbers result in slower passphrase verification and increased resistance to brute-force password cracking (should the keys be stolen).*

## install apps

```bash
sudo apt update
sudo apt install \
    apt-transport-https lsb-release \
    ca-certificates gnupg2 dirmngr software-properties-common \
    curl jq nmap rsync joe vim mc tmux git build-essential xml-twig-tools\
    vnstat htop ifmetric lm-sensors bmon tcptrack \
    xclip xsel
```

## install dotfiles

```bash
cd ~
git clone git@github.com:CatalinManolescu/dotfiles.git .dotfiles
cd .dotfiles
./install
```
