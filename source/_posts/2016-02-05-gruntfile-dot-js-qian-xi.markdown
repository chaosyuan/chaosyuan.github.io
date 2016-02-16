---
layout: post
title: "Grunt 浅析"
date: 2016-02-05 17:57:36 +0800
comments: true
categories: 
---


####Grunt是什么
Grunt是一个基于JavaScript上的一个很强大的任务管理器(task runner)，就是用JavaScript去执行一些程序来完成一些任务。比如说验证HTML、CSS、JavaScript的语法，压缩CSS、JavaScript、图像，[Sass](http://www.w3cplus.com/sassguide/)、[LESS](http://less.bootcss.com/)和[Stylus](http://stylus-lang.com/)编译成CSS等等，这些东西很适合用Grunt来解决。

Grunt其实是一个基于NodeJS,可用于自动化构建、测试、生成文档的项目管理工具。可以在开发中提高效率、减少错误率。

Grunt不仅仅是构建工具，它还是任务运行器，管理每个子任务的自动化运行，它还可以做更多东西

####为什么要用Grunt

刚开始要用的时候很疑惑，为什么要用Grunt？在开发ios时候，相应的环境和配置文件配置好之后，直接Archive。很直接方便。其实前端这些都是为了自动化。对于前端项目，代码维护、打包、发布等流程变得极为繁琐，同时浪费的时间和精力也越来越多，当然人为的错误也随着流程的增加而增加了更多的出错率。为了明确模块分工，我们可以会将JavaScript、CSS代码拆解成很多个模块，它们都有独立的一个个文件，但这样一来用来页面上，增加了文件个数，同时也产生过多的Http请求，从性能的角度来考虑，不利于页面优化。所以需要合并这些JavaScript和CSS文件。尽可能的将项目中的文件进行压缩.这样一来， 都希望有一种工具能代替手工操作，能帮助整个团队在开发中能精简流程、提高效率、减少错误率。

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

#####package.json
package.json是一个JSON文件，创建这个文件的方式有很多种。比如：

1. 根据 [grunt-init](http://www.gruntjs.net/project-scaffolding) 模版自动创建特定于项目的package.json文件
2. 从[官网](http://gruntjs.com/)上复制或者下载一个.
3. [npm init](https://docs.npmjs.com/init)命令创建一个基本的package.json文件

最简单的package.json文件模板：

```
{
  "name": "my-project-name",
  "version": "0.1.0",
  "devDependencies": {
    //项目依赖插件
    "grunt": "~0.4.5",
    "grunt-contrib-clean": "^0.6.0",
    "grunt-contrib-compass": "^1.0.0",
    "grunt-contrib-concat": "^0.5.0",
    "grunt-contrib-connect": "^0.9.0",
    "grunt-contrib-copy": "^0.7.0",
  }
}
```

向package.json文件中添加Grunt插件最简单方式是通过命令

```
sudo npm install <module> --save-dev
```

这条命令不仅安装了<module>，还会自动将其添加到[devDependencies](https://docs.npmjs.com/files/package.json#devdependencies) 配置段中，遵循[tilde version range](https://docs.npmjs.com/misc/semver#Ranges)格式。
例如：

```
sudo npm install grunt-google-cdn --save-dev
```

#####Gruntfile.js
在项目根目录下使用[grunt-init](https://github.com/gruntjs/grunt-init-gruntfile)或者直接创建一个Gruntfile.js文件。

Gruntfile.js文件模板：

```
module.exports = function(grunt) {

  // Project configuration.
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
        uglify: {
        //uglify任务的配置信息
            options: {
                beautify: false,
                mangle: true //混淆变量名
            },
            dist: {
                files: [{}]
            }
        },
  });

  // 加载包含 "uglify" 任务的插件。
  grunt.loadNpmTasks('grunt-contrib-uglify');

  // 默认被执行的任务列表。
  grunt.registerTask('default', ['uglify']);
};
```

配置完成后

```
sudo  npm install
```

具体配置任务请参考[官网](http://www.gruntjs.net/configuring-tasks)

参考文献：

1. http://www.gruntjs.net
2. https://docs.npmjs.com/files/package.json




