---
title: windows-note
mathjax: true
comments: true
date: 2018-02-05 17:19:03
updated: 2018-02-05 17:19:03
categories:
    - System
    - windows
tags: [win32,windows]
---

### Tip
1. windows 下创建 link 的正确方法。`mklink`

### Excel
如何用函数实现求A列乘以B列然后相加的总和,如下，类似1*5+2*6+3*7+4*8，用函数如何实现
1  5
2  6
3  7
4  8

1. =SUMPRODUCT(A1:A15,B1:B15)
1. 或者直接{=sum(A1:A15\*B1:B15)},花括号表示是数组公式，按ctrl+shift+enter

### SecureCRT
 相信不少SecureCRT的新手都有过这样的困扰：SecureCRT 超时自动断开连接 很影响工作
解决办法：
Options->Session Options->Terminal->Anti-idle->勾选Send protocol NO-OP
(中文版：选项->会话选项->终端->反空闲->发送协议NO-OP)
后面的设置时间默认的是60秒，只要小于自动断开连接的时限就可以了

### 端口被占用
netstat -aon|findstr "443"
tasklist|findstr "???"

### 进程列表，kill
相信大家都有用命令行(CMD)解决问题的习惯，起码我感觉自己在处理Windows系统故障时越来越离不开Windows PE了，今天我想介绍两个很实用的命令：Tasklist与Tskill。
命令：Tasklist
　　功能：命令用来显示运行在本地或远程计算机上的所有进程，可以监控用户的操作。
　　命令格式：
　　Tasklist [/S system [/U username [/P [password]]]] [/M [module] | /SVC | /V] [/FI filter] [/FO format] [/NH]
　　参数含义
　　/S system　 指定连接到的远程系统。
　　/U [domain\]user　指定使用哪个用户执行这个命令。
　　/P [password]　 为指定的用户指定密码。
　　/M [module]　 列出调用指定的DLL模块的所有进程。如果没有指定模块名，显示每个进程加载的所有模块。
　　/SVC　显示每个进程中的服务。
　　/V　显示详细信息。
　　实例分析：
　　如果我们只是查看本地主机进程信息，直接办入命令即可。下面的实例是从客户机远程查看内网中某台主机时程信息。
　　假如我们有一台服务器：
　　内网地址：192.168.0.1，
　　管理员帐号：administrator
　　管理员密码：password
　　我们需要在CMD窗口输入：
　　Tasklist /s 192.168.0.1 /u administrator /p password
　　这条命令可以使我们方便的查看到远程主机的运行情况，当然前提是保证RPC服务正常启动。

命令：tskill

　　功能：用来关掉进程的
　　命令格式：
　　TSKILL processid | processname [/SERVER:servername] [/ID:sessionid | /A] [/V]
　　参数含义
　　processid 要结束的进程的 Process ID。
　　processname 要结束的进程名称。
　　/SERVER:servername 含有 processID 的服务器(默认值是当前值)。
　　使用进程名和 /SERVER 时，必须指定
　　/ID 或 /A
　　/ID:sessionid 结束在指定会话下运行的进程。
　　/A 结束在所有会话下运行的进程。
　　/V 显示正在执行的操作的信息。
　　这个Tskill用法很简单，直接输入Tskill 图象名或PID就可以了。
　　偶尔碰上Tskill无法结束的进程，还可以试试Ntsd命令，
　　格式为: ntsd -c q -pn {进程名｝
　　参数含义：
　　-c是表示执行debug命令;
　　q表示执行结束后退出;
　　-p 表示后面紧跟着是你要结束的进程对应的PID;
　　-pn 表示后面紧跟着是你要结束的进程名; 
