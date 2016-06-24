---
layout: post
title: "HTTP缓存策略"
date: 2016-03-27 19:00:22 +0800
comments: true
categories: 
---

#### 写在前面

最近在开发公司[web](https://www.jucaifu.com/)时，对HTTP缓存不是很了解，之后仔细看了HTTP缓存策略.记录下来。
浏览器一般缓存图片、CSS、JS等静态文件，因为这些文件的更新频率相对来说比较低，合理利用浏览器的缓存可以优化网站性能，提升体验。

#####HTTP协议(Header)
HTTP缓存主要由HTTP协议的头（Header）信息来制定，缓存分为两部分，分别是本地缓存和缓存协商。在介绍缓存之前，先说一下服务端Header 取值
>Expires：后面跟一个绝对时间字符串，表示过期时间.比如：Mon, 22 Jun 2026 01:05:00 GMT。


>Cache-Control：除了提供了同Expires相同并更精确的缓存功能，还提供了验证机制，它可以取以下这些值：
>max-age：功能和Expires类似，但是后面跟一个以“秒”为单位的相对时间，来供浏览器计算过期时间。
>no-cache：不使用本地缓存。需要使用缓存协商，先与服务器确认返回的响应是否被更改，如果之前的响应中存在ETag，那么请求的时候会与服务端验证，如果资源未被更改，则可以避免重新下载。
>no-store：直接禁止浏览器缓存数据，每次用户请求该资源，都会向服务器发送一个请求，每次都会下载完整的资源。
>private：表示只有用户客户端可以缓存，而 CDN 等不可。
>public：表示用户客户端和 CDN 都可以缓存当前资源。

需要注意：如果同时出现Cache-Control和Expires，Cache-Control的优先级要高于Expires

```
 Cache-Control: max-age=315360000
 Expires: Tue, 16 Jun 2026 07:30:16 GMT
```




#### 本地缓存

本地缓存是指当浏览器请求资源时，如果命中了浏览器本地的缓存资源，那么浏览器就不会发送真正请求给服务器。它的执行过程如下：
  1. 浏览器第一次发送请求给服务器时，此时浏览器还没有本地缓存副本，服务器返回资源给浏览器，响应码是200 OK，浏览器收到资源后，把资源和对应的响应头一起缓存下来
  2. 浏览器第二次准备发送请求给服务器时候，浏览器会先检查上一次服务端返回的响应头信息中的Cache-Control，如果下次请求时间没有超过缓存过期时间，浏览器会直接命中本地缓存，而不会向浏览器发送请求。
  3. 如果没有命中，浏览器就会把请求发送给服务器，进入缓存协商阶段
  
![enter image description here](http://7xoc8b.com1.z0.glb.clouddn.com/bendihuancun.png)

#### 协商缓存
当第一次请求时服务器返回的响应头中没有Cache-Control和Expires或者Cache-Control和Expires过期或者它的属性设置为no-cache时，那么浏览器第二次请求时就会与服务器进行协商，询问浏览器中的缓存资源是不是旧版本，需不需要更新，此时，服务器就会做出判断，如果缓存和服务端资源的最新版本是一致的，那么就无需再次下载该资源，服务端直接返回304 Not Modified 状态码，如果服务器发现浏览器中的缓存已经是旧版本了，那么服务器就会把最新资源的完整内容返回给浏览器，状态码就是200 Ok。

服务器通过2种方式来判断浏览器缓存是否是最新的
#####**1.Last-Modified与If-Modified-Since**
当浏览器第一次请求资源时，服务器会把资源的最新修改时间**Last-Modified: Mon, 04 March 2016 09:13:50 GMT**放在响应头中返回给浏览器，第二次请求时，浏览器就会把上一次服务器返回的修改时间放在请求头**If-Modified-Since:Last-Modified: Mon, 04 March 2016 09:13:50 GMT**发送给服务器，服务器就会拿这个时间跟服务器上的资源的最新修改时间进行对比，如果两者相等或者大于服务器上的最新修改时间，那么表示浏览器的缓存是有效的，此时缓存会命中，服务器就不再返回内容给浏览器了，同时Last-Modified头也不会返回，因为资源没有被修改，返回了也没什么意义。如果没命中缓存则最新修改的资源连同Last-Modified头一起返回。

这种方式是通过头信息资源的修改时间来判断资源有没有更新。当然这种方式有一定的弊端。比如：
1. Last-Modified的日期只能精确到秒，如果在1s内做了修改，那么就会出现错误判断
2. 如果一些资源定期生成，这种情况下内容没有变化但是服务器的 Last-Modified改变了，导致使用不了缓存

另一种方式就是根据资源的内容来判断，就是ETag与If-None-Match

#####**2.ETag/If-None-Match**

首先说一下Etag，Etag是资源在服务器的唯一标识符，能够更加准确的控制缓存。其实这种方式和Last-Modified与If-Modified-Since流程是相似的。唯一的区别是它基于资源的内容的摘要信息（比如MD5 hash）来判断。浏览器发送第二次请求时，会把第一次的响应头信息ETag的值放在If-None-Match的请求头中发送到服务器，与最新的资源的摘要信息进行对比，如果相等，取浏览器缓存，否则内容有更新，最新的资源连同最新的摘要信息返回。用ETag的好处是如果因为某种原因到时资源的修改时间没改变，那么用ETag就能区分资源是不是有被更新。

Last-Modified 与 ETag 是可以一起使用的，当两者同时存在的时候,通过以下表达式来进行缓存策略的判断

```
if ETagFromServer != ETagOnClient || LastModifiedFromServer != LastModifiedOnClient
   GetFromServer
else
   GetFromCache
```
参考： [StackOverflow-What takes precedence: the ETag or Last-Modified HTTP header?](http://stackoverflow.com/questions/824152/what-takes-precedence-the-etag-or-last-modified-http-header)

-----------------------------------
**Request**

| Cache-Control: max-age=31536    | 以秒为单位           | 
| ------------- |:----------:|
| If-Modified-Since:"Mon, 04 March 2016 10:38:50 GMT"     | 缓存文件的最后修改时间 |
| If-None-Match:"0693f78a437cc1:0"   |  缓存文件的ETag 值  |  
| Cache-Control:no-cache | 不使用缓存      | 


**Response**

| Cache-Control: max-age=60    | 60秒过后缓存过期（相对时间）           | 
| ------------- |:----------:|
| Data:"Mon, 04 March 2016 10:39:00 GMT"     | 当前Response发送的时间 |
| Expires:"Mon, 04 March 2016 10:40:00 GMT"   |  缓存过期的时间（绝对时间）  |  
| Last-Modified:"Mon, 04 March 2016 10:38:50 GMT" | 服务器文件的最后修改时间      | 
|ETag:"20d3abb7ec1cd1:0" | 服务器文件的ETag值     | 


使用Ctrl+F5(Com+R) 强制刷新页面，可以忽略以上讲的两种缓存策略

| 操作      | Cache-Control          | Last-Modified/Etag  |
| ------------- |:-------------:| :-----:|
| 前进后退      | 有效 | 有效 |
| 刷新-F5     | 无效      | 有效 |
| 强制刷新 | 无效     | 无效   |

