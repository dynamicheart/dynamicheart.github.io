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

{% asset_img rendering.png This is an redering image %}

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

# Show Line Number And Set Tab Size

Create a custom configuration file.
```bash
touch ~/.vim_runtime/my_configs.vim
```

and add a new line:
```
set number
set tabstop 2
```

[reference](https://stackoverflow.com/questions/1878974/redefine-tab-as-4-spaces)

# Choose A More Suitable Theme

Add below lines to your my_configs.vim file.

```
let g:solarized_termcolors=256
syntax enable
set background=light
colorscheme solarized
```

However, the color display is weird. The solution is to add two more lines
```
set term=screen-256color
set t_ut=
```

The relavent github issue is [here](https://github.com/Microsoft/BashOnWindows/issues/1706)

# Show NERDTree on Startup

Add below to your my_configs.vim file:

```
autocmd vimenter * NERDTree
```

Besides, you can map a Ctrl+n to open NERDTree by do below:
```
map <C-n> :NERDTreeToggle<CR>
```

Start with nerdtree open but main window focused:
```
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
- Install [YouCompleteMe](https://github.com/Valloric/YouCompleteMe)

# Some Tricks When Installing vim-airline
Uninstall lightline.vim first by do below on terminal:
```bash
rm -rf ~/.vim_runtime/sources_non_forked/lightline.vim
```
And add below to you ~/.vimrc file:
```
" vim-airline 
" https://github.com/vim-airline/vim-airline
let g:airline#extensions#tabline#enabled = 1
let g:airline_powerline_fonts = 1

" Incompatible powerline symbols for Inziu Iosevka font
" https://github.com/vim-airline/vim-airline/wiki/FAQ
if !exists('g:airline_symbols')
  let g:airline_symbols = {}
endif
let g:airline_symbols.linenr = " " 
" https://github.com/vim-airline/vim-airline/issues/1397
let g:airline_symbols.whitespace = " "
```
and ~/.vim_runtime/my_configs.vim:

```
set number
set tabstop=2

" Fix display bug
" https://github.com/Microsoft/BashOnWindows/issues/1706
set term=screen-256color
set t_ut=

" Color theme
" https://github.com/altercation/vim-colors-solarized
let g:solarized_termcolors=256
syntax enable
set background=light
colorscheme solarized

" NERDtree
" https://github.com/scrooloose/nerdtree
autocmd vimenter * NERDTree
autocmd vimenter * wincmd p
map <C-n> :NERDTreeToggle<CR>
autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTree") && b:NERDTree.isTabTree()) | q | endif
```

Then enter vim and run:
```
:AirlineTheme <theme>
```

I prefer solarized.

[Here](https://gist.github.com/dynamicheart/bbdd0bc18494a41969006f9edf20e3f3) are the complete configuration files.
