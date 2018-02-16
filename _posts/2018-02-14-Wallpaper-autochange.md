---

layout: post
title: Ubuntu 自动切换壁纸
category: linux

---

Windows 下很容易就能实现多壁纸自动切换，那 Linux 下能不能呢？答案当然是肯定的。根据网上的资料，我拼凑出了一份脚本。

<!--description-->

**注：本文所述方法在 Ubuntu 16.04 下实验有效，理论上适用于使用 Gnome 桌面系统的所有情况**

主要原理及方法见 [博客园一篇文章](http://www.cnblogs.com/youxia/p/linux030.html "让 Ubuntu 桌面自动更换壁纸 - 京山游侠 - 博客园") 。重要的东西这篇文章里都讲完了，我只能狗尾续貂一下。

自动切换壁纸主要由两个 XML 文件控制，一个是 `/usr/share/backgrounds/contest/\*.XML` ，主要作用是定义某个壁纸切换方案，格式可以参见此目录下任意文件。另一个是 `/usr/share/gnome-background-properties/xenial-wallpapers.XML` ，这个文件定义了哪些壁纸（或者壁纸切换方案）会在系统设置中显示出来。

## 使用脚本生成第一个 XML 文件的内容

<small> （好像大部分都是抄的） </small>

参考原有文件可发现，第一个 XML 文件无非两个主要模块：某壁纸静态时间、此壁纸切换动画及切换时长，使用 bash 脚本可以减少大量工作。

一下脚本用于生成第一个 XML 文件的内容：

    # WallpaperAutoChange.sh
    #!/bin/bash
    read -p 'input delay time for every picture (10 by default) : ' static_time
    if [[ $static_time == '' ]] ; then
        static_time=10
    fi
    read -p 'input change time (0.7 by default) : ' transition_time
    if [[ $transition_time == '' ]] ; then
        transition_time=0.7
    fi

    url='/usr/share/backgrounds/'

    echo "<background>"
    echo "  <starttime>"
    echo "    <year>2009</year>"
    echo "    <month>08</month>"
    echo "    <day>04</day>"
    echo "    <hour>00</hour>"
    echo "    <minute>00</minute>"
    echo "    <second>00</second>"
    echo "  </starttime>"

    files=`ls -U $url | grep -v contest`
    last_file=''

    for current_file in $files; do
        if [[ $last_file == '' ]] ; then
            last_file=$current_file
            first_file=$last_file
            echo "  <static>"
            echo "    <duration>$static_time</duration>"
            echo "    <file>$url$last_file</file>"
            echo "  </static>"
        else
            echo "  <transition>"
            echo "    <duration>$transition_time</duration>"
            echo "    <from>$url$last_file</from>"
            echo "    <to>$url$current_file</to>"
            echo "  </transition>"
            echo "  <static>"
            echo "    <duration>$static_time</duration>"
            echo "    <file>$url$current_file</file>"
            echo "  </static>"
            last_file=$current_file
        fi
    done
        echo "  <transition>"
        echo "    <duration>$transition_time</duration>"
        echo "    <from>$url$current_file</from>"
        echo "    <to>$url$first_file</to>"
        echo "  </transition>"

    echo "</background>"

值得注意的部分：

- `#!/bin/bash` 也即 shebang ，用于指定运行脚本的程序
- 运行时会要求输入图片的静态时间、和切换时间，默认值为 10 秒和 0.7 秒
- 程序将搜索 `/usr/share/backgrounds/` 下的所有文件并加入 XML 文件中

## 将 WallpaperAutoChange.sh 输出的内容写入 XML 文件

<small> （这一脚本自己写的成分稍多一点） </small>

    #!/bin/bash

    if [ ! -f /usr/share/backgrounds/contest/autochange.xml ] ; then
        sudo touch /usr/share/backgrounds/contest/autochange.xml
        sudo chmod =666 /usr/share/backgrounds/contest/autochange.xml
    fi

    . WallpaperAutoChange.sh > /usr/share/backgrounds/contest/autochange.xml

- 由于 `/usr/share/backgrounds/contest/` 目录的权限设置，一般用户无权新建，故当不存在目标 XML 文件时，需使用 `sudo` 命令新建文件。但如此一来新建的文件所有者将为 root ，根据系统的文件默认权限不同，可能会使得每次更改此文件都需要输入密码。这个文件并不是什么关键文件，因此将其权限改为所有用户可读取、修改。

- `if [ ! -f filename ] ` 用于检测是否不存在目标文件。其中 `!` 代表不满足条件，`-f filename` 代表存在且为文件。注意中括号两旁必须为空格。

## 修改第二个 XML 文件

在文件中加入：

    <wallpaper>
         <name>auto change</name>
         <filename>/usr/share/backgrounds/contest/autochange.xml</filename>
     </wallpaper>

## 将脚本放入用户脚本目录
到现在为止，将两个脚本放在同一目录，并指定目录运行脚本，就可以完成 XML 文件更新的功能。

为了能在任意目录执行更新脚本命令，也为了妥善放置脚本，应当将脚本放置在 `/usr/local/bin` 目录。这个目录是专门用于放置用户脚本的，并且默认在 PATH 路径中（执行命令时 bath 会到 PATH 路径中查找命令。可通过 `echo $PATH`查看 PATH 路径）。

观察 PATH 路径可发现，还有一个 `/usr/local/sbin` 目录，这个目录用于存放系统命令，通常只有管理员可运行。此外，还有 `/usr/bin` `/usr/sbin` `/bin` `/sbin` 目录，其中前两个通常为用户安装脚本，后两个用于存放重要的系统命令。

至此，直接输入 `generate_wallpaper_autochange.sh` 即可更新壁纸切换文件。
