---
title: JavaScript 经典实例 —— 读书笔记
date: 2019-01-24 10:41:21
tags:
---
### 一、Javascript对象、基本类型和字面值之间的关系
　　字面值：表示某种特定类型的一个值
　　基本类型：字符串，数值，布尔值，null，undefined（5个）
　　对象：使用new操作生成的javascript对象实例
<!--more-->

	var str1 = "string" // "string"引号内容为字面值
	var str2 = String("string") // 生成基本类型
	var str3 = new String("string") // 生成对象实例
	str1 === "string" // true
	str2 === "string" // true
	str3 == "string" // true
	str3 === "string" // false 
	// 基本类型严格等于字面值，对象实例则不会严格等于字面值

　　从字符串提取一个列表：使用方法——indexOf/substr/split/substring/trim
	
	indexOf(str, start) // 返回字符串中从start开始第一个str的位置索引，start可不传，默认从头开始
	substring(start, end) //返回字符串中从start开始到end结束的字符串提取
	substr(start, end-start) //返回字符串中从start开始到end结束的字符串提取,第二参数传的为提取字符串长度
	split(val) //返回一个以val分隔开的数组
	trim() //去掉字符串中的空格 