---
title: 人体检测算法 HOG
date: 2014-12-07 22:46:15
updated: 2014-12-07 22:46:15
comments: true
categories:
    - 数字图像
tags: [识别]
---

人体检测
==============
HOG的人体检测，算法的好坏，也只能靠统计的方法来定量衡量。

训练
--------------

HOG特征
--------------

SVM分类
--------------


拓展一下
--------------


软硬结合的实现方案
==============


RTL
--------------
HOG特征值，是占用最计算量，在使用CPU软件实现方案中消耗最长的计算时间。为提高实时性，此部分必须由RTL来提速。


Software
--------------


### 视频计时基准码（SAV，EAV）
现存在两个计时基准信号，一个位于各视频数据块的发端（活跃视频的起点，SAV），另一个则位于各视频数据块的末端（活跃视频的终点，EAV）。

    ... U Y V Y U Y V Y FF 00 00 XY 80 10 80 10 ... 80 10 80 10 FF 00 00 XY U Y V Y U Y V Y ...

每个计时基准信号包括一个四字序列，格式为：FF 00 00 XY。（数值用十六进制计数法表示。）前三个字是固定的前导码。第四个字包括的信息定义场2的标识，场消隐的状态和行消隐的状态。

| 数据比特编号    | 第一个字   | 第二个字  | 第三个字  | 第四个字（XY）   |
|:------:+:-----:+:----:+:----:+:-----:|
|  9(MSB)   |  1  | 0  | 0  |  1  |
|  8   |  1  | 0  | 0  |  F  |
|  7   |  1  | 0  | 0  |  V  |
|  6   |  1  | 0  | 0  |  H  |
|  5   |  1  | 0  | 0  |  P3  |
|  4   |  1  | 0  | 0  |  P2  |
|  3   |  1  | 0  | 0  |  P1  |
|  2   |  1  | 0  | 0  |  P0  |
|  1   |  1  | 0  | 0  |  0  |
|  0(LSB)   |  1  | 0  | 0  |  0  |



困难重重
==============

