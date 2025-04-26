---
id: amos6ttzgyo7gwihos8e3om
title: Workspace
desc: ''
updated: 1709306218220
created: 1666165646092
---

# YADM

* https://yadm.io/

When you live in a command line, configurations are a deeply personal thing. They are often crafted over years of experience, battles lost, lessons learned, advice followed, and ingenuity rewarded. When you are away from your own configurations, you are an orphaned refugee in unfamiliar and hostile surroundings. You feel clumsy and out of sorts. You are filled with a sense of longing to be back in a place you know. A place you built. A place where all the short-cuts have been worn bare by your own travels. A place you proudly call… $HOME. 

https://github.com/vorago/dotfiles

# Zsh

* https://ohmyz.sh/
* https://github.com/Vorago/dotfiles/blob/mint/packages/zsh/install.sh

# Shell prompt

* https://starship.rs/

# Desktop workspace

## Ubuntu packages

```
sudo apt install -y \
       variety \
       baobab \
       curl \
       jq \
       guake
```

## Snap packages

```
sudo snap install chromium
sudo snap install dbeaver-ce
sudo snap install deezer-unofficial-player
sudo snap install drawio
sudo snap install flameshot 
sudo snap install gimp
sudo snap install shadowsocks-electron
sudo snap install shadowsocks-rust
sudo snap install slack
sudo snap install telegram-desktop

sudo snap install code-insiders --classic 
sudo snap install code --classic 
sudo snap install codium --classic 
sudo snap install kubectl --classic 
```

## Flatpak packages

```
flatpak install -y com.github.tchx84.Flatseal
```

## Other tools

### NeoVim

[[editor.neovim]]

### Enpass

* https://www.enpass.io/downloads/
* https://www.enpass.io/support/kb/general/how-to-install-enpass-on-linux/

```
echo "deb https://apt.enpass.io/ stable main" | sudo tee /etc/apt/sources.list.d/enpass.list
wget -O - https://apt.enpass.io/keys/enpass-linux.key | sudo tee /etc/apt/trusted.gpg.d/enpass.asc
sudo apt update
sudo apt install enpass
```

### Dropbox

```
https://www.dropbox.com/download?dl=packages/ubuntu/dropbox_2020.03.04_amd64.deb
```

### MegaSync

https://mega.io/desktop#downloadapps

#### Ubuntu 22.04

```
curl -LO https://mega.nz/linux/repo/xUbuntu_22.04/amd64/megasync-xUbuntu_22.04_amd64.deb
sudo sudo apt install ./megasync-xUbuntu_22.04_amd64.deb
rm megasync-xUbuntu_22.04_amd64.deb
```

### OpenLens

https://github.com/MuhammedKalkan/OpenLens/releases

```
openlens_version=6.2.5
curl -sLO https://github.com/MuhammedKalkan/OpenLens/releases/download/v${openlens_version}/OpenLens-${openlens_version}.amd64.deb
sudo dpkg -i OpenLens-${openlens_version}.amd64.deb
rm OpenLens-${openlens_version}.amd64.deb
```

### AWS cli

[[aws]]

### Docker

[[containers.docker]]

### Podman


### Pritunl

* https://client.pritunl.com/#install

```
sudo tee /etc/apt/sources.list.d/pritunl.list << EOF
deb https://repo.pritunl.com/stable/apt jammy main
EOF

sudo apt --assume-yes install gnupg
gpg --keyserver hkp://keyserver.ubuntu.com --recv-keys 7568D9BB55FF9E5287D586017AE645C0CF8E292A
gpg --armor --export 7568D9BB55FF9E5287D586017AE645C0CF8E292A | sudo tee /etc/apt/trusted.gpg.d/pritunl.asc
sudo apt update
sudo apt install pritunl-client-electron
```