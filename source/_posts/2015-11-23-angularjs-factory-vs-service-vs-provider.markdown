---
layout: post
title: "AngularJS:Factory vs Service vs Provider"
date: 2015-11-23 20:02:38 +0800
comments: true
categories: 
---

最近在写公司后台的管理系统，由于初试AngularJS. 所以很自然地就就把业务逻辑写在 controller 和 scope 中。在git上找到AngularJs编码规范，细读之后，回头看来，代码写的是一塌糊涂。controller中充斥着大量的业务逻辑。controller本身只充当控制器的作用，controller 只在需要的时候才会初始化，一旦不需要就会被抛弃。因此，当每次切换或刷新页面的时候，Angular 会清空当前的 controller。因此controller 里是不能保存持久化数据的。这不属于它的作用范畴。业务逻辑处理应该抽离出来放在Service层，这样代码逻辑更加简洁，后期维护和测试也更加方便.
####AngularJS Service
Angular 提供了3种方法来创建并注册我们自己的 Service.

 >   1.Factory
 >   2.Service
 >   3.Provide


#####1.Factory()
Angular里面创建service最简单的方式是使用factory()方法,用 factory 就是创建一个对象，为它添加属性，然后把这个对象返回出来。你把 service 传进 controller 之后，在 controller 里这个对象里的属性就可以通过 factory 使用了

```javascript
(function(){
  'use strict';
  angular.module('service.productList', [])
    .factory('productListProvider', productListProvider);
  productListProvider.$inject = ['$log','$http'];
  function productListProvider ($log,$http) {

    var service = {
      success: success,
      error: error,
      log: log,
      debug: debug,
    };
    return service;
    function success() {}
    function error() {}
    function log() {}
    function debug() {}
    }
})();
```       
在controller中调用
```javascript
(function () {
  'use strict';
  angular.module('app.product')
    .controller('ProductController', ProductController);
  ProductController.$inject = ['$scope', '$state',  'productListProvider'];

  function ProductController($scope, $state, productListProvider) {
  
    function productList() {
      return productListProvider.success();
    }
    productList();
  }
})();
```
**注意：需要使用.config()来配置service的时候,不能使用factory()方法**

#####2.Service()
service()通过构造函数的方式创建service, 给"this"添加属性，然后 service 返回"this"。 service 传入controller ，在controller里 "this" 上的属性就可以通过 service 来使用了

```javascript

(function () {
  'use strict';
  angular.module('login.services')
    .service('login', login);
  login.$inject = ['$scope', '$state']

  function login($scope, $state) {

    this.userInfo = {};
    this.setUserInfo = function (useName) {

      self.userInfo['name'] = useName
      return userInfo;
    }
  }
})();
```
在controller中调用
```javascript
(function () {
  'use strict';
  angular.module('app.userModule')
    .controller('UserInfoController', UserInfoController);

  UserInfoController.$inject = ['$scope', 'login'];

  function UserInfoController ($scope, login) {
    login.setUserInfo();
  }

})();
```
#####3.Provider()

providers 是唯一一种你可以传进 .config() 函数的 service。当想要在 service 对象启用之前，先进行模块范围的配置，那优先考虑使用provider
```javascript
(function () {
  'use strict';
  angular.module('providerTestModele', [])
    .config(function ($stateProvider, $urlRouterProvider) {
      $urlRouterProvider.otherwise("/");
    })
    .provider('providerTest', ['$window', function ($window) {
      this.test = {
        "firstName": "yw"
      };
      this.$get = function () {
        return this.test;
      };
    }
    ]);
})();
```


####参考
1.https://www.airpair.com/angularjs/posts/top-10-mistakes-angularjs-developers-make
2.https://github.com/johnpapa/angular-styleguide/blob/master/i18n/zh-CN.md
3.http://tylermcginnis.com/angularjs-factory-vs-service-vs-provider/

