---

layout: post
title: Vim学习笔记
category: Vim

---

## On A Long Way To A Better Tommorrow
**有人说：工欲善其事，必先利其器。想要做一个好的程序员，怎么能不学好vim呢？**

*<u>然后这个骗子在学vim的过程中累死了。</u>*

<!--description-->

### 1 查找模式与重复修改
使用`.`命令重复上一次的修改操作。
修改操作的定义是删除、修改、插入等。

`f{char}`会查找当前行内的下一个{char}，`;`和`,`分别是查找下一个和查找上一个。
类似的，用`\*`可以查找光标所在的单词，`N` 和 `n` 是查找上一个和下一个。

* 操作模式：查找下一个 --> 重复修改操作 --> 撤销

批量替换 `:%s/originalContent/targetContent/gc`

### 2 技巧：把修改分开 —— 常使用`<Esc>`退出编辑模式

### 3 文本对象
* `ap` 一个段落
* `s` 一个句子

### 4 插入模式下的快捷键
*前三条可用在bash shell中*

* `<C-h>` 删除前一个字母 (用处不大)
* `<C-w>` 删除至前一个单词
* `<C-u>` 删至行首
* `<C-[>` 切换到普通模式 (`Esc`太远了)
* `<C-o>` 切换到插入-普通模式 (可在插入模式中执行一个普通模式命令)
* `<C-r>{register}` 粘贴寄存器中的内容 

### 5 `zz` 重绘屏幕 将当前行置于屏幕中央

### 6 重新映射Caps Lock

- #### ubuntu
`sudo apt install gnome-tweak-tool`

搜索ctrl，并进行设置（巨easy）

tty1-6中设置方法见[ linux下键盘映射 hsnotebook的ChinaUnix博客 ](http://blog.chinaunix.net/uid-26161820-id-3425670.html)

*这里有一个坑：很多教程会指导你用xmodmap或者xkbmap。但在使用fcitx的情况下，每当切换键盘布局，比如从中文切换到英文，都会重置这个设置。努力一天没能解决这个问题。(ubuntu16)*

- #### windows
`Win + R` 输入 `regedit`
插入二进制值

``````
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Keyboard Layout]

"Scancode Map"=hex:00,00,00,00,00,00,00,00,02,00,00,00,1d,00,3a,00,00,00,00,00
``````

### 7 选择模式
选择模式中，使用`o`来切换活动端

**note**  `<C-V>` + `$` 与 `<C-V>` + `l`  移动到行尾表现不同（试试`A`插入）

