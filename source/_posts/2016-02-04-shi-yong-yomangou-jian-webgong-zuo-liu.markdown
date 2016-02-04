---
layout: post
title: "使用Yoman构建Web工作流"
date: 2016-02-04 18:10:12 +0800
comments: true
categories: 
---

####前言
最近公司的[web](https://www.jucaifu.com/)应用上线了，抽空总结一下项目的构建流程。项目是用[Yeoman](http://yeoman.io/)创建出来的。
####Yeoman简介
[Yeoman](http://yeoman.io/)是Google的团队和外部贡献者团队合作开发的，它的目标是通过[Yo](http://yeoman.io/),[Grunt](http://gruntjs.com/)和[Bower](http://bower.io/)为开发者创建一个易用Web应用的工作流，可以让网络前端开发者快速打造一个漂亮的网络应用(web applications)。

Yeoman的目的不仅是要为新项目建立工作流，同时还是为了解决前端开发所面临的诸多严重问题，比如各种零散的依赖关系 

####Yeoman的作用
1. Yo:Yeoman核心工具，构建目录结构或文件，生成项目的生产环境和编译环境
2. bower:Web开发的包管理器，类似于npm，npm专注于nodeJs模块，而bower专注于CSS、JavaScript、图像等前端相关内容的管理。需要注意的是，Bower的运行，依赖于版本控制工具git。要想成功安装Yeoman,需先安装 Git
3. grunt:自动化构建工具.当然也可以使用[gulp](http://gulpjs.com/) 
