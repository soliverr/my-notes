---
id: jvkq6t7yjuzxde9257nvhep
title: Neovim
desc: ''
updated: 1718209856220
created: 1677841223774
---

# Installing

* https://github.com/neovim/neovim/wiki/Installing-Neovim
    * https://github.com/neovim/neovim/releases/tag/stable

## Ubuntu/Debian

**Stable**:

```
culr -LO https://github.com/neovim/neovim/releases/download/stable/nvim-linux64.deb
apt install ./nvim-linux64.deb
```

## Pre-build archives

```
curl -LO https://github.com/neovim/neovim/releases/latest/download/nvim-linux64.tar.gz
sudo rm -rf /opt/nvim
sudo tar -C /opt -xzf nvim-linux64.tar.gz
```
