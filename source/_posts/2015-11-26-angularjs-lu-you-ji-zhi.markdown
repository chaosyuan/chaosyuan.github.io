---
layout: post
title: "AngularJS 路由机制"
date: 2015-11-26 19:55:58 +0800
comments: true
categories: 
---

###前言

在谈路由机制前先说一下现在比较流行的单页面应用，就是所谓的Single Page APP。它的主要目的有

1. 单页面应用
2. 无刷新的视图切换 
3. 每个页面包含不同数据
   
虽然使用Javascript和Ajax可以实现上述功能，但是Ajax的一个致命缺点就是导致浏览器后退按钮失效，解决此问题有两种方法: 

1. 使用hash，监听hashchange事件来进行视图切换
2. 使用HTML5的history API，通过pushState()记录操作历史，监听popstate事件来进行视图切换
  
这样便形成了通过地址栏进行导航的深度链接(deeplinking ),也就是AngularJS的路由机制。通过路由机制，一个单页应用的各个视图就可以很好的组织起来了

####ngRoute  VS ui-router

ngRoute 和 ui-router
#####相同点

-  两者都可以实现路由功能

#####不同点

1. ngroute是用AngularJS框架的核心部分。
2. ui-router是一个社区库，它是用来提高完善ngroute路由功能的

[ui-router](https://github.com/angular-ui/ui-router)路由器属于一个第三方模块，功能非常强大。更轻松配置项目的一个模块库，是ngRouter的升级版！它支持一切正常ngroute也可以做许多额外的功能。所以实际运用中，常选择ui-router作为路由器，而不用ngRoute。

#####ngRoute



