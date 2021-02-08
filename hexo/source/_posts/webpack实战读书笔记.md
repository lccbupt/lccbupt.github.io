---
title: webpack实战-读书笔记
date: 2020-05-15 11:51:49
tags:
---

{% asset_img header.png webpack %}

### 一、关于webpack
webpack是前端常用的打包工具，用来组织和管理模块包括html，css，js，图片等资源；其核心就是解决模块之间的依赖，把各个模块按照特定的规则和顺序组织在一起，最终合并成一个js文件或多个js文件，webpack可以理解为一个模块处理工厂。
核心概念：
- 入口（entry）
- 输出（output）
- loader
- 插件（plugins）
<!--more-->

### 二、模块打包
#### 1.不同模块的标准
##### CommonJs:
- 每个文件即一个模块；每个模块拥有各自的作用域；
- 导出：module.exports
- 导入：require（1、require模块是第一次加载，这时首先执行该模块，然后到处内容；2、require模块曾被加载过，这时该代码不会再次执行，而是直接导出）
- 多模块的依赖动态的，导入模块是获取一份导出值的拷贝；可以对导入的值进行更改；

##### ES6 module
- 同样的是每个文件即一个模块；每个模块拥有各自的作用域
- 导出：export（命名导出 和 默认导出）
- 导入：import；导入的变量都是只读的不可更改；
- 自动采取严格模式
- 对模块的依赖是静态的，导入的是变量是对原有值的映射，不可以对导入的变量进行更改；

#### 2.模块打包原理
打包后的bundle包括：
- 最外层的立即执行函数
- installedModules对象，存储导出的值
- __webpack_require__函数，堆模块进行加载完成模块的导入
- modules对象，存储依赖关系的模块

### 三、资源输入输出
webopack会从入口文件开始检索，并将具有依赖关系的模块生成一颗依赖书，最终得到一个chunk。有这个chunk得到的打包产物则为bundle。
#### 1.配置资源入口
- entry和context，entry：配置支持多种类型，字符串，数组，对象和函数；context入口路径前缀可省略
- 确定入口模块位置
- 定义chunk name

#### 2.配置资源出口
- output

### 四、预处理器（一切皆模块）
loader的本质是函数，对接收到的内容进行转换；在module.rules可配置多个loader

### 五、样式处理
- 分离样式文件：extract-text-webpack-plugin(webpack4之前) 和mini-css-extract-plugin(weboack4后)
- 样式预处理： 如scss、less 需sass-loader、less-loader
- PostCSS:编译插件的容器
- css module:css模块化

### 六、代码分片
- 提取公共模块，通过入口划分代码

### 七、其他打包工具
webpack是目前比较全面的打包工具，出webpack之外也有很多性能比较好的打包工具，但通用性不高；
- Rollup：更专注于javascript的打包，自身附加代码少，适合应用于打包javascript库
- Parcel：打包速度快，支持零配置