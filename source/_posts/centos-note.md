---
title: centos-note
mathjax: true
comments: true
date: 2018-10-12 16:32:01
updated: 2018-10-12 16:32:01
categories:
    - System
    - linux
tags: [linux,centos]
---

## CentOS Installation
### 制作 U 盘启动
包含了一个 setup 命令，可以配置系统的东西


给小黑安装CentOS 7的时候，遇到了很多问题，主要是使用UltraISO刻录的启动盘，在安装的过程中使用找不到磁盘，在网上找了很久，终于搞定了，做此笔记记录一下。

前提
假设你有CentOS-7-x86_64-DVD-1611.iso 文件和一个大容量U盘（我的是32G）， 
在其他linux系统中，你的U盘被挂载在/dev/sdb上

步骤
使用如下命令就可以将CentOS 7的系统刻录到U盘上

```
dd if=CentOS-7-x86_64-DVD-1611.iso of=/dev/sdb1
```

使用下面的命令查看 `dd` 的进展
```
watch -n 5 pkill -USR1 ^dd$
```

（ps： 切记此处的输出设备一定是整个U盘整体，而非该U盘上的某个分区如/dev/sdb1）
然后就可以使用该U盘启动，进行系统安装了。

#### U 盘格式恢复
（分区格式有 MBR 或者 GTP，MBR 一般是 Windows 下的，现在比较新的使用了 GPT 分区格式，同时需要 BIOS 使用 UEFI 进行 boot ）
由于经过 `dd` 命令进行制作之后，U 盘已经被分区了，有可能分区格式不是使用 MBR 格式，所以在 Windows 下找不着。
可以使用 `mkfs.ntfs -I /dev/sdb` 来将 U 盘重新格式化（使用 ntfs 的文件系统，前提是你得先安装 ntfsprogs ）
可以使用 `mkfs.vfat -I /dev/sdb` 来将 U 盘重新格式化（使用 fat32 的文件系统）

下面是一些其他命令
```
fdisk -l # 查看当前磁盘情况
vgscan # 扫描卷组
vgdisplay # 查看卷组
lvscan # 扫描逻辑卷
vgchange -ay /dev/VolGroup00 # 激活卷组 (把 /dev/xxx 改为你对应的)
mkfs -t vfat -I /dev/sdb # 格式化 (把 /dev/xxx 改为你对应的 U 盘或者其他磁盘)
```

### 输入法问题
#### 问题描述

很久以前，可能是我重装过一次 ibus 拼音输入法或者是装过其它的输入法，之后把它卸载了。总之而言，经过我的乱搞后，成功地使我的电脑出现了一个神奇的问题：每次重启电脑或者账户重新登录，我的 ibus 拼音输入法就会无效，具体表现为桌面图标显示了“中”字，但是任何输入窗口下都只能输入英文，按 Shift 也无法在 ibus 拼音输入法的中英文模式之间切换。 
这并不代表我不能输入中文，一个可笑的解决办法就是我进入设置——区域和语言，把拼音输入法删除，然后再添加，这样以来就能正常使用 ibus 拼音输入法了。然而下次重启或者重新登录后，对不起，问题依旧，需要再次重复以上操作。

#### 解决方案

我为这个问题苦恼不已，在网上查了很久也没找到合适的方法，ibus, ibus-libpinyin 不知重装了多少次，基本每次打开我的 CentOS，我都会先搜索一波重启后拼音输入法无效的问题，折腾一会儿失败了，然后在需要输入中文的时候又迫不得已地打开了设置……

终于在今天，我一如往常地想要努力一把，搜索的具体内容已经变了很多，这次我输入的大概是“CentOS 默认输入法”，我打开一篇“centos7.3安装fcitx输入法设置为默认拼音输入法”。发现了一个我从未遇到过的“im-chooser”，按照教程走下来，将 ibus 设为默认输入法。然后再重新登录之后，我惊喜地发现 ibus 拼音输入法可以正常使用了！重启电脑之后，也可以输入中文！

懂 Linux 的人也许会笑我，连 im-chooser 都不知道，这么简单的问题竟然这么长时间都搞不定。小弟的确很菜，但我还是为自己能够解决掉自己的问题而高兴，下面贴出我参考教程所作的详细步骤：

回到当前使用的普通用户，设置 ibus 输入法为默认输入系统：
```
  sudo yum install  im-chooser
  imsettings-switch ibus
```

然后注销一次用户重新登录 
命令就仅仅需要以上两步，重新登录以后，ibus 的拼音输入法就可以正常使用了。解决方案也很简单，可以说没有技术含量。 

## troubleshoot
CentOS 6.x 版本的，yum 使用了 python2 。所以不可以卸载 python2。同时安装
pythone3 后，python 命令还是指向 python2 的。此处不宜使用暴力修改（网络上许多人都说，修改 /usr/bin/yum 来实现 python3）。


