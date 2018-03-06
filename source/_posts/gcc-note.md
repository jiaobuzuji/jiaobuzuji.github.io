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
https://www.vultr.com/docs/how-to-install-gcc-on-centos-6
https://www.dwhd.org/20170517_083340.html

yum install libmpc-devel mpfr-devel gmp-devel zlib-devel cmake
yum install svn texinfo-tex flex zip libgcc.i686 glibc-devel.i686

在 source 文件中，使用`ldconfig`命令，可以检查 dll 的配置。

俄罗斯的 mirror 会快许多，linux 下可以使用 md5sum 对下载下来的文件进行校验！
```
http://gcc.gnu.org/install/
http://mirror.linux-ia64.org/gnu/gcc/infrastructure/
```

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
or (建议使用下面的配置)
```
../gcc-4.9.0/configure --prefix=/usr/local/gcc-4.9.0 --with-system-zlib --disable-multilib --enable-languages=c,c++,java
```
**一定要检查一下 config.log 是否有出错！！！**

```
yum install -y flex texinfo
make -j4
make install
```
make -j$(getconf _NPROCESSORS_ONLN) && make install

编译后安装到  /usr/local/gcc-4.9.0

3.4. 验证安装
#cd /usr/local/gcc-4.9.0
#./bin/gcc -v
看到版本信息
