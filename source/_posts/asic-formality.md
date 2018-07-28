---
title: Formality 工具
date: 2018-07-13 14:30:06
updated: 2018-07-13 14:30:10
comments: true
categories:
    - ASIC
tags: [formality]

---

## 环境

## undriven nets
一般的，设计中只要存在undriven（除inout IO、dft port），都需要在设计上修改，设计上不允许存在undriven。同时dc工具默认的是，只要存在undriven point，最后的网表都是会接0处理的。

解决方法：fm有一个系统变量verification_set_undriven_signals，如果你认为设计中存在的这些undriven points是你们期望的，那么该变量设置为synthesis即可；一般的是设置为默认值binary:x，这样设置就是为了确保一旦存在undriven point，fm的结果就会fail。

## Formality Ultra
这个 feature 默认是没有打开的，这个 feature 需要 license 支持。通过 `get_license FormalityUltra` 就可以打开

