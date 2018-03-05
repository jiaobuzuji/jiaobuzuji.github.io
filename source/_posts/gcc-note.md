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
http://gcc.gnu.org/install/

3.1. 解压 gcc-4.9.0.tar.gz
得到目录 gcc-4.9.0,进入目录
#tar -xvzf gcc-4.9.0.tar.gz
#cd gcc-4.9.0

3.2. 下载编译准备文件
主要是需要下面的库文件(需要完整版本,下载全部文件)
MPFR=mpfr-2.4.2
GMP=gmp-4.3.2
MPC=mpc-0.8.1
ISL=isl-0.12.2
CLOOG=cloog-0.18.1
执行
```
./contrib/download_prerequisites
```
如果编译机器不能上网，直接打开文本文件 download_prerequisites,把里面
依赖的库下载回来后，放在指定目录，然后注释下载命令，再次执行
contrib/download_prerequisites,把相关文件解压，并建立链接。
#vi contrib/download_prerequisites
注释 wget 相关的内容,手动把这些文件下载回来,拷贝到工作目录 gcc-4.9.0下。
#./contrib/download_prerequisites

3.3. 创建编译目录并编译安装
指定编译语言会比较好，这样make会更容易通过。
```
mkdir ../gcc-build-4.9.0
cd ../gcc-build-4.9.0
../gcc-4.9.0/configure --prefix=/usr/local/gcc-4.9.0 --enable-stage1-checking=release --enable-stage1-languages=c,c++
```
or
```
../gcc-4.9.0/configure --prefix=/usr/local/gcc-4.9.0 --disable-multilib --enable-stage1-checking=release --enable-stage1-languages=c,c++
```

```
yum install -y flex texinfo
make -j4
make install
```

编译后安装到  /usr/local/gcc-4.9.0

3.4. 验证安装
#cd /usr/local/gcc-4.9.0
#./bin/gcc -v
看到版本信息
