---
title: HEXO 零基础搭建个人 Blog
date: 2018-01-27 22:46:15
updated: 2018-07-17 23:33:12
comments: true
categories:
    - Misc
tags: [hexo,blog,markdown]

---

## 前言
本人不是网页前端设计专业，一切技术对于我来说都是新的，所以有必要将之前折腾的东西记录下。

### 最初的做法
一开始折腾 [Github Page][] 的时候是在 2015 年，那里 [Hexo] 刚兴起不久，只有耳闻，却没有涉及，而是选择了一个更加冷门的方法：同样使用 [Markdown][] 书写博客，再由[pandoc][]转成 html ，页面布局直接模(chao)仿(xi)大神的[template](https://github.com/tzengyuxio/pages/blob/gh-pages/pandoc/pm-template.html5)。在此感谢这些鬼东西一直在折磨我，留言以纪念。

> 一直在模仿，从未有超越
> ——*某名人言*

#### 相关连接
* http://pages.tzengyuxio.me/pandoc/
* http://pandoc.org/

[pandoc]: http://pandoc.org/

### 关于本博客
而今，[Hexo] 已经十分成熟，在国人的群策群力之下，[Hexo] 已经开始赶超 [Jekyll][] 。作为中国人，当然得支持国货啦（重点是中文！中文！！中文！！）
1. 以 [Hexo] 为 Blog framework
1. 以 [Next][] 为主题
1. 部署在 [Github Page][] 上
1. 在 [namesilo][] 购买个人域名，支持支付宝。
1. 使用 Valine 作为评论区，需要注册 [LeanCloud][]
1. 使用微博作为图床

---

## 免费搭建个人博客
重点当然在于免费啦～
**NOTE** : 大部分域名都是需要购买的，也有一些是可以免费注册的，但可能不遵守 ICANN 。

### 注册与基本安装
1. [Github][] 或者 Coding
1. 七牛 或者 微博
1. 如果想弄个人域名，则可能还需要注册 万网，GoDaddy, 或者 [namesilo][] 等域名商，还可注册 DNSpod


1. 创建 [Github Page][]
1. 安装 Nodejs, [Hexo]，[Github Desktop](https://desktop.github.com/) 或者 git
1. 认真阅读一下 [Next][] 的帮助文件，然后使用 npm 安装好插件
  * 利用 `--save` 可以将插件保存在 package.json 中，如： `npm install --save hexo-generator-sitemap`
1. 域名加密 https，可能还需要了解 [let's encrypt](https://letsencrypt.org/)
1. ~~安装 gem，travis。gem 是 ruby 下的东西，所以要安装 ruby。Ubuntu 下可以使用下面命令行安装~~
  ``` bash
  $ sudo apt-get instal -y ruby ruby-dev
  $ sudo gem install travis
  ```
**NOTE 1** : 建议尽量安装新版本的
**NOTE 2** : 官网的教程一般最详细有效的，但合理的借助他人的总结，也有助于快速入门

### 规划
规划是指合理安排文件的位置，这里就简单说明一下我的规划。[Github Page][] 里面规定，`username.github.io` 这个 repos 必须以 master 这个 branch 作为 [Github Page][]。为了保证 Page 与源码同一个 repo，所以我新建了一个叫 `source` 的 branch ，然后让 [Hexo] 自动部署在这个 repo 的 master 上面。当然你也可以 'Page' 与 'source' 放在两个不同的 repo 中，你钟意咯。

**[Github Page][] 可以不止一个！！**[Github Page][] 有两种： *User/Organization Pages and Project Pages* ，User Page（username.github.io）只能一个，但 Project Page（普通的 repo）可以多个，Project Page 可以利用 CNAME 来创建个人域名中的 subdomain。

**NOTE** : [namesilo][] 可以创建 50 个 subdomain

#### 相关连接
https://help.github.com/articles/user-organization-and-project-pages/

### 搭建
[Hexo] 中非常多好看的主题，本着少折腾的基本原则，选择大众所爱，且有一个大团队在维护的 [Next][] 主题。开始之前，一定要先好好阅读 [Hexo] 的文档，使用 [Next][] 也要好好先阅读一下使用文档，通读两三遍也算是正常的。然后开工。

#### Ubuntu 上安装 Node.js

##### 直接安装
1. 安装
``` bash
sudo apt-get install nodejs
sudo apt-get install npm
```
1. 升级 npm
``` bash
sudo npm install npm -g
/usr/local/bin/npm -> /usr/local/lib/node_modules/npm/bin/npm-cli.js
npm@2.14.2 /usr/local/lib/node_modules/npm
```
1. 升级 node.js
``` bash
sudo npm install –g n
sudo n latest(升级node.js到最新版)  or $ n stable（升级node.js到最新稳定版）
n后面也可以跟随版本号比如：`n v0.10.26` 或者 `n 0.10.26`
```

1. 卸载
先卸载 npm , 后卸载nodejs
``` bash
sudo npm uninstall npm -g
sudo apt-get remove nodejs
```
##### nvm安装
``` bash
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash
```
安装成功后,需要关闭xshell，重新启动。nvm才会生效。使用 `command -v nvm` 查看 nvm 是否安装成功。

通过 `nvm ls` 查看已安装的版本，通过 `nvm ls-remote` 查看可使用版本

``` bash
$ nvm ls-remote
        v0.1.14
        v0.1.15
        v0.1.16
        v0.1.17
        v0.1.18
...
```
安装nodejs, 通过 `nvm install 7.8.0` 来安装，后面的版本号我们可以任意选择


##### npm镜像替换为淘宝镜像

``` bash
npm get registry 
```
> https://registry.npmjs.org/

设成淘宝的

``` bash
npm config set registry http://registry.npm.taobao.org/
```

换成原来的
``` bash
npm config set registry https://registry.npmjs.org/
```

##### 选装cnpm (推荐！！)
1. 说明：因为npm安装插件是从国外服务器下载，受网络影响大，可能出现异常，如果npm的服务器在中国就好了，所以我们乐于分享的淘宝团队干了这事。！来自官网：“这是一个完整 npmjs.org 镜像，你可以用此代替官方版本(只读)，同步频率目前为 10分钟 一次以保证尽量与官方服务同步。”；
1. 官方网址：http://npm.taobao.org；
1. 命令安装；注意：安装完后最好查看其版本号 `cnpm -v` 或关闭命令提示符重新打开，安装完直接使用有可能会出现错误；
``` bash
npm install cnpm -g --registry=https://registry.npm.taobao.org
```
注：cnpm跟npm用法完全一致，只是在执行命令时将npm改为cnpm

##### 全局安装与本地安装
npm 的包安装分为本地安装（local）、全局安装（global）两种，从敲的命令行来看，差别只是有没有 `-g` 而已，比如我们使用 npm 命令安装常用的 Node.js、web框架模块、express等

``` bash
$ npm install express          # 本地安装
$ npm install express -g       # 全局安装
```

#### 初始化 Blog
``` bash
$ hexo init myblog
$ cd myblog
$ npm install
$ git clone https://github.com/theme-next/hexo-theme-next/ themes/next
$ sed -i "s~landscape~next~" _config.yml # 修改主题为 next
$ hexo g -f # force generate
$ hexo s -o # server and open browser
```
然后就可以在浏览器进行预览了。Hexo 的插件有很多，请自行安装。

#### 自定义主题
``` bash
$ mdkir -p source/_data
$ cp themes/next/_config.yml source/_data/next.yml
```
通过修改 next.yml 就可以简单地自定义 [Next][] 主题了。添加 keyword, 修改 scheme 等等。**这些对于你来说，太简单了。**

#### 创建 Tags 与 Categories
``` bash
$ hexo new page tags
$ hexo new page categories
```

在 /source/tags/index.md 中，修改一下，添加 type，如下所示：

```raw
---
title: Tags
date: xxxxxxxx
type: "tags"
---
```

同理，在 /source/categories/index.md 中，修改一下，添加 type，如下所示：

```raw
---
title: Categories
date: xxxxxxxx
type: "categories"
---
```

#### 图床
1. [Github][] 本身就可以作为图床，但由于 [Github][] 主要是项目管理，资源比较珍贵，所以我们有理由找其他来代替。
1. 七牛作为图床，也是非常好的，但会限制流量，限制空间，然而对于我这种小 Blog，足矣，所以是一个不错的选择。
1. 微博免费提供了图片的外链功能，可作为图床，且不限制流量，不限制空间，是图床的绝佳选择。
  * 未来有可能不能外链
  * 外链的 URL 没有规律，到时换图床会比较麻烦

将图片上传到微博相册，然后鼠标右键就可以复制出 URL 了。有许多插件可以批量上传到微博，同时快速复制 URL。其实微博本身就提供了批量上传图片功能。有了 URL ，就可以肆意用了～

#### 评论区
1. disqus : (已经被 GFW 封杀，不谢)
1. gitment : 使用 [Github][] 的 issue 来作为评论区。邮件提醒，支持 [GFM][]。
1. gitalk : 有团队在维护，且更加炫，与 gitment 同理。
1. Valine : 无后台运行，以 [LeanCloud][] 云服务，但邮件提醒比较麻烦
1. hexo-gitter :

但是因为是使用 [Github][] 的资源，而且每一个 pages 都要手动去初始化，不是很方便，还有一个重要的原因，就是 issue 是不能由博主删除，同时利用 [Markdown][] 连接的图片，会自动上传到 [Github][] 上面去。最后选择无后台的 valine

#### 写文
刀已擦亮，博已建成。开写：

``` bash
$ hexo new "My New Post"
```

#### 相关连接
* https://xuanwo.org/2015/03/26/hexo-intor/
* https://zhuanlan.zhihu.com/p/26625249
* <http://ibruce.info/2013/11/22/hexo-your-blog/>

### 自动部署
使用 `hexo d` 可以实现部署，但每次 `git push` 后，必须在次手动部署一下，实在是比较麻烦，此时可以利用 [Github][] 与 Travis-CI 来实现 push 时自动部署功能。

在使用 [Github][] 生成 token 时，有两个地方要注意：
1. 生成时， token 只会出现一次，下次打开时，就不可见了。所以要及时保存可以用掉
1. 要使用 token 来部署网站，所以此 token 必须拥有一些权限。例如：repo，gist。这两个选项得勾上。
1. ~~使用 travis 命令行对 token 进行加密~~

其实现在 Travis-CI 已经可以 env 的设置了，而且还自动帮你加密了，可以不用自己加密了。所以可以不用安装 ruby, gem, travis

#### 相关连接
* http://notes.iissnan.com/2016/publishing-github-pages-with-travis-ci/
* http://lotabout.me/2016/Hexo-Auto-Deploy-to-Github/

### SEO
[Github][] 禁止百度的 Spider，所以百度无法自动检索到我们建的博客。[Next][] 主题中已经集成了百度的自制提交，直接使用就是了。然后就是 Bing，Google，提交 sitemap。更多的 SEO 方法，我就不折腾了。

https://www.bing.com/webmaster
https://ziyuan.baidu.com
https://www.google.com/webmasters

#### 相关连接
* https://www.jianshu.com/p/7e1166eb412a

## Meta
### Tip
1. `hexo clean` 可以简写为 `hexo cl`
1. `hexo s` 之后，hexo 会监视文件更改，只要博文一保存，就可以刷新浏览器看效果。使用 `hexo s -s`
1. ~~windows 下，markdow-preview.vim 得先安装好 python3，并添加环境变量。~~
1. 使用 npm 更新 package.json 中的版本
  ```nodejs
  npm update -S
  npm update –D
  ```

### 曾想仗剑走天涯
> 曾想仗剑走天涯
> 后来工作忙没去

狂妄地以为自己可以制作心中的 [Hexo] 主题，所以受一些东西折磨：
1. swig, ejs，都是 JavaScript 模板，可以快速生成 js 代码。还去 [w3school](http://www.w3school.com.cn/) 瞎逛自学了有一阵。
1. scss 与 css
1. cdn, avatar, FontAwesome
1. 各种 Brower 之间的不兼容
1. 使用 gulp 对 html 的优化

### 相关连接
* http://www.w3school.com.cn/
* http://node-swig.github.io/swig-templates/docs/#variables

[MkdWiki]: https://en.wikipedia.org/wiki/Markdown "Markdown Wiki"
[Markdown]: https://daringfireball.net/projects/markdown/syntax
[GFM]: https://guides.github.com/features/mastering-markdown
[Jekyll]: https://jekyllrb.com/ "Jekyll's Homepage"
[LeanCloud]: https://leancloud.cn
[namesilo]: https://www.namesilo.com/
[Github Page]: https://help.github.com/articles/what-is-github-pages/
[Github]: https://github.com/ "Github's Homepage"
[Hexo]: https://hexo.io/ "Hexo's Homepage"
[Next]: http://theme-next.iissnan.com/
