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

## build source
为什么要 build git 呢？因为在 CentOS 6 中，git 的版本很旧，只有 git 1.7.x
版本。些版本的 git 似乎没有 cache 的样子，每次的 git status 都要整个 git 查找
从而导致很卡，而 oh-my-zsh，每次都会去查看一下 git 目录下的状态，进而十分影响
用户体验

$ git clone https://github.com/git/git
$ cd git
$ make
or
$ make prefix=/usr all doc info ;# as yourself
or
$ make prefix=/usr install install-doc install-html install-info ;# as root
上面的语句，可以安装 manual。安装完后，就可以使用 man git 来查看最新的 git 了。

...
http-push.c:920: error: ‘xml_cdata’ undeclared (first use in this function)
http-push.c:921: warning: implicit declaration of function ‘XML_Parse’
http-push.c:926: warning: implicit declaration of function ‘XML_ErrorString’
http-push.c:927: warning: implicit declaration of function ‘XML_GetErrorCode’
http-push.c:930: warning: implicit declaration of function ‘XML_ParserFree’
http-push.c: In function ‘remote_ls’:
http-push.c:1154: error: ‘XML_Parser’ undeclared (first use in this function)
http-push.c:1154: error: expected ‘;’ before ‘parser’
http-push.c:1161: error: ‘parser’ undeclared (first use in this function)
http-push.c:1164: error: ‘xml_cdata’ undeclared (first use in this function)
http-push.c: In function ‘locking_available’:
http-push.c:1228: error: ‘XML_Parser’ undeclared (first use in this function)
http-push.c:1228: error: expected ‘;’ before ‘parser’
http-push.c:1235: error: ‘parser’ undeclared (first use in this function)

Oh man, compile errors. Bad.

The key seemed to be:
expat.h: No such file or directory

A bit o Googling: missing library headers.
$ sudo yum install expat-devel

Restart the build. Works.

$ make install

Whee!
$ git --version
git version 1.8.3.2.768.g911011a

ps. Dont forget the
export PATH=~/bin:$PATH

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

git describe --- 显示当前离当前提交最近的tag

如果符合条件的tag指向最新提交则只是显示tag的名字，否则会有相关的后缀来描述该tag之后有多少次提交以及最新的提交commit id。不加任何参数的情况下，git describe 只会列出带有注释的tag

获取最新的 tags
```
git describe --exact-match --tags HEAD 2>&1
```
另外一个方案
```
git describe --tags `git rev-list --tags --max-count=1`
```

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

