---
title: gcc-note
mathjax: true
comments: true
date: 2018-03-05 16:53:48
updated: 2018-03-05 16:53:48
categories:
    - Tools
    - gcc
tags: [gcc,compiler]
---

## 源码编译 gcc

mkdir ../gcc-build-4.9.0

#cd ../gcc-build-4.9.0

#../gcc-4.9.0/configure --prefix=/usr/local/gcc-4.9.0 --enable-stage1-checking=release --enable-stage1-languages=c,c++,go

#make -j 4

#make install
编译后安装到  /usr/local/gcc-4.9.0

3.4. 验证安装
#cd /usr/local/gcc-4.9.0
#./bin/gcc -v
看到版本信息
