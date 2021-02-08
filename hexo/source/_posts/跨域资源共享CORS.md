---
title: 跨域资源共享CORS
date: 2020-05-08 11:47:05
tags: javascript
---
> CORS 一个w3c标准，它允许浏览器向跨域服务器发出XMLHttpRequest请求，从而克服AJAX的同源策略限制。
#### 一、简介
- CORS需要浏览器和服务器同时支持，所有浏览器（ie10+）以上都支持,
- CORS通信过程是浏览器自动完成，通信与AJAX同源通信没有差别,
- CORS通信关键在于服务器，只要服务端实现了CORS接口，就可以跨域通信。
<!-- more -->

#### 二、两种请求
浏览器将CORS请求分为两类；简单请求 和 非简单请求
##### 简单请求
- 请求方法 HEAD,GET,POST
- HTTP头部信息不超出以下字段：
    Accept
    Accept-Language
    Content-Language
    Last-Event-ID
    Content-Type：只限于三个值application/x-www-form-urlencoded、multipart/form-data、text/plain

- 简单请求流程：
    浏览器直接发出CORS请求，在头部信息中增加一个Origin字段
    如果Origin指定的源不在许可范围内，服务器返回正常的HTTP回应，浏览器发现回应的头信息中没有Access-Control-Allow-Origin字段就报错

##### 非简单请求
- 请求方法如PUT\DELETE等，对服务器有特殊要求
- 或者Content-Type的字段是 application/json
- 流程：
    正式通信之前，会增加一次HTTP查询请求，成为“预检”请求
    浏览器先询问服务器，当前页面是否在服务器可续名单内，之后才正式发出XMLHttpRequest

#### 三、与jsonp方式跨域区别
- jsonp的跨域方式只支持Get方法；CORS支持所有类型的http请求

-----------------
> 参考资料： https://www.ruanyifeng.com/blog/2016/04/cors.html