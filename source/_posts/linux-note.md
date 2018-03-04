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

## VNC


## Meta
### Tip

ubuntu 删除出现 /var/lib/dpkg/lock 时的解决方法
```bash
$ sudo rm /var/cache/apt/archives/lock
$ sudo rm /var/lib/dpkg/lock
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
