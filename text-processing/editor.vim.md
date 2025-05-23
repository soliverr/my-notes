---
id: d85wooqm16563wv6jgt6m29
title: Vim
desc: ''
updated: 1677841729074
created: 1629706368987
---
Vim
===

## Vim as IDE

* [Vim as an IDE, not the text editor](https://blog.ghaiklor.com/2019/10/12/vim-as-an-ide-not-the-text-editor/)

### [SpaceVim](https://spacevim.org/)

<https://spacevim.org/asynchronous-plugin-manager/>

> *SpaceVim use dein as default plugin manager, and implement a UI for dein.*

#### Install

##### Linux

```
$ curl -sLf https://spacevim.org/install.sh | bash

[✔] Successfully clone SpaceVim
[✔] BackUp /home/oliver/.vim to /home/oliver/.vim_back
[✔] Installed SpaceVim for vim
[✔] BackUp /home/oliver/.config/nvim to /home/oliver/.config/nvim_back
[✔] Installed SpaceVim for neovim
[➭] Install dein.vim
Клонирование в «/home/oliver/.cache/vimfiles/repos/github.com/Shougo/dein.vim»…
remote: Enumerating objects: 33, done.
remote: Counting objects: 100% (33/33), done.
remote: Compressing objects: 100% (19/19), done.
remote: Total 5955 (delta 12), reused 24 (delta 10), pack-reused 5922
Получение объектов: 100% (5955/5955), 1.17 MiB | 2.61 MiB/s, готово.
Определение изменений: 100% (3380/3380), готово.
[✔] dein.vim installation done
[➭] Downloading DejaVu Sans Mono Bold Oblique for Powerline.ttf
[✔] Downloaded DejaVu Sans Mono Bold Oblique for Powerline.ttf
[➭] Downloading DejaVu Sans Mono Bold for Powerline.ttf
[✔] Downloaded DejaVu Sans Mono Bold for Powerline.ttf
[➭] Downloading DejaVu Sans Mono Oblique for Powerline.ttf
[✔] Downloaded DejaVu Sans Mono Oblique for Powerline.ttf
[➭] Downloading DejaVu Sans Mono for Powerline.ttf
[✔] Downloaded DejaVu Sans Mono for Powerline.ttf
[➭] Downloading DroidSansMonoForPowerlinePlusNerdFileTypesMono.otf
[✔] Downloaded DroidSansMonoForPowerlinePlusNerdFileTypesMono.otf
[➭] Downloading Ubuntu Mono derivative Powerline Nerd Font Complete.ttf
[✔] Downloaded Ubuntu Mono derivative Powerline Nerd Font Complete.ttf
[➭] Downloading WEBDINGS.TTF
[✔] Downloaded WEBDINGS.TTF
[➭] Downloading WINGDNG2.ttf
[✔] Downloaded WINGDNG2.ttf
[➭] Downloading WINGDNG3.ttf
[✔] Downloaded WINGDNG3.ttf
[➭] Downloading devicons.ttf
[✔] Downloaded devicons.ttf
[➭] Downloading mtextra.ttf
[✔] Downloaded mtextra.ttf
[➭] Downloading symbol.ttf
[✔] Downloaded symbol.ttf
[➭] Downloading wingding.ttf
[✔] Downloaded wingding.ttf
[➭] Updating font cache, please wait ...
[✔] font cache done!

Almost done!
==============================================================================
==    Open Vim or Neovim and it will install the plugins automatically      ==
==============================================================================

That's it. Thanks for installing SpaceVim. Enjoy!
```

#### Vimproc

If you got a vimproc error like:

```
[vimproc] vimproc's DLL: "~/.SpaceVim/bundle/vimproc.vim/lib/vimproc_linux64.so" is not found.
```

Please read :help vimproc and make it, you may need to install make (from build-essential) and a C compilator (like gcc) to build the dll (see issue #435 and #544).

https://github.com/Shougo/vimproc.vim

If you use dein.vim, you can update and build vimproc automatically. This is the recommended package manager.

```
call dein#add('Shougo/vimproc.vim', {'build' : 'make'})
```

Для nvim это нужно прописать в *init.vim* <https://gist.github.com/telamon/8d1f79e52cb8b1d7871c8dd5a089c37d>

`~/.config/nvim/init.vim`:


## NeoVim

[[editor.neovim]]

## Plugin manager

* [NeoBundle](https://github.com/Shougo/neobundle.vim) - больше не поддерживается
* [Dein](https://github.com/Shougo/dein.vim)
* [Pathogen](https://github.com/tpope/vim-pathogen)


## Полезные плагины

* [vim-table-mode](https://github.com/dhruvasagar/vim-table-mode)
