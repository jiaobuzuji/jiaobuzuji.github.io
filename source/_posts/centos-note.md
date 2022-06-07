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

包含了一个 setup 命令，可以配置系统的东西
同时还有 systemd 命令
还有一组 system 的命令可以对系统进行配置。

centos7 使用 systemctl 来代替 ckconfig 与 service 。
systemctl [start|stop|status|enable|disable|restart....] 具体查看帮助文件

## CentOS Installation
### 制作 U 盘启动

给小黑安装CentOS 7的时候，遇到了很多问题，主要是使用UltraISO刻录的启动盘，在安装的过程中使用找不到磁盘，在网上找了很久，终于搞定了，做此笔记记录一下。

前提
假设你有CentOS-7-x86_64-DVD-1611.iso 文件和一个大容量U盘（我的是32G）， 
在其他linux系统中，你的U盘被挂载在/dev/sdb上

步骤
使用如下命令就可以将CentOS 7的系统刻录到U盘上(假设你的U盘为 /dev/sdb)，操作中可能需要 sudo 权限

```
umount /dev/sdb
mkfs.ntfs -I --fast /dev/sdb   # mkfs.ntfs -I /dev/sdb (low-level formatting)
dd if=CentOS-7-x86_64-DVD-1611.iso of=/dev/sdb
```

使用下面的命令查看 `dd` 的进展
```
watch -n 5 pkill -USR1 ^dd$
```

（ps： 切记此处的输出设备一定是整个U盘整体，而非该U盘上的某个分区如/dev/sdb1）
然后就可以使用该U盘启动，进行系统安装了。

### 不小心将 U 盘弄成了 ISO9660 的光盘格式
由于 ISO9660 光盘格式是只读的。所以无法写U盘。此时使用 fdisk 无论怎么分区，格式化，都没有办法被 windows 读取与使用 U 盘。
使用 udisksctl info --block-device /dev/sdc 可以看到 IdType 为 iso9660
#### 为什么会整成 ISO9660 呢？
因为 ISO 文件本身就是这种 ISO9660 格式。dd 命令只是 Byte to Byte 地拷贝过去。因为我们是使用了 `/dev/sdb` 这样一整个 U 盘，所以U盘格式都变了。
```
dd if=CentOS-7-x86_64-DVD-1611.iso of=/dev/sdb
```
#### 怎样修复呢？
解铃还须系铃人！！
```
touch removeiso.txt
dd if=removeiso.txt of=/dev/sdb
```


### Minimal wifi 联网
安装好 Minimal 的系统后，默认只安装了 NetworkManager，而并没有安装 NetworkManager-wifi（可能是因为考虑到充当Server，不需要wifi吧）。所以是无法使用 wifi 设备的。
正确的安装方式：
1. 将上一步制作好的 RockyLinux Minimal 启动盘（U盘） 插入电脑。
1. 'fdisk -l' 确保设备名（如 /dev/sdb）
1. 假如U盘的设备名为 /dev/sdb。然后就可以骚操作
```
su root
cd ~; mkdir udisk
mount /dev/sdb1 udisk
cd /etc/yum.repos.d/
sed -e 's|^mirrorlist=|#mirrorlist=|g' \
    -e 's|^#baseurl=.*|baseurl=file:///root/udisk/BaseOS/|g' \
    -i.bak \
    /etc/yum.repos.d/Rocky-BaseOS.repo
dnf --repo=baseos -y install NetworkManager-wifi dhcp
systemctl restart NetworkManager
```
1. 之后`nmtui`或者可以使用 `nmcli dev wifi connect WIFI名称 password WIFI密码 wep-key-type key ifname wlp5s0` 来连接网络了


### U 盘格式恢复
（分区格式有 MBR 或者 GTP，MBR 一般是 Windows 下的，现在比较新的使用了 GPT 分区格式，同时需要 BIOS 使用 UEFI 进行 boot ）
由于经过 `dd` 命令进行制作之后，U 盘已经被分区了，有可能分区格式不是使用 MBR 格式，所以在 Windows 下找不着。
可以使用 `mkfs.ntfs -I --fast /dev/sdb` 来将 U 盘重新格式化（使用 ntfs 的文件系统，前提是你得先安装 ntfsprogs ）
可以使用 `mkfs.vfat -I /dev/sdb` 来将 U 盘重新格式化（使用 fat32 的文件系统，**不推荐！！！**）

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
  sudo yum install  im-chooser imsettings-xim
  imsettings-switch ibus
```

然后注销一次用户重新登录 
命令就仅仅需要以上两步，重新登录以后，ibus 的拼音输入法就可以正常使用了。解决方案也很简单，可以说没有技术含量。 

###配置Fcitx为默认输入法
方法一：（推荐此法！）

1. 新建配置文件 vim /etc/X11/xinit/xinput.d/fcitx.conf ，内容为：

XIM=fcitx
XIM_PROGRAM=/usr/local/bin/fcitx 确保此文件存在
XIM_ARGS=”-D” 此处为大写D

SHORT_DESC="Fcitx"
GTK_IM_MODULE=xim
QT_IM_MODULE =xim

2. 然后在/etc/alternatives/目录下，将符号链接xinputrc删除，重新建一个：

mv /etc/alternatives/xinputrc /etc/alternatives/xinputrc.bak
rm –rf /etc/alternatives/xinputrc
ln -s /etc/X11/xinit/xinput.d/fcitx.conf /etc/alternatives/xinputrc

注：如果你使用的桌面是英文环境的，还需要在使用用户的用户目录下.bashrc配置文件里添加如下内容：
export LANG=”zh_CN.UTF-8″
export LC_CTYPE=”zh_CN.UTF-8″
export XIM=fcitx
export XIM_PROGRAM=fcitx
export GTK_IM_MODULE=xim
export XMODIFIERS=”@im=fcitx”

在系统，首选项输入法中将fcitx设置为默认输入法，注销重新登录

注意：如果你的输入法安装了，但是又不能按ctrl+space杂办，是因为你还缺少啦一个库文件
yum install gtk3-immodule-xim
安装好就可以使用啦

## troubleshoot
CentOS 6.x 版本的，yum 使用了 python2 。所以不可以卸载 python2。同时安装
pythone3 后，python 命令还是指向 python2 的。此处不宜使用暴力修改（网络上许多人都说，修改 /usr/bin/yum 来实现 python3）。

## chkconfig：
    chkconfig命令主要用来更新（启动或停止）和查询系统服务的运行级信息。谨记chkconfig不是立即自动禁止或激活一个服务，它只是简单的改变了符号连接。
语法：
    chkconfig --list [name]
    chkconfig --add name
    chkconfig --del name
    chkconfig [--level levels] name <on|off|reset>
    chkconfig [--level levels] name

    chkconfig 没有参数运行时，显示用法。如果加上服务名，那么就检查这个服务是否在当前运行级启动。如果是，返回true，否则返回false。如果在服务名后面指定了on，off或者reset，那么chkconfi 会改变指定服务的启动信息。on和off分别指服务被启动和停止，reset指重置服务的启动信息，无论有问题的初始化脚本指定了什么。on和off开关，系统默认只对运行级3，4，5有效，但是reset可以对所有运行级有效。
    --level选项可以指定要查看的运行级而不一定是当前运行级。
    需要说明的是，对于每个运行级，只能有一个启动脚本或者停止脚本。当切换运行级时，init不会重新启动已经启动的服务，也不会再次去停止已经停止的服务。

    chkconfig --list ：显示所有运行级系统服务的运行状态信息（on或off）。如果指定了name，那么只显示指定的服务在不同运行级的状态。
    chkconfig --add name：增加一项新的服务。chkconfig确保每个运行级有一项启动(S)或者杀死(K)入口。如有缺少，则会从缺省的init脚本自动建立。
    chkconfig --del name：删除服务，并把相关符号连接从/etc/rc[0-6].d删除。
    chkconfig [--level levels] name <on|off|reset>：设置某一服务在指定的运行级是被启动，停止还是重置。例如，要在3，4，5运行级停止nfs服务，则命令如下：
    chkconfig --level 345 nfs off
运行级文件：
    每个被chkconfig管理的服务需要在对应的init.d下的脚本加上两行或者更多行的注释。第一行告诉chkconfig缺省启动的运行级以及启动和停止的优先级。如果某服务缺省不在任何运行级启动，那么使用 - 代替运行级。第二行对服务进行描述，可以用\ 跨行注释。
例如，random.init包含三行：
# chkconfig: 2345 20 80
# description: Saves and restores system entropy pool for \
# higher quality random number generation.

附加介绍一下Linux系统的运行级的概念：
    Linux中有多种运行级，常见的就是多用户的2，3，4，5 ，很多人知道5是运行X-Windows的级别，而0就是关机了。运行级的改变可以通过init命令来切换。例如，假设你要维护系统进入单用户状态，那么，可以使用init1来切换。在Linux的运行级的切换过程中，系统会自动寻找对应运行级的目录/etc/rc[0-6].d下的K和S开头的文件，按后面的数字顺序，执行这些脚本。对这些脚本的维护，是很繁琐的一件事情，Linux提供了chkconfig命令用来更新和查询不同运行级上的系统服务。


## 基本配置
timedatectl set-ntp true
fdisk /dev/sda

ip addr
ip link
ip link set wlp5s0 up 
iwlist wlp5s0 scan | grep ESSID
wpa_passphrase 网络 密码 > wifi.conf
wpa_supplicant -c wifi.conf -i wlp5s0 
dhcpcd


## U盘 Recover 系统
将安装 Linux 的 U盘插入电脑，重启电脑选择U盘启动
选择安装界面的 `Troubleshooting` 选项。然后进入 `Rescue a Rocky Linux system`。此时就会运行U盘上的系统。同时将硬盘上的系统挂载在 /mnt/sysroot 目录下
使用 `chroot /mnt/sysroot` 就可以以 root 身份进入硬盘上的系统
