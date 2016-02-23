Vim Tutor
=============

lighlighting for Python
------------------------
1. download python.vim, copy into <vim location>/<$VIMRUNTINE>/syntax or ~/.vim/syntax

2. edit ~/.vimrc file,like below:
```shell
syntax enable
syntax on
set filetype=python
au BufNewFile,BufRead *.py,*.pyw setf python

```

indent
-----------------------
edit ~/.vimrc like below :

```shell

set autoindent " same level indent
set smartindent " next level indent
set expandtab
set tabstop=4
set shiftwidth=4
set softtabstop=4

```

line number
----------------------
set nu

view 
---------------------
1. install ctags
    sudo apt-get install ctags
    or
    download source from http://ctags.sourceforge.net/

2. edit vimrc, set ctags location

```shell

let Tlist_Ctags_Cmd='/usr/bin/ctags'

```

