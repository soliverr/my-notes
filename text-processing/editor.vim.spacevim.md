---
id: ye6gdixdg4kfa1ailvt6f5o
title: Spacevim
desc: 'SpaceVim use dein as default plugin manager, and implement a UI for dein'
updated: 1618476714220
created: 1617608671228
---

# SpaceVim

[SpaceVim](https://spacevim.org/)

<https://spacevim.org/asynchronous-plugin-manager/>

> *SpaceVim use dein as default plugin manager, and implement a UI for dein.*

## Install

### Linux

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

## Layers

### shell

[shell layer](https://spacevim.org/layers/shell/)

#### Configuration

```sh
[[layers]]
  name = "shell"
  default_position = "top"
  default_height = 30
```

#### Key bindings

|Key Binding  | Description                              |
| ----------- | ---------------------------------------- |
| SPC '       | Open or switch to the terminal windows   |
| Ctrl-d      | Close terminal windows in terminal mode  |
| q           | Hide terminal windows in Normal mode     |
| <Esc>       | Switch to Normal mode from terminal mode |
| Ctrl-Left   | Switch to the windows on the left        |
| Ctrl-Down   | Switch to the windows below              |
| Ctrl-Up     | Switch to the windows on the top         |
| Ctrl-Right  | Switch to the windows on the right       |

### git

[git layer](https://spacevim.org/layers/git/)

#### Configuration

`git_plugin`: default value is `git`, available values include: `gina`, `fugitive`, `gita`, `git`.

if you want to use fugitive instead:

```sh
[[layers]]
  name = "git"
  git_plugin = 'fugitive'
```

#### Key bindings

|Key Binding  | Description                              |
| ----------- | ---------------------------------------- |
|SPC g s      | view git status                          |
|SPC g S 	    | stage current file                       |
|SPC g U 	    | unstage current file                     |
|SPC g c 	    | edit git commit                          |
|SPC g p      | git push                                 |
|SPC g m 	    | git branch manager                       |
|SPC g d 	    | view git diff                            |
|SPC g A 	    | stage all files                          |
|SPC g b 	    | open git blame windows                   |
|SPC g h a 	  | stage current hunk                       |
|SPC g h r 	  | undo cursor hunk                         |
|SPC g h v 	  | preview cursor hunk                      |
