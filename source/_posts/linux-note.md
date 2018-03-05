---
title: linux-note
mathjax: true
comments: true
date: 2018-02-06 16:57:33
updated: 2018-02-06 16:57:33
categories:
    - System
    - linux
tags: [linux,ubuntu,centos,shell]
---

## 前言
目前使用到的 Linux 系统主要是 ubuntu 与 centos 系统。

## ssh

```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

https://www.ibm.com/developerworks/cn/linux/l-cn-sshforward/

linux ssh_config和sshd_config配置文件
 
ssh_config和sshd_config都是ssh服务器的配置文件，二者区别在于，前者是针对客户端的配置文件，后者则是针对服务端的配置文件。两个配置文件都允许你通过设置不同的选项来改变客户端程序的运行方式。下面列出来的是两个配置文件中最重要的一些关键词，每一行为“关键词&值”的形式，其中“关键词”是忽略大小写的。
1、编辑 /etc/ssh/ssh_config 文件
 
# Site-wide defaults for various options
   Host *
        ForwardAgent no
        ForwardX11 no
        RhostsAuthentication no
        RhostsRSAAuthentication no
        RSAAuthentication yes
        PasswordAuthentication yes
        FallBackToRsh no
        UseRsh no
        BatchMode no
        CheckHostIP yes
        StrictHostKeyChecking no
        IdentityFile ~/.ssh/identity
        Port 22
        Cipher blowfish
        EscapeChar ~

 
下面对上述选项参数逐进行解释：
# Site-wide defaults for various options
带“#”表示该句为注释不起作，该句不属于配置文件原文，意在说明下面选项均为系统初始默认的选项。说明一下，实际配置文件中也有很多选项前面加有“#”注释，虽然表示不起作用，其实是说明此为系统默认的初始化设置。
Host *
"Host"只对匹配后面字串的计算机有效，“*”表示所有的计算机。从该项格式前置一些可以看出，这是一个类似于全局的选项，表示下面缩进的选项都适用于该设置，可以指定某计算机替换*号使下面选项只针对该算机器生效。
ForwardAgent no
"ForwardAgent"设置连接是否经过验证代理（如果存在）转发给远程计算机。
ForwardX11 no
"ForwardX11"设置X11连接是否被自动重定向到安全的通道和显示集（DISPLAY set）。
RhostsAuthentication no
"RhostsAuthentication"设置是否使用基于rhosts的安全验证。
RhostsRSAAuthentication no
"RhostsRSAAuthentication"设置是否使用用RSA算法的基于rhosts的安全验证。
RSAAuthentication yes
"RSAAuthentication"设置是否使用RSA算法进行安全验证。
PasswordAuthentication yes
"PasswordAuthentication"设置是否使用口令验证。
FallBackToRsh no
"FallBackToRsh"设置如果用ssh连接出现错误是否自动使用rsh，由于rsh并不安全，所以此选项应当设置为"no"。
UseRsh no
"UseRsh"设置是否在这台计算机上使用"rlogin/rsh"，原因同上，设为"no"。
BatchMode no
"BatchMode"：批处理模式，一般设为"no"；如果设为"yes"，交互式输入口令的提示将被禁止，这个选项对脚本文件和批处理任务十分有用。
CheckHostIP yes
"CheckHostIP"设置ssh是否查看连接到服务器的主机的IP地址以防止DNS欺骗。建议设置为"yes"。
StrictHostKeyChecking no
"StrictHostKeyChecking"如果设为"yes"，ssh将不会自动把计算机的密匙加入"$HOME/.ssh/known_hosts"文件，且一旦计算机的密匙发生了变化，就拒绝连接。
IdentityFile ~/.ssh/identity
"IdentityFile"设置读取用户的RSA安全验证标识。
Port 22
"Port"设置连接到远程主机的端口，ssh默认端口为22。
Cipher blowfish
“Cipher”设置加密用的密钥，blowfish可以自己随意设置。
EscapeChar ~
“EscapeChar”设置escape字符。
2、编辑 /etc/ssh/sshd_config 文件：‍

 
# This is ssh server systemwide configuration file.
          Port 22
          ListenAddress 192.168.1.1
          HostKey /etc/ssh/ssh_host_key
          ServerKeyBits 1024
          LoginGraceTime 600
          KeyRegenerationInterval 3600
          PermitRootLogin no
          IgnoreRhosts yes
          IgnoreUserKnownHosts yes
          StrictModes yes
          X11Forwarding no
          PrintMotd yes
          SyslogFacility AUTH
          LogLevel INFO
          RhostsAuthentication no
          RhostsRSAAuthentication no
          RSAAuthentication yes
          PasswordAuthentication yes
          PermitEmptyPasswords no
          AllowUsers admin

 

 
‍下面逐行说明上面的选项设置：
Port 22
"Port"设置sshd监听的端口号。
ListenAddress 192.168.1.1
"ListenAddress”设置sshd服务器绑定的IP地址。
HostKey /etc/ssh/ssh_host_key
"HostKey”设置包含计算机私人密匙的文件。
ServerKeyBits 1024
"ServerKeyBits”定义服务器密匙的位数。
LoginGraceTime 600
"LoginGraceTime”设置如果用户不能成功登录，在切断连接之前服务器需要等待的时间（以秒为单位）。
KeyRegenerationInterval 3600
"KeyRegenerationInterval”设置在多少秒之后自动重新生成服务器的密匙（如果使用密匙）。重新生成密匙是为了防止用盗用的密匙解密被截获的信息。
PermitRootLogin no
"PermitRootLogin”设置是否允许root通过ssh登录。这个选项从安全角度来讲应设成"no"。
IgnoreRhosts yes
"IgnoreRhosts”设置验证的时候是否使用“rhosts”和“shosts”文件。
IgnoreUserKnownHosts yes
"IgnoreUserKnownHosts”设置ssh daemon是否在进行RhostsRSAAuthentication安全验证的时候忽略用户的"$HOME/.ssh/known_hosts”
StrictModes yes
"StrictModes”设置ssh在接收登录请求之前是否检查用户家目录和rhosts文件的权限和所有权。这通常是必要的，因为新手经常会把自己的目录和文件设成任何人都有写权限。
X11Forwarding no
"X11Forwarding”设置是否允许X11转发。
PrintMotd yes
"PrintMotd”设置sshd是否在用户登录的时候显示“/etc/motd”中的信息。
SyslogFacility AUTH
"SyslogFacility”设置在记录来自sshd的消息的时候，是否给出“facility code”。
LogLevel INFO
"LogLevel”设置记录sshd日志消息的层次。INFO是一个好的选择。查看sshd的man帮助页，已获取更多的信息。
RhostsAuthentication no
"RhostsAuthentication”设置只用rhosts或“/etc/hosts.equiv”进行安全验证是否已经足够了。
RhostsRSAAuthentication no
"RhostsRSA”设置是否允许用rhosts或“/etc/hosts.equiv”加上RSA进行安全验证。
RSAAuthentication yes
"RSAAuthentication”设置是否允许只有RSA安全验证。
PasswordAuthentication yes
"PasswordAuthentication”设置是否允许口令验证。
PermitEmptyPasswords no
"PermitEmptyPasswords”设置是否允许用口令为空的帐号登录。
AllowUsers admin
"AllowUsers”的后面可以跟任意的数量的用户名的匹配串，这些字符串用空格隔开。主机名可以是域名或IP地址。
 

通常情况下我们在连接 OpenSSH服务器的时候假如 UseDNS选项是打开的话，服务器会先根据客户端的 IP地址进行 DNS PTR反向查询出客户端的主机名，然后根据查询出的客户端主机名进行DNS正向A记录查询，并验证是否与原始 IP地址一致，通过此种措施来防止客户端欺骗。平时我们都是动态 IP不会有PTR记录，所以打开此选项也没有太多作用。我们可以通过关闭此功能来提高连接 OpenSSH 服务器的速度。

服务端步骤如下： 
编辑配置文件 /etc/ssh/sshd_config 
vim /etc/ssh/sshd_config 
找到 UseDNS选项，如果没有注释，将其注释 
#UseDNS yes 
添加 
UseDNS no

找到 GSSAPIAuthentication选项，如果没有注释，将其注释 
#GSSAPIAuthentication yes 
添加 
GSSAPIAuthentication no

保存配置文件

重启 OpenSSH服务器 
/etc/init.d/sshd restart

Putty server refused our key的三种原因和解决方法 
1、.ssh文件夹权限错
.ssh 以及其父文件夹（root为/root，普通用户为Home目录）都应该设置为只有该用户可写（比如700）。

以下为原因：

ssh服务器的key方式登录对权限要求严格。对于客户端: 私钥必须为600权限或者更严格权限(400), 一旦其他用户可读, 私钥就不起作用(如640), 表现为系统认为不存在私钥
对于服务器端: 要求必须公钥其他用户不可写, 一旦其他用户可写(如660), 就无法用key登录, 表现为:Permission denied (publickey).
同时要求.ssh目录其他用户不可写,一旦其他用户可写(如770), 就无法使用key登录, 表现为:Permission denied (publickey).
```
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

2、SElinux导致

密钥文件不能通过SElinux认证，解决方法如下：
1
	
# restorecon -R -v /home #root用户为/root

我遇到的就是这种情况，找了好久还找到是这个原因，因为是新装的虚拟机，SElinux还没关闭。

这篇博文详细得说明了原因：http://www.toxingwang.com/linux-unix/linux-basic/846.html


3、sshd配置不正确

正确配置方法如下：

/etc/ssh/sshd_config 1、找到 #StrictModes yes 改成 StrictModes no （去掉注释后改成 no） 2、找到 #PubkeyAuthentication yes 改成 PubkeyAuthentication yes （去掉注释） 3、找到 #AuthorizedKeysFile .ssh/authorized_keys 改成 AuthorizedKeysFile .ssh/authorized_keys （去掉注释） 4、保存 5、/etc/rc.d/init.d/sshd reload 重新加载

## VNC
VNC概述

   VNC (Virtual Network Computing)是虚拟网络计算机的缩写。VNC 是一款优秀的远程控制工具软件，由著名的 AT&T 的欧洲研究实验室开发的。VNC 是在基于 UNIX 和 Linux操作系统的免费的开源软件，远程控制能力强大，高效实用，其性能可以和 Windows 或 MAC 中的任何远程控制软件媲美。在 Linux 中，VNC 包括以下四个命令：vncserver，vncviewer，vncpasswd，和 vncconnect。大多数情况下只需要其中的两个命令：vncserver 和 vncviewer。目前，原来的AT&T版本已经不再使用，因为更多有重大改善的分支版本已经出现， 像是RealVNC， VNC tight 和UltraVNC。 Real VNC 是当前最活跃和强大的主流应用。

 RealVNC官方网址  :  http://www.realvnc.com/

 Tight VNC官方网址:  http://www.tightvnc.com/

 UltraVNC官方网址 ： http://www.uvnc.com/

 

VNC原理

VNC系统由客户端，服务端和一个协议组成。VNC的服务端目的是分享其所运行机器的屏幕， 服务端被动的允许客户端控制它。 VNC客户端（或Viewer） 观察控制服务端，与服务端交互。 VNC 协议 Protocol (RFB)是一个简单的协议，传送服务端的原始图像到客户端（一个X,Y 位置上的正方形的点阵数据）， 客户端传送事件消息到服务端。

服务器发送小方块的帧缓存给客户端，在最简单的情况，VNC协议使用大量的带宽，因此各种各样的方法被发明出来减少通讯的开支，举例来说，有各种各样的编码方法来决定最有效率的方法来传送这些点阵方块）

协议允许客户端和服务端去协议哪种编码会被使用，最简单的编码，被大多数客户端和服务端所支持的是， 从左到右的像素扫描数据的原始编码， 当原始的满屏被发送后，只发送变化的方块区域。这种编码在幁间只有小部分屏幕变化的情况下工作的非常好（像是鼠标键在桌面移动的情况，或在光标处敲击文字），不过如果大量的像素同时变化带宽将会增加的非常高，像是拖动一个窗口或观看全屏录像。

VNC默认使用TCP端口5900至5906，而JAVA的VNC客户端使用5800至5806。一个服务端可以在5500口用“监听模式”连接一个客户端，使用监听模式的一个好处是服务端不需要设置防火墙。

UNIX上的VNC称为xvnc，同时扮演两种角色，对X窗口系统的应用程序来说它是X server，对于VNC客户端来说它是VNC服务器程序。

 

实验环境

     VNC服务端：

             操作系统：Red Hat Enterprise Linux Server release 5.7 (Tikanga)

     VNC客户端：

             操作系统：Windows 7专业版  64位操作系统

 

VNC安装配置

1、安装VNC包

[root@localhost /]# cd  /depot/os/mnt/cdrom/Server

[root@localhost /]# rpm -ivh vnc-server-4.1.2-14.el5_6.6.x86_64.rpm

[root@localhost /]# rpm -ivh vnc-4.1.2-14.el5_6.6.x86_64.rpm

验证vnc-server包是否安装成功：

[root@localhost /]# rpm -qa vnc-server

vnc-server-4.1.2-14.el5_6.6

2、配置vncservers文件

修改/etc/sysconfig/vncservers文件，未经修改的vncservers文件如下所示：

[root@localhost ~]# more /etc/sysconfig/vncservers

# The VNCSERVERS variable is a list of display:user pairs.

#

# Uncomment the lines below to start a VNC server on display :2

# as my 'myusername' (adjust this to your own).  You will also

# need to set a VNC password; run 'man vncpasswd' to see how

# to do that. 

#

# DO NOT RUN THIS SERVICE if your local area network is

# untrusted!  For a secure way of using VNC, see

# <URL:http://www.uk.research.att.com/archive/vnc/sshvnc.html>.

# Use "-nolisten tcp" to prevent X connections to your VNC server via TCP.

# Use "-nohttpd" to prevent web-based VNC clients connecting.

# Use "-localhost" to prevent remote VNC clients connecting except when

# doing so through a secure tunnel.  See the "-via" option in the

# `man vncviewer' manual page.

# VNCSERVERS="2:myusername"

# VNCSERVERARGS[2]="-geometry 800x600 -nolisten tcp -nohttpd -localhost"

clip_image002

将最后两行配置信息取消注释，添加系统账号

VNCSERVERS="1:root 2：etl"

VNCSERVERARGS[1]="-geometry 1024x768 -nolisten tcp -nohttpd "

VNCSERVERARGS[2]="-geometry 1024x768 -nolisten tcp -nohttpd "

VNCSERVERS 是用来设定可以使用VNC的服务器账号，可以设定多个，例如上面root、etl，但是中间要用空格隔开。使用VNCVIEWER登录时，192.168.48.128:1表示是以root账号登录，以此类推。

关于参数配置说明：

1：-geometry 表示桌面分辨率，默认为1024x768，所以上面的1024x768也可以不写。

2：-nohttpd  表示不监听HTTP端口（58xx）。

3：-nolisten tcp 表示不监听TCP端口（60xx）

4：-localhost 只运行从本机访问。

5：AlwaysShared 默认只允许一个VNCVIEWER连接，此参数表示同一个显示端口允许多用户同时登录.

6：-depth  表示色深，参数有8,16,24,32.

7: SecurityTypes None 登录不需要密码认证VncAuth默认值,要密码认证。

3、设置VNC用户密码

如果此时不设置VNC用户密码，启动vncserver服务，则会报如下错误：

[root@localhost ~]# service vncserver start

Starting VNC server: 1:root [FAILED]

[root@localhost /]# vncpasswd

Password:

Verify:

[root@localhost /]# su - etl

[etl@localhost ~]$ vncpasswd

Password:

Verify:

[etl@localhost ~]$

4、启动vncserver服务

[root@localhost ~]# service vncserver start

Starting VNC server: 1:root

New 'localhost.localdomain:1 (root)' desktop is localhost.localdomain:1

Creating default startup script /root/.vnc/xstartup

Starting applications specified in /root/.vnc/xstartup

Log file is /root/.vnc/localhost.localdomain:1.log

2:etl

New 'localhost.localdomain:2 (etl)' desktop is localhost.localdomain:2

Creating default startup script /home/etl/.vnc/xstartup

Starting applications specified in /home/etl/.vnc/xstartup

Log file is /home/etl/.vnc/localhost.localdomain:2.log

[  OK  ]

VNC会在用户根目录($HOME)下的".vnc"文件夹下生成一系列文件。其中passwd为vnc用户密码文件，由vncpasswd生成。其他的都由vnc初次启动时生成，xstartup为VNC客户端连接时启动的脚本

5、配置xstartup文件

如下所示，将下面紫红色的部分注释取消。

clip_image004

#!/bin/sh

# Uncomment the following two lines for normal desktop:

unset SESSION_MANAGER

exec /etc/X11/xinit/xinitrc

[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup

[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources

xsetroot -solid grey

vncconfig -iconic &

xterm -geometry 80x24+10+10 -ls -title "$VNCDESKTOP Desktop" &

twm &

切换到etl账号，依法炮制

[root@localhost ~]# su - etl

[etl@localhost ~]$ vi /home/etl/.vnc/xstartup

clip_image006

[root@localhost ~]# service vncserver restart

Shutting down VNC server: 1:root 2:etl [  OK  ]

Starting VNC server: 1:root

New 'localhost.localdomain:1 (root)' desktop is localhost.localdomain:1

Starting applications specified in /root/.vnc/xstartup

Log file is /root/.vnc/localhost.localdomain:1.log

2:etl

New 'localhost.localdomain:2 (etl)' desktop is localhost.localdomain:2

Starting applications specified in /home/etl/.vnc/xstartup

Log file is /home/etl/.vnc/localhost.localdomain:2.log

[  OK  ]

6、配置防火墙

如果你不配置防火墙，此时用VNC Viewer连接的话，一般会报："connect：Connection timed out(10060)"错误,如下所示：

clip_image008

clip_image010

一般这种情况要么关闭防火墙，要么需要配置防火墙。

[root@localhost ~]# service iptables stop

Flushing firewall rules: [  OK  ]

Setting chains to policy ACCEPT: filter [  OK  ]

Unloading iptables modules: [  OK  ]

关闭防火墙后，用VNCView连接服务器没有问题，但是一般不建议关闭防火墙，

[root@localhost ~]# service iptables restart

Flushing firewall rules: [  OK  ]

Setting chains to policy ACCEPT: filter [  OK  ]

Unloading iptables modules: [  OK  ]

Applying iptables firewall rules: [  OK  ]

Loading additional iptables modules: ip_conntrack_netbios_ns [  OK  ]

[root@localhost ~]# iptables -I INPUT -p tcp --dport 5901 -j ACCEPT

[root@localhost ~]# iptables -I INPUT -p tcp --dport 5902 -j ACCEPT

clip_image012

OK，可以通过VNC连接到服务器了

 

关于VNC服务使用的端口号与桌面号相关，VNC使用TCP端口从5900开始，对应关系如下
桌面号为“1” ---- 端口号为5901
桌面号为“2” ---- 端口号为5902
桌面号为“3” ---- 端口号为5903
……
基于Java的VNC客户程序Web服务TCP端口从5800开始，也是与桌面号相关，对应关系如下
桌面号为“1” ---- 端口号为5801
桌面号为“2” ---- 端口号为5802
桌面号为“3” ---- 端口号为5803
基于上面的介绍，如果Linux开启了防火墙功能，就需要手工开启相应的端口，以开启桌面号为“1”相应的端口为例，命令如下

开机自启动vncserver服务
# chkconfig vncserver on

[参考资料]：

http://zh.wikipedia.org/wiki/VNC

http://www.ha97.com/4634.html

http://blog.sina.com.cn/s/blog_48f9c0840100x2qh.html

 

## Meta
### Tip

ubuntu 删除出现 /var/lib/dpkg/lock 时的解决方法
```bash
$ sudo rm /var/cache/apt/archives/lock
$ sudo rm /var/lib/dpkg/lock
```

快速复制到剪贴板

```bash
$ clip < file.txt
```
## 前言
安装字体
 1. 下载 Consolas 字体，然后
sudo mkdir -p /usr/share/fonts/yahei
sudo cp YaHei.Consolas.1.12.ttf /usr/share/fonts/yahei/
然后，改变权限：
sudo chmod 644 /usr/share/fonts/yahei/YaHei.Consolas.1.12.ttf
安装：
cd /usr/share/fonts/yahei/
sudo mkfontscale
sudo mkfontdir
sudo fc-cache -fv

boot 空间不够：
dpkg --get-selections|grep linux  查看有哪些多余的 kernal 
sudo apt-get remove linux-image-(版本号)（就是上面带image的版本）
有卸载不完全的（有提示），可以用 sudo apt-get autoremove来删除。
sudo dpkg -P linux-image-xxx 清除出现 `deinstall` 的 list


tmux使用C/S模型构建，主要包括以下单元模块：
* server服务器。输入tmux命令时就开启了一个服务器。
* session会话。一个服务器可以包含多个会话
* window窗口。一个会话可以包含多个窗口。
* pane面板。一个窗口可以包含多个面板。



tmux里的session，window,pane
—-

session指的是按下tmux命令后 存在的连接便是session
创建session
tmux

创建并指定session名字
tmux new -s $session_name

删除session
Ctrl+b :kill-session

临时退出session
Ctrl+b d

列出session
tmux ls

进入已存在的session
tmux a -t $session_name

删除所有session
Ctrl+b :kill-server

删除指定session
tmux kill-session -t $session_name

—-

window在session里，可以有N个window，并且window可以在不同的session里移动
创建window
Ctrl+b +c

删除window
Ctrl+b &

下一个window
Ctrl+b n

上一个window
Ctrl+b p

重命名window
Ctrl+b ,

在多个window里搜索关键字
Ctrl+b f

在相邻的两个window里切换
Ctrl+b l

—-

pane在window里，可以有N个pane，并且pane可以在不同的window里移动、合并、拆分
创建pane
横切split pane horizontal
Ctrl+b ” (问号的上面，shift+’)

竖切split pane vertical
Ctrl+b % （shift+5）

按顺序在pane之间移动
Ctrl+b o

上下左右选择pane
Ctrl+b 方向键上下左右

调整pane的大小
Ctrl+b :resize-pane -U #向上
Ctrl+b :resize-pane -D #向下
Ctrl+b :resize-pane -L #向左
Ctrl+b :resize-pane -R #向右
在上下左右的调整里，最后的参数可以加数字 用以控制移动的大小，例如：
Ctrl+b :resize-pane -D 50

在同一个window里左右移动pane
Ctrl+b { （往左边，往上面）
Ctrl+b } （往右边，往下面）

删除pane
Ctrl+b x

更换pane排版
Ctrl+b “空格”

移动pane至window
Ctrl+b !

移动pane合并至某个window
Ctrl+b :join-pane -t $window_name

显示pane编号
Ctrl+b q

按顺序移动pane位置
Ctrl+b Ctrl+o

—-
其他：

复制模式
Ctrl+b [
空格标记复制开始，回车结束复制。

粘贴最后一个缓冲区内容
Ctrl+b ]

选择性粘贴缓冲区
Ctrl+b =

列出缓冲区目标
Ctrl+b :list-buffer

查看缓冲区内容
Ctrl+b :show-buffer

vi模式
Ctrl+b :set mode-keys vi

显示时间
Ctrl+b t

快捷键帮助
Ctrl+b ? (Ctrl+b :list-keys)

tmux内置命令帮助
Ctrl+b :list-commands
