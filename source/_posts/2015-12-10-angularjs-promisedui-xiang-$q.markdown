---
layout: post
title: "AngularJS Promise对象($q)"
date: 2015-12-10 11:38:10 +0800
comments: true
categories: 
---

#### promise

promise是一种编程模式，以同步操作的流程形式来操作异步事件，避免了层层嵌套，可以链式操作异步事件。
在编写javascript异步代码时，callback是最最简单的机制，可是用这种机制控制流程、异常处理等比较麻烦，影响到代码的可读性甚至是代码质量，而promise解决了这个问题。

#### $q
在javaScript中有个广泛流行的库[Q](https://github.com/kriskowal/q)，angular中的$q就是从它引入的.
$q是做为angularjs的一个内置服务而存在的，只是对promise异步编程模式的一个简化实现版.
#####defer
defer对象(延迟对象)可以通$q.defer( )获取，下面是defer对象的api：

 >   1. resolve(value):向promise对象发送消息成功完成任务，value即为发送的消息。
 >   2. reject(reason): 向promise对象发送消息已经不可能完成这个任务了(拒绝)，reason即为发送的消息（拒绝原因）。
 >   3. notify(value): 向promise对象发送消息现在任务完成的情况（通知），value即为发送的消息。
 >   4. all( ) 传入Promise的数组，批量执行，返回一个promise对象
 >   5. when( ) 传入一个不确定的参数，如果符合Promise标准，就返回一个promise对象
    
关于状态有几个规定：
 >   1. 状态的变更是不可逆的
 >   2. 等待状态可以变成完成或者拒绝
  
#####promise 对象的方法
promise就是这个defer对象的承诺对象，defer主要是用来发送消息的。promise对象可以通过defer.promise获取,消息发送完promise会调用现有的回调函数.

    function getUserOrderList(){
      var model = $q.defer();
        Users.userOrderList.then(function(success){
        model.resolve(success);
      },function(error){
        model.reject(error);
      });
      return model.promise;
    }
    

then(successCallback,errorCallback,notifyCallback):参数为不同消息下的不同回调函数，defer发送不同的消息执行不同的回调函数，消息作为这些回调函数的参数传递。返回值为回一个promise对象,为支持链式调用而存在。就是一种对执行结果不确定的一种预先定义，如果成功，就执行successCallback；如果失败，就执行errorCallback，就像事先给出了一些承诺。

all( )方法，可以把多个primise的数组合并成一个。当所有的promise执行成功后，会执行后面的回调。回调中的参数，是每个promise执行的结果。当批量的执行某些任务时，就可以使用这个方法

     var downLoadLyric = function(){
         console.log("开始下载歌词");
         return "下载歌词";
    };
     var downLoadSong = function(){
         console.log("开始下载伴奏");
         return "下载伴奏";
    };
    $q.all([downLoadLyric(),downLoadSong()])
          .then(function(result){
              console.log(result);
        });

执行的结果：

    开始下载歌词
    开始下载伴奏
    Array [ "下载歌词", "下载伴奏" ] 
    

when( )可以传入一个参数，参数可以是一个值，也可以是一个符合promise标准的外部对象。当传入的参数不确定时，可以使用这个方法

     var funcA = function(){
            console.log("funcA");
            return "hello,funcA";
         };
     $q.when(funcA())
          .then(function(result){
            console.log(result);
        });
   
   执行的结果：
   
    hello,funcA

     
    



     
    


