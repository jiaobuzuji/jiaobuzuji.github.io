---
title: HEXO 零基础搭建个人 Blog
date: 2018-01-27 22:46:15
comments: true
categories:
    - 杂记
tags:
    - hexo
    - blog
---

## 前言
本人不是网页前端设计专业，一切技术对于我来说都是新的，所以有必要将之前折腾的东西记录下。

~由[pandoc][]转成html，页面布局是使用了网络某大神的[template][]。~

[template]: https://github.com/tzengyuxio/pages/blob/gh-pages/pandoc/pm-template.html5
[Markdown]: https://en.wikipedia.org/wiki/Markdown
[pandoc]: http://pandoc.org/


https://daringfireball.net/projects/markdown/syntax

gem 是 ruby 下的，所以要安装 ruby
sudo apt-get instal -y ruby ruby-dev
sudo gem install travis

其实现在 travis-ci.org 已经提示了 env 的设置了，而且还自动帮你加密了，可以不用自己加密了。
在使用 github 生成 token 时，有两个地方要注意：
1，生成时， token 只会出现一次，下次打开时，就不可见了。所以要及时保存可以用掉
2，要使用 token 来部署网站，所以此 token 必须拥有一些功能。例如：repo，gist。这两个选项得及时勾上。


github 禁止百度的 spider，所以百度无法自动检索到个人建的 blog 。这里采用自制提交。
https://www.bing.com/webmaster
https://ziyuan.baidu.com

hexo new page tags
在 /source/tags/index.md 中，修改一下，添加 type，如下所示：

---
title: Tags
date: xxxxxxxx
type: "tags"
---

使用 github 的 issue 来作为评论区。有一个更好的，叫： gitalk

