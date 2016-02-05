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

---

####ngRoute  VS ui-router

ngRoute 和 ui-router
#####相同点

-  两者都可以实现路由功能

#####不同点

1. ngroute是用AngularJS框架的核心部分。
2. ui-router是一个社区库，它是用来提高完善ngroute路由功能的

[ui-router](https://github.com/angular-ui/ui-router)路由器属于一个第三方模块，功能非常强大。更轻松配置项目的一个模块库，是ngRouter的升级版！它支持一切正常ngroute也可以做许多额外的功能。所以实际运用中，常选择ui-router作为路由器，而不用ngRoute。

---

#### ui-Router
安装方式:
```
bower install angular-ui-router --save
```
将源文件包含到页面中:

```
<script src="bower_components/angular-ui-router/release/angular-ui-router.js"></script>
```

将ui.router作为web应用的依赖，注入到主程序
```
(function(){
     'use strict';
      angular.module('jcfApp', ['ui.router']);
})();
```
UI-Router可以将视图嵌套，因为它基于的是操作状态(.state)而仅非URL。当在ui-router中处理路由和状态时，开发者的重心是当前的状态是什么以及在哪一个页面里.

```
<div  ui-view="main-content"></div>
```

为特定状态指定的模板将会放在ui-view元素中。,这就是在同一个路由下实现嵌套视图的方法。要定义一个路由，与ngRoute的方法相同：使用.config方式，但使用的不是$routeProvider而是$stateProvider

```
(function(){
    'use strict';
    angular.module('app.account')
        .config(routerConfigure);
    routerConfigure.$inject = ['$stateProvider', '$urlRouterProvider'];
    function routerConfigure($stateProvider, $urlRouterProvider) {
        $urlRouterProvider.otherwise("/");
        $stateProvider
            .state("account", {
                url: "/account/id&from",
                views: {
                    "main-content": {
                        templateUrl: 'modules/account/account.tpl.html',
                        controller: "AccountController",
                        controllerAs: "vm"
                    }
                }
            })
    };
})();
```

设置对象上定义了一个叫account的状态。设置对象stateConfig和路由设置对象的选项是差不多的。url选项将会为应用的状态指定一个URL基于用户浏览该应用所在的状态。这样当在浏览该应用的时候便能实现深度链接的效果。URL可以有多个参数。参数可以使用$stateParams服务取到。

每个视图下可以设置模板 - template - HTML字符串（模板），或者是返回HTML字符串的函数 - templateUrl - HTML模板的路径（如上图代码），或者是返回HTML模板路径的函数 - templateProvider - 返回HTML字符串的函数。需要注意的是如果设置了视图选项views,该状态下的'template','templateUrl'
及’templateProvider‘，将被忽略

#####控制器
指定控制器，或者在路由里面创建一个作为控制器的函数。但如果没有定义模板，控制器将无效

#####when()
该函数接受两个参数：1.当前的路径，2.需要重定向到的路径（或者是需要在路径被访问是运行的函数）。设置重定向前需要为$urlRouterProvider设置when函数来接受一个字符串。例如：当重定向一个400路由到/error

```
$urlRouterProvider.when('400', '/error');
```

#####Resolve
预载入功能，可以预先载入依赖或者数据，然后注入到控制器中。在ngRoute中resolve选项可以允许在路由到达前载入数据保证（promises）。

预载入选项需要一个对象，这个对象的key即要注入到控制器的依赖，value为需要被载入的factory服务。如果传入的时字符串，angular-route会试图匹配已经注册的服务。如果传入的是函数，该函数将会被注入，并且该函数返回的值便是控制器的依赖之一。如果该函数返回一个数据保证（promise），这个数据保证将在控制器被实例化前被预先载入并且数据会被注入到控制器中。

```
  .state('banner-Detail', {
    url: "/bannerDetail",
    views: {
      "main-content": {
        templateUrl: "modules/front_end_config/app_ad/tpl/banner_detail.html",
        controller: "bannerDetailCtr"
      }
    },
    resolve: {
      desp: ['$ocLazyLoad',
        function ($ocLazyLoad) {
          return $ocLazyLoad.load('modules/front_end_config/app_ad/tpl/banner_detail.controller.js')
        }]
    }
  })
```
#####otherwise()
当用户访问的路径没有被定义的时候它将重定向到指定的页面。这是创建’默认‘路径的好方法。 otherwise()只接受一个参数，函数或者字符串，字符串必须为合法的url路由地址，函数则会在没有任何路径被匹配的时候被调用.

```
  $urlRouterProvider.otherwise('/');
```

```
$urlRouterProvider.otherwise(
    function($injector, $location) {
      $location.path('/');
    });
```



