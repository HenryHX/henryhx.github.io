q---
layout: post
title: 'Hello Jekyll'
date: 2017-09-16
author: HenryHX
cover: '/assets/img/2017-09-16-hello-jekyll/jekyll-banner.png'
tags: jekyll blog
---

> 利用Jekyll和GitHub Pages搭建静态博客网站。

### 前言

话说，一直以来，我就有一个想法，搭建一个自己的博客，让自己更有归属感。博客园上开过户，Godaddy上买过域名和空间，用过wordpress, 也想自己完全搭建前后端，但是奈何，理想太丰满，每次都因为各种各样的原因没能坚持下去。

近来，在翻阅博客时就被一些简洁好看的样式给吸引住了，得知是jekyll，于是开始打算按照网上的大批教程，用jekyll搭建自己的博客，希望这次能坚持的久一些。

### Jekyll介绍

Jekyll 是一个简单的博客形态的静态站点生产机器。它有一个模版目录，其中包含原始文本格式的文档，通过一个转换器（如 Markdown）和我们的 Liquid 渲染器转化成一个完整的可发布的静态网站，你可以发布在任何你喜爱的服务器上。Jekyll 也可以运行在 GitHub Page 上，也就是说，你可以使用 GitHub 的服务来搭建你的项目页面、博客或者网站，而且是完全免费的。

### 搭建环境

Jekyll是运行在Ruby平台上的程序，所以，最开始要搭建Jekyll的工作环境，然后再安装Jekyll工具，全部安装成功后，你就可以使用Jekyll这个强有力的工具了。

#### 1. Ruby

> 该部分的环境安装和配置网上已经有了大量的教程，可以参考[jekyll博客搭建之艰辛之路](http://www.jianshu.com/p/27de87d4447e)中的第一步到第五步。

```.	
* 安装 [Ruby](https://rubyinstaller.org/downloads/) 和 [Ruby Development Kit](https://rubyinstaller.org/downloads/)
* 切换到Devkit的安装目录下，执行 ruby dk.rb init，会自动生成config.yml配置文件 
* ruby dk.rb install
* 更改默认的source源：gem sources --add http://gems.ruby-china.org/ --remove http://rubygems.org/
* gem install bundler
* gem install jekyll
```		
#### 2. Github Pages

最近发现 github 改版了，已没有像原来的 Launch automatic page generator 这样的按钮。本节所有过程可能在不同的情况下会有少数不同,但基本如此，大家可根据自己的情况亲自测试。本节参考自[如何在Github Pages搭建自己写的页面？](http://www.cnblogs.com/lijiayi/p/githubpages.html)

**步骤一：**登录到Github上，新建一个repository，命名为`[username].github.io` ，勾选 initialize this repository with a README，点击create repository。当然repository名字也可以自定义，如 `test` ，前一种命名的好处在于，通过`https://[username].github.io`即可访问博客首页，而后一种复杂一点 `https://[username].github.io/test`.

**步骤二：**打开settings，有一个Github Pages 的设置，点击 source 中的本来的 None ，使其变成 master 分支，也就是作为部署github pages 的分支，然后点击 save。
![github-page-setting.jpg](/assets/img/2017-09-16-hello-jekyll/github-page-setting.jpg "Github Pages")
页面刷新之后，再看 github pages 设置框处，多了一行网址，就是你的 github pages 的网址了。

**步骤三：**将对应的项目拷贝到自己本地的目录下，就可以开始自定义博客样式和内容啦^-^

### 样式设置

可以去github上fork一个自己喜欢的jekyll模板，如果没有自己喜欢的，可以考虑自己设计模板。
![jekyll-catalogue.jpg](/assets/img/2017-09-16-hello-jekyll/jekyll-catalogue.jpg "jekyll 目录")

在自定义博客之前，确保你对[Jekyll目录结构](http://jekyll.com.cn/docs/structure/)有所了解。
```			.
	├── _config.yml # 配置文件
	├── _includes # 页面组件方便重用
	|   ├── footer.html # 页脚
	|   └── head.html # html文档的头部内容
	|   └── header.html # 顶部菜单栏
	|   └── pageNav.html # 文章列表分页组件
	├── _layouts # 布局模板
	|   ├── default.html # 默认模板
	|   └── post.html # 文章页面模板
	├── _posts # 这里放文章
	|   ├── 2017-05-03-elements-of-javascript-style.md # 命名格式：年-月-日-文章标题.md
	|   └── 2007-02-21-life-on-mars.md
	├── _site # Jekyll将源码处理后生成的站点文件，里面的内容可直接发布
	├── assets # 存放用于线上环境的静态资源，如需修改css和js文件请到dev文件夹
	|   ├── css # dev文件夹中sass编译后的样式文件
	|   └── fonts # 字体文件
	|   └── icons # 图标文件
	|   └── img #  图片文件
	|   └── js # dev文件夹中处理后的脚本文件
	├── dev # 开发文件
	|   ├── js # 存放脚本源码
	|   └── sass # 样式源码
	|       └── app.scss # 整合下面的所有样式文件
	|       └── base.scss # 引入字体、Reset部分样式
	|       └── common.scss # 模板的主要样式
	|       └── helper.scss # 工具样式
	|       └── layouts.scss # 响应式布局
	└── gulpfile.js # 自动化任务脚本
	└── index.html # 模板首页
	└── tags.html # 标签页面
	└── 404.html # 404页面
	└── package.json # 管理项目的依赖项
```
修改完成后，可以使用Jekyll服务`$ jeklly serve`开启博客的本地预览。通过以上命令，将针对项目 jekyll-blog 生成 _site 目录，并启动本地服务器。 过一会后如果有以下提示，则代表正常运行。
```.
Server address: http://127.0.0.1:4000/
Server running... press ctrl-c to stop.
```
在浏览器访问以下网址`http://127.0.0.1:4000/`即可看到博客站点。

### 发布
本地确认文章无误，可以通过git add,git commit,git push等git命令推送Jekyll项目到Github Pages服务器，这样代码便部署上去了。之后即可通过网址`[username].github.io`（在Github Pages步骤一配置）直接访问到个人博客咯。

### 绑定域名
打开settings，有一个`Github Pages`的设置，在`Custom domain`内输入期望绑定的域名，然后点击 Save,此时Github将会在repository中添加一个文件`CNAME`。
![github-page-setting.jpg](/assets/img/2017-09-16-hello-jekyll/github-page-setting.jpg "Github Pages")

购买域名的方式还是挺多的,我个人比较倾向于Godaddy，支付的时候选择**支付宝**就可以了。域名已经买完了，直接是没法用的，因为没有进行DNS解析，别人是没法正常通过域名访问你的页面的，国内的话推荐使用DNSPod进行解析，服务免费稳定，而且速度行业比较快。

具体修改DNS方法可以参考DNSPod官方教程：[Godaddy注册商域名修改DNS地址](https://support.dnspod.cn/Kb/showarticle/tsid/42/)

此外还需在DNSPod自己的域名下添加两条A记录，地址就是Github Pages的服务IP地址：`192.30.252.153`和`192.30.252.154`。
![dns.jpg](/assets/img/2017-09-16-hello-jekyll/dns.jpg "dns设置")

至此，所有的内容就完成了，需要等一阵子，时间不超过72小时(一般很快），让域名切换到新的解析DNS上。之后你可以直接访问你的域名看看是不是成功了。

### 总结
**今天一小步 未来一大步**
![summary.jpg](/assets/img/2017-09-16-hello-jekyll/summary.jpg "总结")