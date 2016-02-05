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

Yeoman的目的不仅是要为新项目建立工作流，同时还是为了解决前端开发所面临的诸多严重问题，比如各种零散的依赖关系. 

####Yeoman的作用
1. Yo:Yeoman核心工具，构建目录结构或文件，生成项目的生产环境和编译环境.
2. bower:Web开发的包管理器，类似于npm，npm专注于nodeJs模块，而bower专注于CSS、JavaScript、图像等前端相关内容的管理。需要注意的是，Bower的运行，依赖于版本控制工具git。要想成功安装Yeoman,需先安装 Git.
3. grunt:自动化构建工具.当然也可以使用[gulp].(http://gulpjs.com/) 

####Yeoman的安装
在安装Yeoman之前，需要安装： [NodeJS](https://nodejs.org/en/)

    yuanweideMBP:~ yuanwei$ node -v
    v5.5.0
    yuanweideMBP:~ yuanwei$ npm -v
    3.3.12


安装yeoman,grunt,bower,-g 安装在全局范围内，任何地方都能调用到相应命令

    sudo npm install -g yo bower grunt-cli gulp

[查看更多的构建程序](http://yeoman.io/generators/)
```
mkdir   /Users/yuanwei/yomanTest
cd /Users/yuanwei/yomanTest
yo
```

```
? 'Allo yuanwei! What would you like to do?
  Run a generator
  Karma
  Webapp
  ──────────────
  Update your generators
❯ Install a generator
  Find some help
  Get me out of here!
  ──────────────
```

用上下箭头选中 Install a generator.安装完成后，生成的目录结构如下：

![enter image description here](http://7xoc8b.com1.z0.glb.clouddn.com/yomanmulu.png)

####目录结构
app
 > 源文件的存放目录,未压缩的js,css,scss等

bower_components
 > 使用bower安装的第三方组件的源代码，jquery,bootstrap,angular等

 node_modules
 > grunt 需要用到的工具模块安装目录
 
 test
 > js 单元测试存放目录

.bowerrc
> bower的配置目录，配置了使用bower安装的第3方组件存放目录

.editorconfig
> 方便开发者在不同的编辑器和IDE之间定义和维护一致的代码风格的配置，[详细查看](http://editorconfig.org/)

.gitattributes
> git属性设置，详细查看[git文档](http://git-scm.com/doc)

.gitignore
> git提交时忽略的目录，如bower_components和node_modules等目录并不需要提交给git

.jshintrc
> jshint的配置文件，[详细查看](http://jshint.com/)

.yo-rc.json
> yo的配置文件

.bower.json
> bower的配置文件，根据配置安装第三方组件到bower_components文件夹下

.Gruntfile.json
> grunt的任务配置，根据任务配置自动化处理js,css等

.package.json
> grunt的配置文件，根据配置安装grunt的依赖组件到node_modules文件夹下

####开发项目
在实际开发时，在app文件下进行开发，Grunt已经配置了开发任务

![enter image description here](http://7xoc8b.com1.z0.glb.clouddn.com/grunttrack.png)

执行（或者直接双击serve）

     grunt serve

执行完命令后，创建tmp文件夹，存放经过编译后的scss文件，通过g[runt-contrib-connect](https://github.com/gruntjs/grunt-contrib-connect)模拟服务器打开app/index.html，并且通过[grunt-contrib-watch](https://github.com/gruntjs/grunt-contrib-watch)监听指定文件(html,css,js等)，如果文件内容改变，则通过[grunt-contrib-connect](https://github.com/gruntjs/grunt-contrib-connect)内的[livereload](https://github.com/gruntjs/grunt-contrib-watch#optionslivereload)功能通知浏览器，实现浏览器自动刷新

livereload功能需要配置浏览器上的扩展  [LiveReload](https://chrome.google.com/webstore/detail/livereload/jnihajbhpnppcggbcgedagnkighmdlei)

![enter image description here](http://7xoc8b.com1.z0.glb.clouddn.com/Chrome-liveReload.png)

安装完毕后，在浏览器右上方开启liveReload
开启图案![enter image description here](http://7xoc8b.com1.z0.glb.clouddn.com/LiveReloadIcon.png)
这样LiveReload功能才能正常使用

####开发完毕

开发完毕时，需要对开发的内容进行检测，测试，压缩合并等

    grunt default

经过压缩合并等的内容都在dist目录下

####推荐插件

[ng-inspector for AngularJS](https://chrome.google.com/webstore/detail/ng-inspector-for-angularj/aadgmnobpdmgmigaicncghmmoeflnamj) 
方便查看页面AngularJS范围层次结构 ，以及它的控制器或指令与范围。

![enter image description here](http://7xoc8b.com1.z0.glb.clouddn.com/ng-inspector%20for%20AngularJS.png)


[AngularJS Batarang](https://chrome.google.com/webstore/detail/angularjs-batarang/ighdmehidhipcmcojjgiloacoafjmpfk)
方便调试AngularJS
![enter image description here](http://7xoc8b.com1.z0.glb.clouddn.com/AngularJS%20Batarang.png)