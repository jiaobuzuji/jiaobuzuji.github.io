---
title: linux-note
mathjax: true
comments: true
date: 2018-02-06 16:57:33
updated: 2018-02-06 16:57:33
categories:
    - System
    - linux
tags: [linux,ubuntu,shell]
---


ubuntu 删除出现 /var/lib/dpkg/lock 时的解决方法
```bash
$ sudo rm /var/cache/apt/archives/lock
$ sudo rm /var/lib/dpkg/lock
```
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
