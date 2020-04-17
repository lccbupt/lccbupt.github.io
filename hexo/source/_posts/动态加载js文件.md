---
title: 动态加载js文件
date: 2018-11-15 15:33:07
tags: Javascript
categories: Javascript
---
> 在某些复杂的项目中我们需要适配多个平台或者渠道，因此需要引入不同平台的js通用文件。所以我们需要根据不同的平台环境判断引入不同的js文件，此时就需要我们掌握动态加载js文件的常用方法以及遇到的常见问题。这里总结几种常用方法和各自的优缺点。
<!--more-->
#### 1.动态加载js文件方法一：jquery的append，appendTo，prepend，prependTo方法
	这四个方法是jquery提供的向某个元素内插入指定内容，当然指定的内容可以包括script标签
	插入的指定内容是不收限制，虽然这个方法可以向html页面中添加js文件但却会产生问题
	比如：
		$('head').prepend('<script src="xxx.js"></script>')
		window.xxx.init() 
		//xxx方法是xxx.js中定义的全局方法
		//此时就会报错，提示xxx是undefined

	因为prepend的方法无法保证js文件的加载速度，在调用init方法的时候js文件还没有完全加载成功
	因此就会报错，所以不建议使用该方法去动态引入js文件。

#### 2.动态加载js文件方法之二：document.createElement
	document.createElement(nodename) 是js原生方法，指定名称创建一个元素

		var myScript= document.createElement("script");
        myScript.type = "text/javascript";
        myScript.src="xxx.js";
        document.body.appendChild(myScript);
        //此处若立即执行xxx.js中的方法也会报错，因为没有加载完，这种方法脚本也是异步加载

#### 3.动态加载js文件方法之三：document.write
	document.write是直接写入某一元素，也是异步加载的顺序，因此如果立即执行脚本也会出现问题

		document.write('<script src="xxx.js"></script>')//加载js脚本，异步
		window.xxx.init() //找不到该方法，脚本异步加载没有完成

#### 4.动态加载js文件解决异步方法：script的onload事件
	上面的3中方法都是异步的，因此动态生成script标签后不能立即执行js文件中的函数，需要监听一下
	script标签加载完成事件，等js文件加载完成后再去调用js中的方法。
	这里就需要使用script的onload事件
		
		var myScript = document.createElement('script')
		myScript.src="xxx.js"
		document.body.appendChild(myScript)
		myScript.onload =function(){
			window.xxx.init() //保证在加载完xxx.js时执行init函数
		}
#### 5.可以使用jquery的$.getScript()方法
>	详细参看：https://api.jquery.com/jquery.getscript/

	$.getScript()方法通过http get方法请求并执行js文件

		jQuery.getScript(url,success(response,status))
		等价于：
		$.ajax({
		  url: url,
		  dataType: "script",
		  success: success
		});

以上是对动态加载js方法的一个小小的总结，在项目开发中注意js的异步加载，在保证动态引入的js加载成功后再调用其中的方法。