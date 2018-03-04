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
