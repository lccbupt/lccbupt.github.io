---
title: promise对象
date: 2018-11-27 18:57:46
tags: 
	- Javascript 
	- Es6
---
## 1. promise 定义
> 　　promise是异步编程的一种解决方案，js传统的异步编程通常是通过“回调函数和事件”，ES6中将promise写进标准，提供原生的promise对象。
　　所谓Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上说，Promise 是一个对象，从它可以获取异步操作的消息。Promise 提供统一的 API，各种异步操作都可以用同样的方法进行处理。
<!--more-->
## 2.promise基本用法
### （1）生成promise实例
　　promise对象是一个构造函数，用来生成promise实例，因此可以使用new关键字来构造实例。

		new promise(function(resolve, reject){
			// code
			if (/* 异步操作成功 */){
			    resolve(value);
			} else {
			    reject(error);
			}
		})

### （2）promise的then方法[Promise.prototype.then()]
　　Promise 实例具有then方法，也就是说，then方法是定义在原型对象。promise的then方法可以指定resolved状态和rejected状态的回调函数。then方法可以接受两个回调函数作为参数。第一个回调函数是promise对象的状态变为resolved时调用，第二个回调函数是promise对象的状态变为rejected时调用。其中第二个参数可选，不一定需要提供。两个函数都接收promise对象传出的值作为参数。

		promise.then(function(value) {
		  // success
		}, function(error) {
		  // failure
		});

### （3）promise的catch方法[Promise.prototype.catch()]
　　pomise的catch方法也是定义在原型对象上。指定发生错误时的回调方法。
	
		getJSON('/posts.json').then(function(posts) {
		  // ...
		}).catch(function(error) {
		  // 处理 getJSON 和 前一个回调函数运行时发生的错误
		  console.log('发生错误！', error);
		});