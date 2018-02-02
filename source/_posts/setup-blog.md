---
title: HEXO 零基础搭建个人 Blog
date: 2018-01-27 22:46:15
updated: 2018-01-31 22:46:15
comments: true
categories:
    - 杂记
tags: [hexo,blog]

---

## 前言
本人不是网页前端设计专业，一切技术对于我来说都是新的，所以有必要将之前折腾的东西记录下。

### 最初的做法
一开始折腾 Github Page 的时候是在 2016 年，那里 Hexo 刚兴起不久，只有耳闻，却没有涉及，而是选择了一个更加冷门的方法：同样使用 Markdown 书写博客，再由[pandoc][]转成 html ，页面布局直接模(chao)仿(xi)大神的[template](https://github.com/tzengyuxio/pages/blob/gh-pages/pandoc/pm-template.html5)。在此感谢这些鬼东西一直在折磨我，留言以纪念。

> 一直在模仿，从未有超越

#### 相关连接
http://pages.tzengyuxio.me/pandoc/
http://pandoc.org/
[pandoc]: http://pandoc.org/

### 关于本博客
而今，Hexo 已经十分成熟，在国人的群策群力，Hexo 已经开始赶超 Jekyll 。作为中国人，当然得支持国货啦（中文教程网络上是一打一打的）
1. 以 hexo 为 blog framework
1. 以 next 为主题
1. 部署在 Github Page 上
1. 在 namesilo 购买个人域名，支持支付宝。
1. 使用 Valine 作为评论区，需要注册 LearnCloud.cn
1. 使用微博作为图床

---

## 免费搭建个人博客
重点当然在于免费啦～
**NOTE** : 大部分域名都是需要购买的，也有一些是可以免费注册的，但可能不遵守 ICANN 。

### 注册与基本安装
1. Github 或者 Coding
1. 七牛 或者 微博
1. 如果想弄个人域名，则可能还需要注册 万网，GoDaddy, 或者 namesilo 等域名商，还可注册 DNSpod


1. 创建 Github Page
1. 安装 Nodejs, Hexo，[Github Desktop](https://desktop.github.com/) 或者 git
1. 认真阅读一下 Next 的帮助文件，然后使用 npm 安装好插件
  - 利用 `--save` 可以将插件保存在 package.json 中，如： `npm install --save hexo-generator-sitemap`
1. 域名加密 https，可能还需要了解 [let's encrypt](https://letsencrypt.org/)
1. ~~安装 gem，travis。gem 是 ruby 下的东西，所以要安装 ruby。Ubuntu 下可以使用下面命令行安装~~
  ```bash
  sudo apt-get instal -y ruby ruby-dev
  sudo gem install travis
  ```
**NOTE 1** : 建议尽量安装新版本的
**NOTE 2** : 官网的教程一般最详细有效的，但合理的借助他人的总结，也有助于快速入门

### 规划
规划是指合理安排文件的位置，这里就简单说明一下我的规划。Github Page 里面规定，`username.github.io` 这个 repos 必须以 master 这个 branch 作为 Github Page。为了保证 Page 与源码同一个 repo，所以我新建了一个叫 `source` 的 branch ，然后让 Hexo 自动部署在这个 repo 的 master 上面。当然你也可以 'Page' 与 'source' 放在两个不同的 repo 中，你钟意咯。

**Github Page 可以不止一个！！**Github Page 有两种： *User/Organization Pages and Project Pages* ，User Page（username.github.io）只能一个，但 Project Page（普通的 repo）可以多个，Project Page 可以利用 CNAME 来创建个人域名中的 subdomain。

**NOTE** : namesilo 可以创建 50 个 subdomain

#### 相关连接
https://help.github.com/articles/user-organization-and-project-pages/


### 搭建
开始之前，一定要先好好阅读 Hexo 的文档，使用 Next 也要好好先阅读一下使用文档，通读两三遍也算是正常的。然后开工。

```bash
hexo init myblog
cd myblog
npm install
git clone https://github.com/theme-next/hexo-theme-next/ themes/next
sed -i "s~landscape~next~" _config.yml # 修改主题为 next
hexo g # generate
hexo s # server
```

然后可以使用浏览器输入：localhost:4000 进行预览了

```bash
mdkir -p source/_data
cp themes/next/_config.yml source/_data/next.yml
```

通过修改 next.yml 就可以简单地自定义 next 主题了。


```bash
hexo new page tags

```

在 /source/tags/index.md 中，修改一下，添加 type，如下所示：

```
---
title: Tags
date: xxxxxxxx
type: "tags"
---
```

#### 图床


#### 评论区

使用 github 的 issue 来作为评论区。有一个更好的，叫： gitalk
但是因为是使用 github 的资源，而且每一个 pages 都要手动去初始化。不是很方便，还是放弃了。
换成无后台的 valine


### 自动部署

其实现在 travis-ci.org 已经提示了 env 的设置了，而且还自动帮你加密了，可以不用自己加密了。
在使用 github 生成 token 时，有两个地方要注意：
1，生成时， token 只会出现一次，下次打开时，就不可见了。所以要及时保存可以用掉
2，要使用 token 来部署网站，所以此 token 必须拥有一些功能。例如：repo，gist。这两个选项得及时勾上。

  代替的方案是直接在 travis-ci.org 中设置环境变量


### SEO

github 禁止百度的 spider，所以百度无法自动检索到个人建的 blog 。这里采用自制提交。
https://www.bing.com/webmaster
https://ziyuan.baidu.com
https://www.google.com/webmasters


### Tip

windows 下创建 link 的正确方法。
mklink /j themes\next\source\lib\fancybox themes\next-fancybox3
mklink /j themes\next\source\lib\fastclick themes\next-fastclick
mklink /j themes\next\source\lib\jquery_lazyload themes\next-jquery-lazyload

windows 下，markdow-preview.vim 得先安装好 python3，并添加环境变量。


[MkdWiki]: https://en.wikipedia.org/wiki/Markdown
[Markdown]: https://daringfireball.net/projects/markdown/syntax
[GFM]: https://guides.github.com/features/mastering-markdown
[Jekyll]: https://jekyllrb.com/

