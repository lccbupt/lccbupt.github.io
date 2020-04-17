---
title: 了解一下jQuery.ajaxPrefilter()
date: 2018-11-20 15:58:39
tags: 
	- Javascript
	- jQuery
categories: Javascript
---
#### 1. jQuery.ajaxPrefilter([dataTypes],handler)
> jquery的官网文档给出的api方法描述：
> Description: Handle custom Ajax options or modify existing options before each request is sent and before they are processed by $.ajax().
<!--more-->
ajaxPrefilter()方法主要是在每次ajax发送send请求前处理请求的选项或者修改存在的一些设置。简单来讲就是调用send之前对HTTP头等进行修改和进一步处理。

> dataTypes 类型: String
一个可选的字符串，其中包含一个或多个空格分隔的数据类型
> handler(options, originalOptions, jqXHR) 类型: Function()
一个处理程序程序，用于设置未来的Ajax请求的默认值。

#### 2.ajaxPrefilter 方法的作用
> 参考：https://www.css88.com/jqapi-1.9/jQuery.ajaxPrefilter/

通过ajaxPrefilter方法可以取消重复发送的ajax请求,如下官网给的例子：

>当自定义选项，需要提前处理，预过滤器（Prefilters）是一个完美的选择。给定下面的代码， 例如，如果自定义abortOnRetry选项被设置为true，那么调用$.ajax()会自动中止请求相同的URL：

		var currentRequests = {};
		$.ajaxPrefilter(function( options, originalOptions, jqXHR ) {
		  if ( options.abortOnRetry ) {
		    if ( currentRequests[ options.url ] ) {
		      currentRequests[ options.url ].abort();
		    }
		    currentRequests[ options.url ] = jqXHR;
		  }
		});

通过ajaxPrefilter 还可以修改已经存在的选项，如下面例子：
		
		$.ajaxPrefilter( function( options ) {
		  if ( options.crossDomain ) {
		    options.url = "http://mydomain.net/proxy/" + encodeURIComponent( options.url );
		    options.crossDomain = false;
		  }
		});

通过ajaxPrefilter方法可以根据提供的dataTypes参数，指定处理特定的请求有效

	//以下只针对 json和srcript 请求有效

	$.ajaxPrefilter( "json script", function( options, originalOptions, jqXHR ) {
	  // Modify options, control originalOptions, store jqXHR, etc
	});

通过ajaxPrefilter 可以将请求重定向到另一个类型，并且返回该数据类型。

	//如下，如果url中函数isActuallyScript函数中设定的指定属性，就设置成script的请求

		$.ajaxPrefilter(function( options ) {
		  if ( isActuallyScript( options.url ) ) {
		    return "script";
		  }
		});
