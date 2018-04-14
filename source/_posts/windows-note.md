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
