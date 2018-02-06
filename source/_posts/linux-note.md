---
title: linux-note
mathjax: true
comments: true
date: 2018-02-06 16:57:33
updated: 2018-02-06 16:57:33
    - System
    - linux
tags: [linux,ubuntu,shell]
---


ubuntu 删除出现 /var/lib/dpkg/lock 时的解决方法
```bash
$ sudo rm /var/cache/apt/archives/lock
$ sudo rm /var/lib/dpkg/lock
```

