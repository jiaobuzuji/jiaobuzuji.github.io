---
title: git-note
mathjax: true
comments: true
date: 2018-02-04 21:56:02
updated: 2018-02-04 21:56:02
categories:
    - Tools
    - git
tags: [git,revision]
---

## 前言

## submodule

### 删除 submodule

#### 未 commit

#### 已 commit 未 push

#### 已 push

#### 相关连接

https://segmentfault.com/a/1190000003076028#articleHeader2

## tag
$ git tag -a v1.4 -m "my version 1.4"

`git tag`

下载指定 tag 的源码: 'git clone --depth 1 -b <yourtags> https:xxx/xxx.git'

## stage

## remote
方法有很多，这里简单介绍几种：

以下均以项目git_test为例：
老地址：http://192.168.1.12:9797/john/git_test.git
新地址：http://192.168.100.235:9797/john/git_test.git
远程仓库名称： origin
方法一 通过命令直接修改远程地址

    进入git_test根目录
    git remote 查看所有远程仓库， git remote xxx 查看指定远程仓库地址
    git remote set-url origin http://192.168.100.235:9797/john/git_test.git

方法二 通过命令先删除再添加远程仓库

    进入git_test根目录
    git remote 查看所有远程仓库， git remote xxx 查看指定远程仓库地址
    git remote rm origin
    git remote add origin http://192.168.100.235:9797/john/git_test.git

方法三 直接修改配置文件

    进入git_test/.git

    vim config

    [core]
    repositoryformatversion = 0
    filemode = true
    logallrefupdates = true
    precomposeunicode = true
    [remote "origin"]
    url = http://192.168.100.235:9797/shimanqiang/assistant.git
    fetch = +refs/heads/*:refs/remotes/origin/*
    [branch "master"]
    remote = origin
    merge = refs/heads/master

    修改 [remote “origin”]下面的url即可

方法四 通过第三方git客户端修改。

以SourceTree为例，点击 仓库 -> 仓库配置 -> 远程仓库 即可管理此项目中配置的所有远程仓库， 而且这个界面最下方还可以点击编辑配置文件，同样可以完成方法三。
