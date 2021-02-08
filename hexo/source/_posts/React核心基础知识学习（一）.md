---
title: React核心基础知识学习（一）
date: 2020-04-26 17:34:22
tags: React
categories: React
---

{% asset_img React.png react学习思维导图一 %}

React是现在很流行的前端框架，之前在项目中使用过几次，但没有系统的学习过；现在的前端框架很多，不仅要会使用，也需要多深入学习，了解其背后的奥秘；首先我先梳理react核心的基础知识，先打好基础。
<!-- more -->
#### 1.React中的元素渲染和条件渲染
react中对于reactDom元素的描述，多采用jsx语法，jsx是一种javascript的扩展语法，其并不表示html标签，jsx可以嵌入一个表达式其本身也是一个表达式；在React中渲染元素使用ReactDom.render()方法。
在实际项目中会有不同情况下渲染不同的dom元素，这时就需要使用条件渲染；有三种方法可以实现条件渲染：元素变量、与运算符&&和三木运算符

#### 2.React中的组件
React中实现组件可以有两种方式：
- 函数组件
- class组件，继承React.Component

组件名首字母必须为大写，小写的情况下React会默认为html标签元素；
组件实现为单一功能原则，一个组件原则上只负责一个功能，减少页面耦合性，提高代码复用
##### class组件中的事件处理注意项
- 事件处理声明为class中的方法 function
- 注意this的绑定 

#### 3.React中的state和props
##### state
- 组件内私有的，完全受控于当前组件
- 不能直接修改state，使用setState()
- state表示随时间产生变化的数据，仅在实现交互时使用

##### props
- 只读性，不能修改
- 类似于函数的传参参数，子组件从父组件读取数据

#### 4.React常用生命周期
- componentDidMount() 会在组件挂载后（插入 DOM 树中）立即调用。
- componentDidUpdate() 会在更新后会被立即调用。首次渲染不会执行此方法。
- componentWillUnmount() 会在组件卸载及销毁之前直接调用。在此方法中执行必要的清理操作，例如，清除 timer，取消网络请求或清除在 componentDidMount() 中创建的订阅等。