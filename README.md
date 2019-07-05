GavinZheng的博客备份

GavinZheng的vim配置：

```
set t_Co=256  
set nocompatible

colorscheme evening
set nu
syntax enable
syntax on
set mouse=a 
set autoindent
set cindent
set tabstop=4
set shiftwidth=4
set nowrap
func Compile( )
    w
    exec "! g++  % -o %< -g -Wall"
endf

func Run( )
    exec "! ./%<"
endf

func Debug( )
    exec "! gdb --tui %<"
endf

nnoremap <F7> :call Compile()<CR>
nnoremap <F8> :call Run()<CR>
nnoremap <F9> :call Debug()<CR>
```

