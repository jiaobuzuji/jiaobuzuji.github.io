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

## 前言

## Dependence
systemc-2.3.0a(注意不是https://github.com/systemc/systemc-2.3，这个太旧)
gcc-4.9.3以上

```bash
$ wget -O systemc-2.3.0a.tar.gz http://www.accellera.org/images/downloads/standards/systemc/systemc-2.3.0a.tar.gz
$ tar -xzvf systemc-2.3.0a.tar.gz
$ cd systemc-2.3.0a
$ sudo mkdir -p /usr/local/systemc-2.3.0a/
$ mkdir objdir
$ cd objdir
$ ../configure --prefix=/usr/local/systemc-2.3.0a
$ make
$ sudo make install
```
http://msyksphinz.hatenablog.com/?page=1511362800

## sim

使用 VCS 进行仿真
修改 `synth_tb/tb_top.v` 中的 dump vcd 的等级

```bash
$ cd xxx/hw/verif/sim
$ make build
$ make run TESTDIR=../traces/traceplayer/sanity0 
$ makde dve TESTDIR=../traces/traceplayer/sanity0 
```
