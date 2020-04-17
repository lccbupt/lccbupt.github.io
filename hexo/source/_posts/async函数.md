---
title: async函数
date: 2018-11-26 17:59:02
tags: Javascript
---
#### 1.async函数基本用法
> async函数返回一个promise对象；可以使用then方法添加回调函数。当函数执行的时候，遇到await就先返回，等异步操作完成，再接着执行函数体后面的语句。例子如下：

	async function getStockByName(name) {
		const symbol = await getStockSymbol(name);
		const stockPrice = await getStockPrice(symbol)
		return stcokPrice;
	}
	getStockByName('gogle').then(function(result) {
		console.log(result)
	})
----
<!--more-->
#### 2.async函数特点
（1）、返回Promise对象
　async函数内部return返回的值，成为then方法回调的参数
　async函数内部平抛出错误会导致返回的Promise对象变为reject状态。抛出的错误对象会被catch方法回调函数接收到
（2）、搭配await命令
　await命令后面返回一个Promise对象，返回该对象的结果，如果不是promise对象，就直接返回对应的值
（３）、async函数写法直观，相比于promise，generator的写法更简单易懂。
	
	function login(urls) {
		const textPromises = urls.map(url => {
			return fetch(url).then(respose => respose.text());
		})
		textPromise.reduce((chain, textPromise) => {
			return chain.then(() => textPromise)
				.then(text => console.log(text))
		}, Promise.resolve());
	} //  promise写法实现异步操作按顺序读取url，按顺序输出

	//上面代码使用fetch方法，同时远程读取一组 URL。每个fetch操作都返回一个 Promise
	 对象，放入textPromises数组。然后，reduce方法依次处理每个 Promise 
	 对象，然后使用then，将所有 Promise 对象连起来，因此就可以依次输出结果

	async function login(urls) {
		for(const url of urls) {
			const response = await fetch(url);
			console.log(await respose.text());
		}
	}// async 函数实现

	async function login() {
		const textPromises = urls.map(async url => {
			const respose = await fetch(url)
			reurn respose.text();
		})
		for(const textpromise of textPromises) {
			console.log(await texpromise)
		}
	}// 多个await并发发出