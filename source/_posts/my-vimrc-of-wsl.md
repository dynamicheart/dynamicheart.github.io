---
title: My vimrc of WSL
date: 2017-10-16 16:36:26
tags: 
  - tutorial
  - ubuntu-on-windows 
  - vimrc
---

# Overview

Recently, I have been using ubuntu-on-windows for a while. It's very convenient and I don't need to install dual system any more. However, sometimes it's not that same as a native linux system when configurating some softwares. This article gives a detailed instruction of how to configure a good-looking, useful and powerful vim editor.

<!--more-->

# Choose An Awesome Font
I use [Inziu Iosevka](https://be5invis.github.io/Iosevka/inziu.html), which can display both English and Chinises well. If you have trouble in modifying the font of your bash, you may expect a solution [here](https://www.zhihu.com/question/36344262/answer/67191917), which is given by the author of the font.

# Use Auto Configuration Tool

Fortunately, somebody has made an awesome tool to help us do the most configurations. Just do the two commands blow:

```bash
git clone --depth=1 https://github.com/amix/vimrc.git ~/.vim_runtime
sh ~/.vim_runtime/install_awesome_vimrc.sh
```

[Git Repository](https://github.com/amix/vimrc)

# Show Line Number

Create a custom configuration file.
```bash
torch ~/.vim_runtime/my_configs.vim
```

and add a new line:
```
set number
```

# Choose A More Suitable Theme

Add below lines to your my_configs.vim file.

```bash
let g:solarized_termcolors=256
syntax enable
set background=light
colorscheme solarized
```

However, the color display is weird. The solution is to add two more lines
```bash
set term=screen-256color
set t_ut=
```

The relavent github issue is [here](https://github.com/Microsoft/BashOnWindows/issues/1706)

# Use vim-airline
Do these commands on terminal:
```bash
cd ~/.vim_runtime/sources_non_forked
git clone git@github.com:vim-airline/vim-airline.git
git clone git@github.com:vim-airline/vim-airline-themes.git
```
Then enter vim and run :Helptags to generate help tags.

# Show NERDTree on Startup

Add below to your my_configs.vim file:

```bash
autocmd vimenter * NERDTree
```

Besides, you can map a Ctrl+n to open NERDTree by do below:
```bash
map <C-n> :NERDTreeToggle<CR>
```

Start with nerdtree open but main window focused:
```bash
autocmd vimenter * wincmd p
```

Close vim if the only window left open is a NERDTree
```
autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTree") && b:NERDTree.isTabTree()) | q | endif
```

# Install Custom Plugins
The auto configuration tool does not include some famous plugins, such as YouCompleteMe. So we have to add these plugins manually.
- Install [vundle](https://github.com/VundleVim/Vundle.vim)
- Install [vim-airline](https://github.com/vim-airline/vim-airline)
- Install [vim-airline-theme](https://github.com/vim-airline/vim-airline-themes)
