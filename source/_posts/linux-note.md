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

## Perl 相关

## 安装到 local
要设置以下几个环境变量
```
PATH
MANPATH
LD_LIBRARY_PATH
C_INCLUDE_PATH
LIBRARY_PATH
```

## 关于login/interactive/no-interactive shell和profile/bash_profile/bashrc

login shell：第一次登录进系统时的shell，一般是指本机启动时的控制台shell或者ssh远程登录时的shell。

interactive shell：登录以后，再打开控制台时运行的shell。

none interactive shell：只是用来执行脚本的shell，执行完就结束进程了，没有用户交互过程。

mac有个特殊地方：每次打开一个终端都是一个login shell。

使用shell的执行选项
上一节所述的调试手段是通过修改shell脚本的源代码，令其输出相关的调试信息来定位错误的，那有没有不修改源代码来调试shell脚本的方法呢？答案 就是使用shell的执行选项，本节将介绍一些常用选项的用法：

-n 只读取shell脚本，但不实际执行
-x 进入跟踪方式，显示所执行的每一条命令
-c "string" 从strings中读取命令

“-n”可用于测试shell脚本是否存在语法错误，但不会实际执行命令。在shell脚本编写完成之后，实际执行之前，首先使用“-n”选项来测试脚本 是否存在语法错误是一个很好的习惯。因为某些shell脚本在执行时会对系统环境产生影响，比如生成或移动文件等，如果在实际执行才发现语法错误，您不得 不手工做一些系统环境的恢复工作才能继续测试这个脚本。
“-c”选项使shell解释器从一个字符串中而不是从一个文件中读取并执行shell命令。当需要临时测试一小段脚本的执行结果时，可以使用这个选项， 如下所示：
sh -c 'a=1;b=2;let c=$a+$b;echo "c=$c"'
"-x"选项可用来跟踪脚本的执行，是调试shell脚本的强有力工具。“-x”选项使shell在执行脚本的过程中把它实际执行的每一个命令行显示出 来，并且在行首显示一个"+"号。 "+"号后面显示的是经过了变量替换之后的命令行的内容，有助于分析实际执行的是什么命令。 “-x”选项使用起来简单方便，可以轻松对付大多数的shell调试任务,应把其当作首选的调试手段。

## umask
在linux中，常常都要提示设置： 
     umask 022

其作用如下：

功能说明：指定在建立文件时预设的权限掩码。
语　　法：umask [-S][权限掩码]
补充说明：umask可用来设定[权限掩码]。[权限掩码]是由3个八进制的数字所组成，将现有的存取权限减掉权限掩码后，即可产生建立文件时预设的权限。
参　　数：
-S 　以文字的方式来表示权限掩码。 
文件：用八进制基数666，即无x位（可执行位）rw- rw- rw-.执行位需由用户自行加入

例一：设要生成的文件以rw- r-- r--这样的权限字出现，即真实权限用八进制表示为644，则被666基数减得022，022即掩码。使用umask 022。

注：033效果与022一样，假设使用033掩码进行设置，则真实权限应为633即rw- r-x r-x ，但前提规定文件不生成x位，所以文件的权限最终将以rw-r--r--出现。

目录：用八进制基数777

例二：设要生成的目录权限以rwxr-xr-x这样的权限字出现，即真实权限用八进制表示为755，则被基数为777的权限字相减后，得掩码022。则使用umask 022进行设置。

总结：

掌握二个要点，一、文件基数为666，目录为777，即文件无设x位，目录可设x位。二、chmod是设哪个位，哪么哪个位就有权限，而umask是设哪个位，则哪个位上就没权限。

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

## tmux
https://wiki.archlinux.org/index.php/tmux

高版本的 tmux 。可以支持 vim 的 termguicolor 这一点比较好。

复制的时候，如果直接用鼠标复制的时候，是又vim处理的，需要按住shift才是xterm处理的，
后测试了下，果然可以，后来用vnc连server发现在vim中也是同样适用的，高兴

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

### tmux如何查看历史输出
在tmux里面，因为每个窗口(tmux window)的历史内容已经被tmux接管了，所以原来console/terminal提供的Shift+PgUp/PgDn所显示的内容并不是当前窗口的历史内容，那么应该怎么办呢?

改用C-b [进入copy mode，然后就可以用PgUp/PgDn/光标来浏览历史输出了，按q退出。C-b PgUp也可以直接进入coy mode. 参见:How do I scroll in tmux? - Super User

copy mode其实比较类似于vi/emacs里面一个只读buffer，可以移动光标，可以搜索，用C-SPC开始选择，选择完后用M-w拷贝(拷贝后自动退 出copy mode)，然后可以C-b ]粘贴(可在其它窗口粘贴), C-b =可以从剪贴板历史中选择。

gnu screen里面呢
gnu screen进入copy mode的方式跟tmux类似(C-a [)，但进入后它是vi style keybindings。

对于拷贝文字，第一次空格设置开始标记，然后用hjklw之类移动光标，第二次空格完成拷贝。粘贴也是用C-a ]


### update-alternatives命令详解

说明：
如我系统已安装有java 1.6，还想要安装java 1.7，但我不想卸载java 1.6。就可以通过update-alternatives –config在多个java版本间进程切换。update-alternatives是用于在多个同类型命令中进行切换的一个脚本，在debian中可以通过apt-get install dpkg来进行安装。

在说明update-alternatives的详细用法之前，先让我们看看系统中已有的例子。打开终端，执行下面的命令：
# ls -l /usr/bin/java
lrwxrwxrwx 1 root root 22 2011-03-12 15:20 /usr/bin/java -> /etc/alternatives/java

# ls -l /etc/alternatives/java
lrwxrwxrwx 1 root root 40 2011-03-12 15:20 /etc/alternatives/java -> /usr/lib/jvm/java-6-openjdk/jre/bin/java
java这个可执行命令实际是一个链接，指向了/etc/alternatives/java。而这个也是一个链接，指向了/usr/lib/jvm/java-6-openjdk/jre/bin/java，这才是最终的可执行文件。之所以建立这样两个链接，是为了方便脚本程序的编写和系统的管理。

语法：
暂空！！！

实例：
1. display参数列出一个命令的所有可选命令

# update-alternatives --display java

 
2. config参数用于给某个命令选择一个link值，相当于在可用值之中进行切换吧。

# update-alternatives --config java  //有 3 个候选项可用于替换 java (提供 /usr/bin/java)  
 选择       路径                                       优先级  状态  
------------------------------------------------------------  
  0            /usr/lib/jvm/java-6-openjdk/jre/bin/java      1061      自动模式  
* 1            /home/wuekzhu/download/jdk1.6.0_23/bin/java   1         手动模式  
  2            /usr/lib/jvm/java-6-openjdk/jre/bin/java      1061      手动模式  

 
3. install参数用于添加一个命令的link值，相当于添加一个可用值，其中slave非常有用。

# update-alternatives –install /usr/bin/java java /usr/local/jre1.6.0_20/bin/javac 100  
# update-alternatives –install /usr/bin/java java /usr/local/jre1.6.0_20/bin/javac 100 –slave /usr/bin/javac javac /usr/local/jre1.6.0_20/bin/javac  

 
4. remove参数用于删除一个命令的link值，其附带的slave也将一起删除。

# update-alternatives –remove java /usr/local/jre1.6.0_20/bin/java  

 
当然如果想让java能使用，还是得把java按照如下添加到环境变量去。
# vim /etc/profile //在最后添加以下内容
JAVA_HOME=/usr/local/jdk1.5.0_22
CLASSPATH=.:$JAVA_HOME/lib/tools.jar
PATH=$PATH: $JAVA_HOME/bin
export PATH=$PATH:$JAVA_HOME/bin
export JAVA_HOME CLASSPATH PATH
如果想通过update-alternatives在多个版本的java间切换，只需简单地用update-alternatives –config java，选择我们想要的java版本即可！


## perl模块安装

转自：http://www.mike.org.cn/blog/index.php?load=read&id=643
 

Perl 到了第五版增加了模块的概念，用来提供面向对象编程的能力。这是 Perl 语言发展史上的一个里程碑。此后，广大自由软件爱好者开发了大量功能强大、构思精巧的 Perl 模块，极大地扩展了 Perl 语言的功能。CPAN(Comprehensive Perl Archive Network)是Perl模块最大的集散地，包含了现今公布的几乎所有的perl模块。

安装方法

我在这里介绍一下各种平台下 perl 模块的安装方法。以安装Net-Server模块为例。

一 Linux/Unix下安装Perl模块有两种方法：手工安装和自动安装。

第一种方法是从CPAN上下载您需要的模块，手工编译、安装。第二种方法是使用CPAN模块自动完成下载、编译、安装的全过程。

A、手工安装的步骤：

　　从 CPAN(http://search.cpan.org/)下载了Net-Server模块0.97版的压缩文件Net-Server-0.97.tar.gz，假设放在/usr/local/src/下。

　　cd /usr/local/src

　　解压缩这个文件,这时会新建一个Net-Server-0.97的目录。

　　tar xvzf Net-Server-0.97.tar.gz

　　换到解压后的目录：

　　cd Net-Server-0.97

　　生成 makefile：

　　perl Makefile.PL

　　生成模块：make

　　测试模块(这步可有可无)：

　　make test
　　
　　如果测试结果报告“all test ok”，您就可以放心地安装编译好的模块了。

　　安装模块前，先要确保您对 perl5 安装目录有可写权限(通常以 su 命令获得)，执行：

　　make install

　　现在，试试 DBI 模块吧。如果下面的命令没有给出任何输出，那就没问题。

　　$>perl -MNet::Server -e1

　　上述步骤适合于 Linux/Unix下绝大多数的Perl模块。可能还有少数模块的安装方法略有差别，所以最好先看看安装目录里的 README 或 INSTALL。

 

有的时候如果是build.pl的需要以下安装步骤：（需要Module::Build模块支持）

  perl Build.PL
 ./Build
 ./Build test
 ./Build install 

 

B、使用CPAN模块自动安装方法一：

　　安装前需要先联上网，并且您需要取得root权限。

　　perl -MCPAN -e shell

　　初次运行CPAN时需要做一些设置，如果您的机器是直接与因特网相联(拨号上网、专线，etc.)，那么一路回车就行了，只需要在最后一步选一个离您最近的 CPAN 镜像站点。例如我选的是位于国内的http://www.cnblogs.com/itech/admin/ftp://www.perl87.cn/CPAN/ 。否则，如果您的机器位于防火墙之后，还需要设置ftp代理或http代理。下面是常用 cpan 命令。

　　获得帮助
　　cpan>help

　　列出CPAN上所有模块的列表
　　cpan>m

　　安装模块，自动完成Net::Server模块从下载到安装的全过程。
　　cpan>install Net::Server

　　退出
　　cpan>quit

C、使用CPAN模块自动安装方法二：

　　cpan -i 模块名

　　例如：cpan -i Net::Server

 

二 windows上perl模块安装　

A 手动（跟Linux类似） [解压后 perl makefile.pl nmake/dmake nmake/dmake install]

nmake需要cd C:\Program Files\Microsoft Visual Studio X\VC\bin and execute vcvars32.bat;然后执行nmake；

dmake 貌似是cpan环境配置好就有了在C:\Perl\site\bin下。
B Cpan （安装前需要对cpan配置，cpan需要安装其他的模块dmake和MinGw gcc compiler） （跟Linux类似）

 

C 如果使用ActivePerl，可以使用PPM来安装，使用PPM GUI或PPM Commandline，PPM commandline实例如下：

a) add correct repositories..

c:\perl\bin\ppm repo add http://theoryx5.uwinnipeg.ca/ppms/package.lst

c:\perl\bin\ppm repo add http://www.roth.net/perl/packages/

 b) add the packages

c:\perl\bin\ppm install Carp-Assert

c:\perl\bin\ppm install Log-Log4perl

c:\perl\bin\ppm install YAML-Syck

 

三 几个主要的CPAN站点有：

　　国内：

　　最新更新请查阅 http://cpan.org/SITES.html

　　http://www.perl87.cn/CPAN/  网页镜像
　　http://www.cnblogs.com/itech/admin/ftp://www.perl87.cn/CPAN/   模块镜像
　　
　　国外：http://www.cpan.org/

四 使用cpan和ppm安装时要注意模块名字的大小写

完！

#### @INC 错误时
cpan > install Config::IniFiles

## centos
CentOS 6.x 版本的，yum 使用了 python2 。所以不可以卸载 python2。同时安装
pythone3 后，python 命令还是指向 python2 的。此处不宜使用暴力修改（网络上许多人都说，修改 /usr/bin/yum 来实现 python3）。


## autojump
如果你找不到适合你的版本的包，你可以从GitHub上下载源码包来编译。(**不要使用 apt yum 等 安装。建议只使用源代码编译安装**)
autojump的基本用法

autojump的工作方式很简单：它会在你每次启动命令时记录你当前位置，并把它添加进它自身的数据库中。这样，某些目录比其它一些目录添加的次数多，这些目录一般就代表你最重要的目录，而它们的“权重”也会增大。

现在不管你在哪个目录，你都可以使用下面的语法来直接跳转到这些目录：
Shell
autojump [目录的名字或名字的一部分]
1
	
autojump [目录的名字或名字的一部分]

注意，你不需要输入完整的名称，因为autojump会检索它的数据库，并返回最可能的结果。

例如，假定我们正在下面的目录结构中工作。

rger140043jbr5bwr2n1znj87c

那么下面的命令将直接让你跳到/root/home/doc下，不管你当前位置在哪里。
Shell
$ autojump do
1
	
$ autojump do

如果你也很讨厌打字，那么我推荐你为autojump起个别名，或者使用默认的别名。
Shell
$ j [目录的名字或名字的一部分]
1
	
$ j [目录的名字或名字的一部分]

另外一个引人注目的功能是，autojump支持zsh和自动补完。如果你不确认哪里是不是你要跳转的地方，敲击TAB键就会列出完整路径。

还是同样的例子，输入：
Shell
$ autojump d
1
	
$ autojump d

然后敲击tab键，将会返回/root/home/doc或者/root/home/ddl。

最后，对于高级用户，你可以访问目录数据库，并修改它的内容。可以使用下面的命令来手动添加一个目录：
Shell
$ autojump -a [目录]
1
	
$ autojump -a [目录]

如果你突然想要把当前目录变成你的最爱和使用最频繁的文件夹，你可以在该目录通过命令的参数 i 来手工增加它的权重
Shell
$ autojump -i [权重]
1
	
$ autojump -i [权重]

这将使得该目录更可能被选择跳转。相反的例子是在该目录使用参数 d 来减少权重：
Shell
$ autojump -d [权重]
1
	
$ autojump -d [权重]

要跟踪所有这些改变，输入：
Shell
$ autojump -s
1
	
$ autojump -s

这会显示数据库中的统计数据。而以下：
Shell
$ autojump --purge
1
	
$ autojump --purge

命令将会把不再存在的目录从数据库中移除。

简言之，autojump将会受到所有命令行高级用户的欢迎。不管你是在ssh进一台服务器，还是仅仅想要追随复古潮流，敲更少的键来减少导航时间总是件好事。如果你真的热衷于此类工具，你也肯定也想看看Fasd，它应该会给你一个惊喜——我们下次再介绍它。

你觉得autojump怎么样？你会经常用它么？发表一下你的评论吧

## putty
putty 登陆linux，命令行下无法用"home"、“end” 键
putty -> Connection -> Data -> Terminal type string 改成 Linux


### 在putty中打开vi时复制文字到windows
复制的时候，如果直接用鼠标复制的时候，是又vim处理的，需要按住shift才是xterm处理的，
后测试了下，果然可以，后来用vnc连server发现在vim中也是同样适用的，高兴

## sed
[root@www ~]# sed [-nefr] [动作]
选项与参数：
-n ：使用安静(silent)模式。在一般 sed 的用法中，所有来自 STDIN 的数据一般都会被列出到终端上。但如果加上 -n 参数后，则只有经过sed 特殊处理的那一行(或者动作)才会被列出来。
-e ：直接在命令列模式上进行 sed 的动作编辑；
-f ：直接将 sed 的动作写在一个文件内， -f filename 则可以运行 filename 内的 sed 动作；
-r ：sed 的动作支持的是延伸型正规表示法的语法。(默认是基础正规表示法语法)
-i ：直接修改读取的文件内容，而不是输出到终端。

动作说明： [n1[,n2]]function
n1, n2 ：不见得会存在，一般代表『选择进行动作的行数』，举例来说，如果我的动作是需要在 10 到 20 行之间进行的，则『 10,20[动作行为] 』

function：
a ：新增， a 的后面可以接字串，而这些字串会在新的一行出现(目前的下一行)～
c ：取代， c 的后面可以接字串，这些字串可以取代 n1,n2 之间的行！
d ：删除，因为是删除啊，所以 d 后面通常不接任何咚咚；
i ：插入， i 的后面可以接字串，而这些字串会在新的一行出现(目前的上一行)；
p ：列印，亦即将某个选择的数据印出。通常 p 会与参数 sed -n 一起运行～
s ：取代，可以直接进行取代的工作哩！通常这个 s 的动作可以搭配正规表示法！例如 1,20s/old/new/g 就是啦！

复制代码

 
以行为单位的新增/删除


将 /etc/passwd 的内容列出并且列印行号，同时，请将第 2~5 行删除！

[root@www ~]# nl /etc/passwd | sed '2,5d'
1 root:x:0:0:root:/root:/bin/bash
6 sync:x:5:0:sync:/sbin:/bin/sync
7 shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
.....(后面省略).....


sed 的动作为 '2,5d' ，那个 d 就是删除！因为 2-5 行给他删除了，所以显示的数据就没有 2-5 行罗～ 另外，注意一下，原本应该是要下达 sed -e 才对，没有 -e 也行啦！同时也要注意的是， sed 后面接的动作，请务必以 '' 两个单引号括住喔！

只要删除第 2 行

nl /etc/passwd | sed '2d' 

 
## ripgrep

https://github.com/BurntSushi/ripgrep

这个就是 rg 查找 命令了。

## 查找 lib
locate libgcc
或者
dpkg -l | grep -i lua
