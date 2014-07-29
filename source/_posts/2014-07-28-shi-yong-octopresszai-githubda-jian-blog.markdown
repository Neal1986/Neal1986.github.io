---
layout: post
title: "使用octopress在github搭建blog"
date: 2014-07-28 16:04:51 +0800
comments: true
categories: [Linux blog github]
tag: github octopress
keywords: octopress github
---

   经过两天的折腾终于把搞起了, 其中"艰辛"不在详述. 我这里主要根据自己在搭建的过程中遇到的问题写一个总结.

由于目前markdown(写博客使用的语法)还不熟练, 所以主要以文字为主, 不过我会尽量简单..

## 一、目的

   由于国内各大网站的blog系统带有广告及关键词限制, 所以在想法在一个open的平台上来搭建博客, 另外做技术的同学的想必都知道github, 所以这里是做技术博客最合适不过的地方.

## 二、搭建环境
   - 系统: centos6.5(其它linux通用)

Note: 如果准备用linux尝试搭建, 请准备好足够的耐心, 因为你在安装ruby及使用git时会遇到各种不同的问题(特指新手)

<!--more-->

## 三、步骤
   参考: [octopress 官方网站](http://octopress.org/docs/)

   1. 安装git, git官方说明版本兼容最好的是 1.7.10, 如果不是先不要急着重装, 将就用, 有问题再google.
   2. 安装ruby, 我用 rbenv 和 RVM 都试过, 建议使用rvm, 遇到的问题容易解决. rbenv安装中总碰到缺这缺那.
   这里安装可能会碰到以下问题:
    - javascript runtime的问题, 直接yum install notejs即可
    - ruby版本最好使用1.9.3, 当然大于些版本也可以,官网上有描述

> If ruby --version doesn't say you're using Ruby at least 1.9.3, revisit your rbenv or RVM installation

   3. 下载octopress源代码
      终端下执行:
      * git clone git://github.com/imathis/octopress.git octopress
      * cd octopresscd
   4. 安装依赖 bundler
      在执行以下命令时最好先切换ruby source(因为source托管在国外网站, 可能被GWF给毙掉)--> [taobao soucres](https://ruby.taobao.org)
      * gem install bundler
      * rbenv rehash    # If you use rbenv, rehash to be able to run the bundle command ,
      * bundle install  # 执行这个步骤前, 编辑 ~/octoperss/Gemfile, 把第一行也改为 taobao源

   5. 安装默认主题
      * rake install

至此安装已经完成.

## 四、提交博客系统

在github上新建一个repo, 命名 *githubusername*.github.io, githubusername需要替换成github用户名, 然后不用创建任何东西(github blog有两种, 本文为第一种, 还有一种是gh-pages)

  1. 生成ssh key, 详见 [生成ssh key](https://help.github.com/articles/generating-ssh-keys), 照着这个做. 这样你本地仓库可以与remote repo建议安全链接
  2. 终端执行
     * rake setup_github_pages,
     * rake generate,
     * rake deploy

这一步你的deploy里面的代码应该会提交到github对应repo. 在填写git repo link的时候 建议使用格式 git@github.com:username/username.github.io.git

## 五、写博客
  1. 同样官方网站说得非常清楚,所以说照着好的网站做肯定是少走弯路的. [write blog with markdonw](http://octopress.org/docs/blogging/)
  2. 终端执行
     > rake new_post["title"]

生成后缀名为markdown的文件, 这样就可以使用自己习惯的写作利器,推荐使用emacs,因为它的多模式实在太强大.
如何编辑markdown文件-> [markdown简单语法](http://jianshu.io/p/q81RER)

  3. 由于是静态博客系统, 所以每次完成写作后需要重新执行
     * rake generate,
     * rake deploy

这样你的博客就发布到github上了..

关于维护blog, 可以参考这篇文章: [维护blog](http://shanewfx.github.io/blog/2012/02/16/clone-blog-from-github/)
