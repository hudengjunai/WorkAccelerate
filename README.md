# vim_runtime
主vim配置
在https://github.com/amix/vimrc.git 基础上配置
# vim 安装  
## 裸机安装  
1.安装教程连接 [link](https://superuser.com/questions/162560/how-to-install-vim-on-linux-when-i-dont-have-root-permissions)  
2.在安装配置configure时候注意配置安装路径和interpreter python 和python config 的版本。
```
LDFLAGS=-L$HOME/usr/local/lib ./configure \
    --with-features=huge \
    --with-tlib=ncurses \
    --enable-multibyte \
    --enable-rubyinterp=yes \
    --enable-pythoninterp=yes \
    --enable-python3interp=yes \
    --with-python-command=/online/home/dengjun.hdj/.conda/envs/py2/bin/python2.7 \
    --with-python-config-dir=/home/dengjun.hdj/.conda/envs/py2/bin/python2-config \
    --with-python3-command=/online/home/dengjun.hdj/.conda/envs/tf2/bin/python3.6 \
    --with-python3-config-dir=/online/home/dengjun.hdj/.conda/envs/tf2/lib/python3.6/config-3.6m-x86_64-linux-gnu \
    --enable-luainterp=yes \
    --enable-gui=gtk2 \
    --enable-cscope \
    --prefix=$HOME/usr/local
```

## anconda 安装（推荐方式）  
1.使用anaconda 安装conda install -c conda-forge vim 
2.安装neovim配置   conda install -c conda-forge neovim

# vim 配置  
1.继承配置https://github.com/amix/vimrc.git 下的基础配置。
2.F5/F10基础命令配置. 
```
 autocmd VimEnter * NERDTree | wincmd p
 autocmd BufWinEnter * NERDTreeMirror
 let g:NERDTreeWinPos = "left"
 autocmd BufEnter * if (winnr("$") == 1 && exists("b:NERDTree") && b:NERDTree.isTabTree()) | q | endif

 nnoremap <silent> <F10> :call PdbProgram()<CR>
 func! PdbProgram()
     exec "w"
     if &filetype == 'python'
         exec "!python -m ipdb %"
     elseif &filetype == 'java'
         exec "!javac -g -encoding UTF-8 % && jdb %:t:r"
     elseif &filetype=='pandoc'
         exec "!pandoc % |lynx -stdin"
         exec "redraw"
     endif
 endfunc

 nnoremap <F5> :call RunProgram()<CR>
 func! RunProgram()
     exec "w"
     if &filetype == 'python'
         exec "!time python %"
     elseif &filetype == 'java'
         echo "the java file"
         exec "!javac -encoding UTF-8 % && java %:t:r"
     elseif &filetype=='pandoc'
         exec "!pandoc % |lynx -stdin"
         exec "redraw"
     else
         echo "the unrecognized type"
     endif
 endfunc

 let g:indent_guides_enable_on_vim_startup = 1
 nmap <F8> :TagbarToggle<CR>

 let vim_markdown_preview_hotkey='<C-m>'
 let vim_markdown_preview_browser='Google Chrome'
 let vim_markdown_preview_temp_file=1
 let vim_markdown_preview_pandoc=1
 
 
```

## 额外配置的插件功能
```
YouCompleteMe        asyncrun.vim         jedi-vim             supertab             tagbar               vim-fugitive         vim-indent-guides    vim-pandoc
Zenburn              calendar.vim         powerline            tabulous             vim-airline          vim-gutentags        vim-markdown-preview
```

## 关于vim的典型应用。
1.Python/Shell/C++/Java编辑器 IDE   
2.markdown编辑器  
3.Latex Editor   
4.terminal vim8.1 supported  
5.lynx pandoc vim composed   

## tips  
* 尽量将所有工作能在一个环境中完成，比如无论在HPC，mac平台，均可以使用相同工作环境完成工作，便于迁移。  
* 配置尽量统一，可以将一部分配置放置在docker中。  
