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

## VLC : 3.0.6
### 准备
* OS : Ubuntu 18.10 amd64 desktop minimal ，meson 要求比较新的版本
* Software & Update 更换一下源，同时勾选 “source xxxxx”
* https://forum.videolan.org/viewtopic.php?t=143536  系统自带的 mingw 是 7.3 ，还是有 bug ，所以我们只能手动安装个 mingw 8
* https://packages.debian.org/ 下载 mingw 8

```
binutils-mingw-w64-i686_2.31.1-11+8.3_amd64.deb
binutils-mingw-w64-x86-64_2.31.1-11+8.3_amd64.deb
g++-mingw-w64-i686_8.2.0-21+21.1_amd64.deb
g++-mingw-w64-x86-64_8.2.0-21+21.1_amd64.deb
gcc-mingw-w64-base_8.2.0-21+21.1_amd64.deb
gcc-mingw-w64-i686_8.2.0-21+21.1_amd64.deb
gcc-mingw-w64-x86-64_8.2.0-21+21.1_amd64.deb
mingw-w64-common_6.0.0-3_all.deb
mingw-w64-tools_6.0.0-3_amd64.deb
mingw-w64-x86-64-dev_6.0.0-3_all.deb
```

```bash
sudo apt -y update
sudo apt -y dist-upgrade
sudo apt -y build-dep vlc --fix-missing
sudo apt install -y build-essential

sudo apt install -y --fix-missing \
  lua5.2 liblua5.2-dev libtool automake autoconf autopoint make gettext pkg-config \
  qt5-default git subversion cmake cvs \
  wine64-development-tools libwine-dev zip p7zip nsis bzip2 \
  yasm ragel ant openjdk-8-jdk default-jdk protobuf-compiler dos2unix \
  gperf flex bison liborc-0.4-dev \
  libltdl-dev meson nasm

# 切换到 java8
sudo update-alternatives --config java
sudo update-alternatives --config javac

# 下载对应版本的 VCL 源代码
curl -R -O ftp://ftp.videolan.org/pub/vlc/3.0.6/vlc-3.0.6.tar.xz && tar -Jxf vlc-3.0.6.tar.xz
mkdir -p "vlc-3.0.6/share/hrtfs/"   # 或者直接到下载最新的源代码，将 hrtfs 文件夹拷贝进去。防止 make package-win-common 时出错
```

### 编译脚本代码

```bash
cd vlc-3.0.6
HOST_TRIPLET=x86_64-w64-mingw32

mkdir -p contrib/win64 && cd contrib/win64
make mostlyclean
../bootstrap --host=${HOST_TRIPLET}
make fetch-all && make -j4
cd -

PKG_CONFIG_LIBDIR="`pwd`/contrib/${HOST_TRIPLET}/lib/pkgconfig"
mkdir -p win64 && cd win64
make uninstall
make distclean
cd - && ./bootstrap && cd -
../configure --host=${HOST_TRIPLET} --build=x86_64-pc-linux-gnu \
  --disable-chromecast \
&& make -j4 && make package-win-common -j4
```

### versioninfo.rc 错误：

```
i686-w64-mingw32-windres: versioninfo.rc.in:21: syntax error
i686-w64-mingw32-windres: preprocessing failed.
Makefile:1224: recipe for target 'versioninfo.lo' failed
make[2]: *** [versioninfo.lo] Error 1
make[2]: Leaving directory '/home/d/vlc-3.0.0/contrib/win32/gcrypt/src'
Makefile:487: recipe for target 'install-recursive' failed
make[1]: *** [install-recursive] Error 1
make[1]: Leaving directory '/home/d/vlc-3.0.0/contrib/win32/gcrypt'
../../contrib/src/gcrypt/rules.mak:72: recipe for target '.gcrypt' failed
make: *** [.gcrypt] Error 2
```

解决方案： 修改 contrib/win32/gcrypt/configure.ac 第42行

修改前：m4_esyscmd([git rev-parse --short HEAD | tr -d '\n\r']))
修改后：m4_esyscmd([printf %x $(wc -l < debian/changelog)]))

### 缺少 libssp-0.dll
随便上网找个 windows 的 dll 。将其放入编译出来的vlc-3.0.6 文件夹中。


## VLC : 3.0.3 (失败告终)
* HOST_TRIPLET : x86_64-w64-mingw32

- 运行[官网教程]中的 Static compilation of plugins，我将其写入一个名为：vlc.sh 的脚本中。-
> You might want to use the following script to enforce static compilation. Run as root, and use at your own risk.

[官网教程]提醒版本要对应，ubuntu1804 使用的的版本为 5.0.3，应该还是可以的。
> **NB**: you need mingw-w64 version 5.0.1 to compile it.

### 第三方库
尝试过使用 vlc-contrib-x86_64-w64-mingw32-20171209.tar.bz2 进行编译，会提示一堆的错误（2018-07-12）。

[官网教程]提醒，所以只能手动 build 第三方的库了。
> At the time of writing (late 2016), the prebuilt libraries work with VLC 2.2.x **only**. To compile the VLC 3.0.x development branch, **DO NOT USE** the prebuilt libraries.

自己 build 的库，似乎比较少，可能是因为这样子，才导致后面出现了一堆错误。


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
* https://packages.debian.org/
* https://higoge.github.io/2015/07/17/sm02/
* https://forum.videolan.org/viewtopic.php?t=143536
* https://github.com/videolan/vlc

[官网教程]: https://wiki.videolan.org/Win32Compile/ "VLC Win32Compile"

