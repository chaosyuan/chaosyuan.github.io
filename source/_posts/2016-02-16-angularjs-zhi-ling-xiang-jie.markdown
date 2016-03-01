---
layout: post
title: "AngularJS 指令详解"
date: 2016-02-16 17:20:32 +0800
comments: true
categories: 
---

####前言
最近在公司的项目中用到AngularJs中的指令。所谓的指令在我理解看来。就是把浏览器能够识别的标签使用语法糖包装起来。在其它地方直接调用。这样极大方便了项目的开发和维护。在[聚财富](https://www.jucaifu.com/)项目中对通用的组件进行抽离，封装成自定义指令。整个工程都可以复用。在项目工程中创建语义化以及高度重用的组件，对于提高代码质量有很大的帮助，也方便扩展及后期维护。目前市场上的前端框架也只有AngularJS 拥有自定义指令的功能，并且AngularJS 是目前唯一提供Web应用可复用能力的框架。

####指令概述

指令是DOM元素上的标记，使元素拥有特定的行为，也是用来扩展浏览器能力的技术之一。在DOM编译期间，和HTML元素关联着的指令会被检测到，并且被执行。这使得指令可以为DOM指定行为，或者改变它。AngularJS有一套完整的、可扩展的、用来帮助web应用开发的指令集（例如ng-repeat.ng-view.ng-controller等）。

####执行流程

下面说一下指令执行过程。
1. 浏览器得到 HTML 字符串内容，解析得成 DOM 结构
2. ng 引入，把 DOM 结构扔给 $compile 函数处理。

> 1. 找出 DOM 结构中有变量占位符
> 2. 匹配找出 DOM 中包含的所有指令引用
> 3. 把指令关联到 DOM节点上
> 4. DOM 中的多个指令按优先级排列
> 5. 执行指令中的 compile 函数（改变 DOM 结构，返回 link 函数）

3. 执行 link 函数（连接模板的 $scope）
注意：$compile和compile，前者是ng内置服务，后者是指令中的编译函数。使用时注意区分。

####表现形式
指令的四种使用方式：

1. 作为元素(E) :`<smsCode></smsCode>`
2. 作为属性(A) :`<div smsCode="sms"></div>`
3. 作为类名(C) :`<div class="smsCode:sms;"></div>`
4. 作为注释(M) :`<!-- directive: smsCode sms -->`

在实际项目中常用的就是作为元素和属性。

####配置参数

下面是一个标准指令的示例，可配置的参数包括以下部分

```
(function(){
  'use strict';  
angular.module('securityLoginToolbar')
    .directive('loginToolbar', function factory(injectables) {

    var directiveDefinitionObject = {

        restrict: string,//指令的使用方式，包括元素，属性，类，注释

        priority: number,//指令执行的优先级

        template: string,//指令使用的模板，用HTML字符串的形式表示

        templateUrl: string,//从指定的url地址加载模板

        replace: bool,//是否用模板替换当前元素，若为false，则append在当前元素上

        transclude: bool,//是否将当前元素的内容转移到模板中

        scope: bool or object,//指定指令的作用域

        controller: function controllerConstructor($scope, $element, $attrs, $transclude){...},//定义与其他指令进行交互的接口函数

        require: string,//指定需要依赖的其他指令

        link: function postLink(scope, iElement, iAttrs) {...},//以编程的方式操作DOM，包括添加监听器等

        compile: function compile(tElement, tAttrs, transclude){

            return: {

                pre: function preLink(scope, iElement, iAttrs, controller){...},
                post: function postLink(scope, iElement, iAttrs, controller){...}
            }
        }//编程的方式修改DOM模板的副本，可以返回链接函数
    };
    return directiveDefinitionObject;
});
})();
```
loginToolbar 指令的名字。需要注意的是：在自定义指令命名时尽量不要以ng开头，防止与系统的指令冲突。


factory函数，叫工厂函数，它是用来创建指令的。它只会被调用一次(类似于iOS的单例模式)：当编译器第一次匹配到相应指令的时候，在其中进行初始化的工作。调用它时使用的是 $inject， 它遵循所有依赖注入(DI)规则.

*  优先级priority - 当一个DOM上有多个指令时，需要指定指令执行的顺序。 优先级就是用来在执行指令的compile函数前，先排序的。高优先级的先执行

*  restrict - EACM中的任意一个字母。它是用来限制指令的声明格式的。如果没有这一项。那就只允许使用属性形式的指令

*  模板template - 将当前的元素替换掉。 替换过程会自动将元素的属性和css类名添加到新元素上

*  模板地址templateUrl - 和template属性一样，这里指示的是一个模板的URL。因为模板加载是异步的，所有编译和链接都会等到加载完成后再执行

*  替换replace - 如果设置成true，那么页面上指令内部里面的内容会被模板替换。比如:<hello><div>这是指令内部的内容</div></hello>，hello指令内部的div内容将会被模板替换掉

*  transclude -  如果不想让指令内部的内容被模板替换，设置为true。

#####编译函数 Compile

```
   compile: function compile(tElement, tAttrs, transclude){}
```

一般需要修改模板DOM的时候，需要用到编译函数。它接受3个参数
1. tElement （template element ）指令所在的元素。对这个元素及其子元素进行变形之类的操作是安全的.
2. tAttrs （template attributes ) 元素上所有指令声明的属性，这些属性都是在编译函数里共享的.
3. transclude - 一个嵌入的链接函数 function(scope, cloneLinkingFn)

> 需要注意的是在编译函数里面不要进行任何DOM变形之外的操作。 DOM监听事件的注册应该在Linking函数中做，而不是Compile函数中

#####链接函数  Link

```
function link(scope, iElement, iAttrs, controller) { ... }
```

link函数主要用来为DOM元素添加事件监听、监视模型属性变化、以及更新DOM。在模板被克隆之后执行的，也是绝大部分指令逻辑代码编写的地方

*  scope - 指令需要监听的作用域

*  iElement （instance element） 指令所在的元素。只有在 postLink 函数中对元素的子元素进行操作才是安全的.

*  iAttrs (instance attributes) 实例属性，所有声明在当前元素上的属性列表，属性在所有链接函数间是共享的.

*  controller - 控制器实例


####总结
最后,希望对大家理解AngularJS能有一些帮助。详细请参考[官网](https://angularjs.org/)。如果发现文章中哪里有不对的,请及时给我发评论.谢谢!