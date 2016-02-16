---
layout: post
title: "Gruntfile.js  浅析"
date: 2016-02-05 17:57:36 +0800
comments: true
categories: 
---

####Grunt是什么
Grunt是一个基于JavaScript上的一个很强大的任务管理器(task runner)，就是用JavaScript去执行一些程序来完成一些任务。比如说验证HTML、CSS、JavaScript的语法，压缩CSS、JavaScript、图像，[Sass](http://www.w3cplus.com/sassguide/)、[LESS](http://less.bootcss.com/)和[Stylus](http://stylus-lang.com/)编译成CSS等等，这些东西很适合用Grunt来解决。

Grunt其实是一个基于NodeJS,可用于自动化构建、测试、生成文档的项目管理工具。可以在开发中提高效率、减少错误率。

Grunt不仅仅是构建工具，它还是任务运行器，管理每个子任务的自动化运行，它还可以做更多东西

####为什么要用Grunt

刚开始要用的时候很疑惑，为什么要用Grunt？在开发ios时候，相应的环境和配置文件配置好之后，直接Archive。很直接方便。其实这都是为了前端自动化。对于前端项目，代码维护、打包、发布等流程变得极为繁琐，同时浪费的时间和精力也越来越多，当然人为的错误也随着流程的增加而增加了更多的出错率。为了明确模块分工，我们可以会将JavaScript、CSS代码拆解成很多个模块，它们都有独立的一个个文件，但这样一来用来页面上，增加了文件个数，同时也产生过多的Http请求，从性能的角度来考虑，不利于页面优化。所以需要合并这些JavaScript和CSS文件。尽可能的将项目中的文件进行压缩.这样一来， 都希望有一种工具能代替手工操作，能帮助整个团队在开发中能精简流程、提高效率、减少错误率。

####Grunt能做什么

Grunt就是一个工具箱，拥有非常丰富的任务插件，可以帮助我们实现各式各样的构建目标。

1.  编译文档型：比如编译LESS、Sass、Stylus、Coffeescript等。
2.  文件操作型：比如说合并、压缩JavaScript、CSS、图片等
3.   质量保障型：比如[JSHint](http://jshint.com/)、[Mocha](https://github.com/mochajs/mocha)等
4.   类库构建型：比如说Backbone.js、ember.js、angular.js等

这些任务都依赖于Grunt提供的插件来完成的，但很多工作依旧需要在命令终端手工输入命令来完成这些操作。为此在Grunt中可以使用watch任务来实现一些监听文件改变、自动触发构建等功能。从而减少人工去每次操作任务

####Grunt安装
正常运行Grunt，必须先安装[Node.js](https://nodejs.org/en/)和[npm](https://www.npmjs.com/)

环境安装完成之后，终端输入命令安装grunt运行工具

      sudo  npm install -g grunt-cli
      
这条命令将会把grunt命令植入到系统路径中，这样就允许从任意目录来运行它(定位到任意目录运行grunt命令)

项目还有两个文件特别重要：**package.json**和**Gruntfile.js**，前者用于Nodejs包管理，比如Grunt插件安装，后者是Grunt配置文件，配置任务或者自定义任务

 1. package.json:用来存储已经作为npm模块发布的依赖模块。文件中列出项目所依赖的Grunt(配置Grunt版本)和Grunt插件(相应版本的插件)
 2. Gruntfile.js:用于配置或者定义Grunt任务和加载Grunt插件
 
需要注意的是这两个文件都放置在项目的根目录中，并且应该与项目的源代码一起提交。
