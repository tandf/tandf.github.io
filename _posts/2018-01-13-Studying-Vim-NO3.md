---

layout: post
title: Vim 学习笔记 No.3
category: Vim

---

## 打开文件

### 1 打开文件
使用`:edit {path}`打开文件，{path}可为绝对路径或相对路径。

输入`:edit %:p<TAB>`可将`%:p`替换为当前缓冲区文件的路径，而输入`:edit %:p:h<TAB>`替换得到的路径不包含文件名。此时可使用相对路径打开缓冲区文件相同目录下的文件。
<!--description-->
*书上介绍的命令统统没有`:p`部分，得到的结果全是`./`形式的，即相对目录*

将如下行加入 vimrc 文件中，可将`%%`自动替换为当前目录（不仅适用于 edit）：

    cnoremap <expr> %% getcmdtype() == ':' ? expand('%:p:h').'/' : '%%'

事实上，个人觉得有比书上介绍的这个方法更好的办法。在 vimrc 中加入这一行：

    " 打开文件时切换到文件目录
    autocmd BufEnter * silent! lcd %:p:h

这样可以直接将活动路径切换至缓冲区文件所在目录。不过书中所介绍的方法可能有不同的应用场景，故还应了解。

### 2 使用 `find` 命令打开文件
`find`命令会在 path 变量储存的路径中查找文件并打开。同样支持<TAB>键。

使用`echo &path`查看当前的 path 变量值。使用`set path+={path}`将{path}加入 path 变量。

可能这个技巧在大工程文件里比较好用吧，暂时没发现其他用处。

### 3 使用 netrw 文件管理系统
首先确保 vimrc 中有如下行：

    filetype plugin on

如果使用了 Vundle，可以发现 vimrc 中应该已经有了`filetype plugin indent on`，删去 indent 即可。

同样使用`edit {path}`命令，不过这里{path}应当使用目录而不是文件名。这时便可使用 netrw 打开文件管理窗口。如果要当前目录打开，则可使用简写`:e.`或`:E`，这两个命令分别在工作目录和活动缓冲区所在目录打开文件。

打开 netrw 后，`j` `k` 选择文件，使用<CR>打开文件或目录，使用<C-^>（非常不方便地要同时按三个键）在最近打开的目录和最近打开的文件之间切换。

用起来感觉这个文件管理系统有点像 Linux 下的 vifm 和 Windows 下 TC 配合上 vimd，不过用来代替文件管理器可能显得有些功能不足。其主要运用场景是通过网络读写文件，暂时不清楚，就当了解吧。

## 管理文件

### 1 缓存区列表
`:ls`显示缓存区列表中的文件。`:bnext``:bprev`在列表中移动，`:bfirst``:blast`跳至开头和结尾，使用`:buffer {num}`打开编号为{num}的缓冲区，编号在`:ls`中查看。也可以用`:buffer {name}`直接按名称打开缓冲区。

推荐 vim 插件 unimpaired.vim 调整映射项，如将`[b`和`]b`设置为`bnext`和`bprev`。使用 Vundle 添加插件的方法百度可得。

使用`bdelete {num}`或简写为`bd {num}`根据编号删除某个缓冲区，甚至可以用`N,Mbd`删除编号区间的缓冲区。