---
title: qt-opencv
mathjax: true
comments: true
date: 2021-04-17 15:57:21
updated: 2021-04-17 15:57:25
categories:
    - System
    - linux
    - opencv
tags: [linux,opencv]
---

## openc linux

[官网教程](https://docs.opencv.org/master/d7/d9f/tutorial_linux_install.html "VLC tutorial_linux_install")

## opencv安装配置
### 安装必要的包
```
sudo apt-get install build-essential
sudo apt-get install cmake git libgtk2.0-dev pkg-config libswscale-dev ffmpeg libavcodec-dev libavformat-dev libavdevice-dev
#以下可选
sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev
```

### 从github下载源码包
```
cd ~/
wget https://github.com/opencv/opencv/archive/3.2.0.zip #主模块
unzip 3.2.0.zip
wget https://github.com/opencv/opencv_contrib/archive/3.2.0.zip #拓展模块
unzip 3.2.0.zip
```

### 编译安装
```
cd opencv-3.2.0/
mkdir build
cd build 	#build目录
cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib-3.2.0/modules -D WITH_FFMPEG=ON -D WITH_TBB=ON -D WITH_GTK=ON -D WITH_V4L=ON -D WITH_OPENGL=ON -D WITH_CUBLAS=ON -DWITH_QT=OFF -DCUDA_NVCC_FLAGS="-D_FORCE_INLINES" –D WITH_QT=ON .. #cmake编译
make -j4
sudo make install #安装
```
安装完成后，在/usr/local/include放opencv头文件按，/usr/local/lib放库文件。(/usr/local/lib64)

### 配置, 添加库路径
```
cd /etc/ld.so.conf.d/
sudo touch opencv.conf
sudo echo "xxx/lib" > opencv.conf
sudo ldconfig
```
xxx/lib 为 opencv 动态库的安装目录，默认为：/usr/local/lib(/usr/local/lib64)

### 测试opencv

opencv内部集成了很多测试demo，可以通过执行这些demo看是否完成opencv的配置。

通过命令进入到demo中：

    cd opencv-4.2.0/samples/cpp/example_cmake

因为虚拟机可能使用不了摄像头的原因，我们就稍微的修改一下代码，让程序显示一张图片就好了。

    sudo vim example.cpp

修改保存后退出进行编译操作。

    sudo mkdir bulid
    cd bulid
    sudo cmake ..
    sudo make

编译完以会得到一个可执行文件，执行后就可以看见图片了。

### 测试 qt+opencv
打开qtcreator，新建工程，配置pro文件：
```
TEMPLATE = app
CONFIG += console c++11
CONFIG -= app_bundle
CONFIG -= qt

SOURCES += main.cpp

# INCLUDEPATH += /usr/local/include \
#                 /usr/local/include/opencv \
#                 /usr/local/include/opencv2
# LIBS += /usr/local/lib/lib*

INCLUDEPATH += /usr/local/include/opencv2
LIBS += /usr/local/lib64/lib*
```

源文件：
```
#include <iostream>
#include "opencv.hpp"

using namespace std;
using namespace cv;

int main()
{
    Mat img;
    img = imread("lena.jpg",-1);
    imshow("a", img);
    cvWaitKey(0);
    return 0;
}
```

注意：图片如果不写全路径，要放到项目的debug的目录下，并非源文件目录下，否则会终端窗口会出现：opencv error: assertion failed(size.width>0, size.height>0) in imshow …提示，无法显示图片。

当出现undefine reference symbols错误时，应该是pro文件中libs没有包含完整。


