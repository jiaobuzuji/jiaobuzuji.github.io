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

第八步，测试opencv

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
