---
title: Linux 编译 Win32 的 VLC
date: 2018-07-13 14:30:06
updated: 2018-07-13 14:30:10
comments: true
categories:
    - Tools
    - vlc
tags: [linux,ubuntu,vlc,cross compiling]

---

## 环境
* OS : Ubuntu 18.04 LTS
```bash
apt -y build-dep vlc
apt install -y "gcc-mingw-w64-x86-64 g++-mingw-w64-x86-64 mingw-w64-tools"
apt install -y "lua5.2 liblua5.2-dev libtool automake autoconf autopoint make gettext pkg-config"
apt install -y "qt4-dev-tools qt5-default git subversion cmake cvs"
apt install -y "wine64-development-tools libwine-dev zip p7zip nsis bzip2"
apt install -y "yasm ragel ant openjdk-8-jdk default-jdk protobuf-compiler dos2unix"
apt install -y "gperf flex bison liborc-0.4-dev"
ldconfig
```
运行[官网教程]中的 Static compilation of plugins，我将其写入一个名为：vlc.sh 的脚本中。
> You might want to use the following script to enforce static compilation. Run as root, and use at your own risk.

```bash
sudo sh vlc.sh
```

下载对应版本的 VCL 源代码
```bash
curl -R -O ftp://ftp.videolan.org/pub/vlc/2.2.8/vlc-2.2.8.tar.xz
tar -Jxf vlc-2.2.8.tar.xz
mv vlc-2.2.8 vlc
```

[官网教程]提醒版本要对应，ubuntu1804 使用的的版本为 5.0.3，应该还是可以的。
> **NB**: you need mingw-w64 version 5.0.1 to compile it.

## VLC : 3.0.3 (失败告终)
* HOST_TRIPLET : x86_64-w64-mingw32

### 第三方库
尝试过使用 vlc-contrib-x86_64-w64-mingw32-20171209.tar.bz2 进行编译，会提示一堆的错误（2018-07-12）。

[官网教程]提醒，所以只能手动 build 第三方的库了。
> At the time of writing (late 2016), the prebuilt libraries work with VLC 2.2.x **only**. To compile the VLC 3.0.x development branch, **DO NOT USE** the prebuilt libraries.

自己 build 的库，似乎比较少，可能是因为这样子，才导致后面出现了一堆错误。

### lua5.2 没找着
```bash
../extras/package/win32/configure.sh --host=${HOST_TRIPLET} --build=x86_64-pc-linux-gnu \
--disable-lua \
```

### 切换到 java8
```bash
update-alternatives --config java
update-alternatives --config javac
```

### VDPAU 出错
[IMHO x11/VDPAU shouldn't be checked to begin with when compiling for win32/win64](https://trac.videolan.org/vlc/ticket/18609)
```bash
sudo apt-get remove -y libvdpau-dev
```

### 提示`schroedinger`出错
download [schroedinger_1.0.11.orig.tar.gz](https://packages.ubuntu.com/xenial/libschroedinger-dev)
```bash
tar -zxf schroedinger_1.0.11.orig.tar.gz
cd schroedinger_1.0.11
./configure --prefix=/usr
make
sudo make install
```

### make 时提示看不到 idna.h 文件
***无解***，坐吃等死。

## VLC : 2.2.8
* HOST_TRIPLET : i686-w64-mingw32
* **`apt install -y "qt4-default"`**:

```bash
cd vlc
mkdir -p contrib/win32 && cd contrib/win32
curl -R -O ftp://ftp.videolan.org/pub/videolan/contrib/i686-w64-mingw32/vlc-contrib-i686-w64-mingw32-20160218.tar.bz2
mv vlc-contrib-i686-w64-mingw32-20160218.tar.bz2 vlc-contrib-i686-w64-mingw32-latest.tar.bz2
make clean
make distclean
../bootstrap --host=${HOST_TRIPLET}
make prebuilt
rm -f ../i686-w64-mingw32/bin/moc ../i686-w64-mingw32/bin/uic ../i686-w64-mingw32/bin/rcc
cd -
./bootstrap
mkdir -p win32 && cd win32
make uninstall
make clean
make distclean
PKG_CONFIG_LIBDIR=$HOME/tmp/vlc/vlc/contrib/${HOST_TRIPLET}/lib/pkgconfig \
../extras/package/win32/configure.sh --host=${HOST_TRIPLET} --build=x86_64-pc-linux-gnu \
--disable-mkv
make
make package-win-common
```

## 懂得的东西
1. configure 失败之后，再次重新 configure 时，最好 `make distclean` 一下。
1. toolchain 很重要，版本的应对对于编译影响比较大
1. Cross Compiling 用的 LIB 一般是与系统不同的，虽然很可能是同名字，但架构不一样，库也不一样。
1. Cross Compiling 在 configure 时，必须通过 `PKG_CONFIG_LIBDIR` 指定目标架构的库文件。

## 相关连接
* https://wiki.videolan.org/Win32Compile/
* https://wiki.videolan.org/VLC_configure_help
* https://trac.videolan.org/vlc/ticket/18609
* https://wiki.videolan.org/Contrib_Status
* ftp://ftp.videolan.org/pub/videolan
* https://packages.ubuntu.com/
* https://higoge.github.io/2015/07/17/sm02/

[官网教程]: https://wiki.videolan.org/Win32Compile/ "VLC Win32Compile"

