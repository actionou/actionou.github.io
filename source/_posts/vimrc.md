---
title: vimrc
date: 2020-12-01 23:26:01
tags: [vim,vimrc]
---

`pycharm`和`jupyter notebook`是我的主要coding工具，但偶尔也免不了直接在终端进行编辑，所以根据自己的需求和使用习惯对vim配置做了如下简单的调整：

```
" 使用 utf-8 编码
set encoding=utf-8

" 去掉有关vi一致性模式,避免操作习惯上的局限.
set nocompatible

" 让Backspace键可以往前删除字符.
set backspace=indent,eol,start

" 总是显示状态行
set laststatus=2

" 在状态栏显示光标的当前位置（位于哪一行哪一列）
set ruler

" 显示还没有输入完整的命令.例如yy命令,输入第一个y会在右下角显示y.
set showcmd

" 显示行号
set number

" 关闭自动折行
set nowrap

" 高亮显示匹配的括号
set showmatch

" 高亮显示所有搜索到的内容
set hlsearch

" 光标立刻跳转到搜索到内容
set incsearch

" 搜索时忽略大小写
set ignorecase

" 搜索到最后匹配的位置后,再次搜索不回到第一个匹配处
set nowrapscan

" 设置Esc超时时间为100ms,尽快生效
set ttimeout
set ttimeoutlen=100

" 高亮光标所在的行
set cursorline

" 开启语法高亮
syntax on

" 在底部显示，当前处于命令模式还是插入模式
set showmode

" 命令模式下，在底部显示，当前键入的指令。比如，键入的指令是2y3d，那么底部就会显示2y3，当键入d的时候，操作完成，显示消失
set showcmd

" 检测文件类型,并载入文件类型插件,为特定文件类型载入相关缩进文件
filetype plugin indent on

" 自动缩进
set autoindent

" 按下 Tab 键时，Vim 显示的空格数
set tabstop=2

" 在文本上按下>>（增加一级缩进）、<<（取消一级缩进）或者==（取消全部缩进）时，每一级的字符数
set shiftwidth=2

" 输入Tab字符时,自动替换成空格
set expandtab

" 设置softtabstop有一个好处是可以用Backspace键来一次删除4个空格.
" softtabstop的值为负数,会使用shiftwidth的值,两者保持一致,方便统一缩进.
set softtabstop=-1

" 使用 utf-8 编码
set encoding=utf-8

" 垂直滚动时，光标距离顶部/底部的位置（单位：行）
set scrolloff=5

" 水平滚动时，光标距离行首或行尾的位置（单位：字符）
set sidescrolloff=15

" 不创建交换文件。交换文件主要用于系统崩溃时恢复文件，文件名的开头是.、结尾是.
set noswapfile

" 出错时，不要发出响声
set noerrorbells

" 出错时，发出视觉提示，通常是屏幕闪烁
set visualbell

" Vim 需要记住多少次历史操作
set history=1000

" 打开文件监视。如果在编辑过程中文件发生外部改变（比如被别的编辑器编辑了），就会发出提示
set autoread

" 如果行尾有多余的空格（包括 Tab 键），该配置将让这些空格显示成可见的小方块
set listchars=tab:»■,trail:■
set list

" 命令模式下，底部操作指令按下 Tab 键自动补全。第一次按下 Tab，会显示所有匹配的操作指令的清单；第二次按下 Tab，会依次选择各个指令
set wildmenu
set wildmode=longest:list,full

" 启用256色
set t_Co=256

" 不创建备份文件。默认情况下，文件保存时，会额外创建一个备份文件，它的文件名是在原文件名的末尾，再添加一个波浪号（〜）
set nobackup

```
