---

layout: post
title: 重装 linux 备忘录
category: linux

---

一个能用的 linux 该有的样子

<!--description-->

** 注 : Ubuntu 16.04 系统，带图形化界面 **

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
    sudo vim /etc/X11/xorg.conf.d/70-synaptics.conf

末尾添加：

    # My Configurations
    Section "InputClass"
        Identifier "touchpad"
        Driver "synaptics"
        MatchIsTouchpad "on"
            Option "SoftButtonAreas" "0 0 0 0 50% 0 82% 0"
            Option "LockedDrags" "1"
            Option "PalmDetect" "1"
            Option "PalmMinWidth" "1"
            Option "PalmMinz" "1"
    EndSection

功能：触摸板右键设置为鼠标中键、拖拽选择、手掌检测

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

## 8 Ubuntu 双系统时间同步
Windows 电脑下装 Ubuntu 双系统后，会出现 Windows 时间出错的问题。查资料后发现这是因为两种系统管理时间方式不同，具体原理见参考资料。解决办法很简单，在 Ubuntu 系统中运行：

    sudo timedatectl set-local-rtc 1

即可解决问题。

[参考资料](https://www.jianshu.com/p/34b486e9fe75 "解决 Windows 与 Ubuntu 双系统时间同步问题 - 简书")

## 9 有关中文版 Ubuntu
如果安装了中文版 Ubuntu ，home 路径下的文件夹名称都是“下载”“文档”这样的中文名称，不便于在命令行中定位。

打开终端，在终端中输入命令：

        export LANG=en_US

        xdg-user-dirs-gtk-update

跳出对话框询问是否将目录转化为英文路径，同意并关闭。
在终端中输入命令：

        export LANG=zh_CN


关闭终端，并重起。 下次进入系统，系统会提示是否把转化好的目录改回中文。 选择不再提示，并取消修改。 主目录的中文转英文就完成了~

[参考资料](https://blog.csdn.net/l0605020112/article/details/20285239)
