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

### Word
使用 F9 可以快速更新“交叉引用”。可以选择一批的“引用”，并按“F9”来进行更新。比较快速。

word 使用正则表达式查找替换

本节中的步骤介绍了如何使用正则表达式转置姓名。请记住，始终使用“查找和替换”对话框来运行您的正则表达式。同时请记住，如果表达式没有按预期工作，你始终可以按下 CTRL + Z 来撤销您的更改，然后尝试其他表达式。
转置姓名
启动 Word，然后打开一个新的空白文档。 复制此表格，将它粘贴到该文档中。 
Josh Barnhill
Doris Hartwig
Tamara Johnston
Daniel Shimshoni

在“开始”选项卡上的“编辑”组中，单击“替换”以打开“查找和替换”对话框。

如果您没有看到“使用通配符”复选框，请单击“更多”，然后选中该复选框。如果您没有选中该复选框，Word 会将通配符视作文本。
在“查找内容”框中键入以下字符。请确保您在两组括号之间包含了空格： (<*>) (<*>)
在“替换为”框中，键入以下字符。请确保您在逗号和第二个斜杠之间包含了空格： \2, \1
选择该表格，然后单击“全部替换”。Word 会转置这些姓名并使用逗号分隔它们，如下所示： 
Barnhill, Josh
Hartwig, Doris
Johnston, Tamara
Shimshoni, Daniel

此时，您可能会想知道：如果您的姓名中有一部分或全部包含中间名首写字母，该怎么做？ 请参阅使用正则表达式中的第一个示例以了解更多信息。

https://blog.csdn.net/robotcator/article/details/31840825
https://www.cnblogs.com/whchensir/p/5768030.html

---------------------

本文来自 robotcator 的CSDN 博客 ，全文地址请点击：https://blog.csdn.net/robotcator/article/details/31840825?utm_source=copy 
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
