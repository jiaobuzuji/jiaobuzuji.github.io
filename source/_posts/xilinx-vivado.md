---
title: xilinx-vivado
mathjax: true
comments: true
date: 2018-02-06 16:45:37
updated: 2018-02-06 16:45:37
categories:
  - ASIC
  - FPGA
tags: [xilinx, vivado]
---

## 前言

> 我所知道的不多。
> ——*某名人言*

## Start vivado
### Install
解压压缩包
然后直接执行 `xsetup`

source Xilinx/vivado/201x.x/setting.sh

### Uninstall
直接 `rm -rf`

## synthesis
使用 vivado 综合代码时，（只是 run systhesis，还没有开始 run implementation）
在 Utilization 的报告中：LUT LUTRAM FF IO DSP 这些初步报告都基本准确，但 BRAM
就十分不准确。BRAM 的报告需要在 run implementation 后才准确些。


## Meta
### Tip
1. vivado 在下板调试时，使用下面的命令，可以将波开数据导出来。
  `write_hw_ila_data -force -csv_file -verbose dump.csv`
1. add_files 可以用来添加 filelist。但不是很灵活。
1. 完成Implementation后，在Vivado IDE的Flow Navigator点击Open Implemented Design，然后选择report_utilization。在生成的结果中选中某一类资源，会看到按模块排列的资源占用情况。或者使用命令：`report_utilization -hierarchical`
