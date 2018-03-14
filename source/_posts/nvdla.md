---
title: nvdla 研究
mathjax: true
comments: true
date: 2018-03-04 18:19:41
updated: 2018-03-04 18:19:41
categories:
    - System
    - linux
tags: [AI,nvdla]
---

## Dependence
systemc-2.3.0a(注意不是https://github.com/systemc/systemc-2.3，这个太旧)
gcc-4.9.3以上

$ wget -O systemc-2.3.0a.tar.gz http://www.accellera.org/images/downloads/standards/systemc/systemc-2.3.0a.tar.gz
$ tar -xzvf systemc-2.3.0a.tar.gz
$ cd systemc-2.3.0a
$ sudo mkdir -p /usr/local/systemc-2.3.0/
$ mkdir objdir
$ cd objdir
$ ../configure --prefix=/usr/local/systemc-2.3.0
$ make
$ sudo make install

http://msyksphinz.hatenablog.com/?page=1511362800

## 前言
