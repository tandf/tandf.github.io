---

layout: post
title: 重装 linux 备忘录
category: linux

---

一个能用的 linux 该有的样子

<!--description-->

## 1 输入法
fcitx 加入英语、双拼，默认英语

## 2 浏览器
chrome 官网下载，登录并同步设置、书签

## 3 vim
1. 卸载 vim、vim-tiny，安装 vim-gtk
2. vimrc 相关

    2.1 拷贝 vimrc 文件

    2.2 执行 `git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim`

    2.3 vim 中执行 `:PluginInstall`

## 4 重映射 Caps Lock
执行 `sudo apt install gnome-tweak-tool` ，搜索 "ctrl" 并将 Caps Lock 设置为 LCtrl。

## 5 触摸板设置

    sudo cp /usr/share/X11/xorg.conf.d/70-synaptics.conf /etc/X11/xorg.conf.d/70-synaptics.conf

末尾添加：

    # My Configurations
    Section "InputClass"
        Identifier "touchpad"
        Driver "synaptics"
        MatchIsTouchpad "on"
            Option "SoftButtonAreas" "0 0 0 0 50% 0 82% 0"
            Option "LockedDrags" "1"
            Option "PalmDetect" "1"
    EndSection

功能：触摸板右键设置为鼠标中键、拖拽选择、手掌检测

*手掌检测部分效果不算好，应该可以进一步设置，暂未研究*

[ 参考 ](https://wiki.archlinux.org/index.php/Touchpad_Synaptics#Configuration)

## 6 安装 ss-qt
略

## 7 设置 vim 默认打开文件
打开默认配置文件

    sudo vim /etc/gnome/defaults.list

复制含有 gedit 的行

    qaq
    :%g/gedit/y A
    :let @+=@a

其中 qaq 用于清空寄存器。将更改加入用户配置文件

    sudo vim /.local/share/applications/mimeapps.list

粘贴默认打开方式，并用 vim 替换 gedit

    +"p
    :%s/gedit.desktop/vim.desktop/g
