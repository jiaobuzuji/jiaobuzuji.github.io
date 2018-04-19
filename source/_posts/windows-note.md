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
