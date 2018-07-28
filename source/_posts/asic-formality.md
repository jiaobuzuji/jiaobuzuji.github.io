---
title: Formality 工具
date: 2018-07-13 14:30:06
updated: 2018-07-13 14:30:10
comments: true
categories:
    - ASIC
tags: [formality]

---

## 别人的
一、 总结：

解决dc综合出现fail点的基本思路：
1. set synopsys_auto_setup true，记得undriven的选项单独再设置一下，undriven要识别成不定态x，对设计验证更充分。
2. set_svf，能更好的进行name_rule匹配，门控匹配等等。
3. dft_mode/test_mode/bypass_mode等，要设置为常值，只检查正常工作模式。因为这几个测试模式，设计主要依赖后端团队；pre-gate步骤不需要关心。
4. dc设置的set_case_analysis，要都设置在formality里。因为当前formality的主要意义，是检查综合步骤的正确性。
5. 出现failing point，第一步骤是依赖formality工具的analysis功能；会有基本分类，有些分类，一眼就能看出问题原因，比如undriven导致的比较失败。这一步骤，只解决简单的问题。
6. pattern视角分析。留意c0/c1/und关键词。留意reference/implement两栏中，有一栏是空的电路逻辑图。
7. 电路逻辑图，结合pattern一起分析，debug更有效果。
8. 另外，综合前，做nLint静态代码检查，是很有必要的。formality检查之前，看综合日志也是很有必要的。流程完整，可以节省很多不必要的debug时间。
二、 netlist和svf的配套一致

gpu当前的综合过程是
1. 底层IP先综合成网表；
2. gpu_top_wrapper，吃底层IP的网表进行的顶层综合。

formality对应的，应是这样的步骤
1. 底层IP formality，吃IP综合的svf。比较的是ip-rtl和ip-netlist
2. 顶层formality，吃底层IP综合的ip-netlist。比较的是（顶层-rtl+底层ip-netlist）和gpu_top_wrapper-netlist
之前是（顶层-rtl+底层ip-rtl）和gpu_top_wrapper-netlist。这种方法，比较出来的结果是大量不匹配；原因估计是svf文件无法对应，因为gpu顶层吃的是底层IP网表。
三、 formality遇到的坑

    set verification_clock_gate_hold_mode collapse_all_cg_cells【解决clock gating问题】
    set synopsys_auto_setup true【结合svf，更大限度的减少不必要的比对。】
    以上两点，在set_svf命令之前设置。
    因为formality验证，主要是应用于检查综合结果质量。如果综合过程中遇到set_case_analysis的设置，就需要在formality里设置相应端口为固定值。

四、 pattern match视角

    pattern是指出现fail point的比较码；
    留意c0/c1、und这样的关键词，是constant常值0、1、undriven的意思。
    先解决undriven

五、 IP/IO/standcell的处理

    按照综合流程的吃法，都吃.lib即可。
    尽量不用blackbox；blackbox，因为输入输出端口的原因，经常给formality检查带来不必要的困难麻烦。

## undriven nets
一般的，设计中只要存在undriven（除inout IO、dft port），都需要在设计上修改，设计上不允许存在undriven。同时dc工具默认的是，只要存在undriven point，最后的网表都是会接0处理的。

解决方法：fm有一个系统变量verification_set_undriven_signals，如果你认为设计中存在的这些undriven points是你们期望的，那么该变量设置为synthesis即可；一般的是设置为默认值binary:x，这样设置就是为了确保一旦存在undriven point，fm的结果就会fail。

## Formality Ultra
这个 feature 默认是没有打开的，这个 feature 需要 license 支持。通过 `get_license FormalityUltra` 就可以打开

