---
title: vue开发笔记
date: 2020-04-17 10:23:53
tags: 
    - vue 
    - Javascript
categories: Vue
---
### vue 开发笔记
日常工作中某些业务逻辑不复杂页面交互多的页面我通常采用vue来开发，使用vue以及其相关框架技术，很好的整合代码，简单的h5活动页面可以将弹框，toast提示，header以及footer等实现组件化，方便使用和复用，很好的提高了开发效率。其中也有很多细节需要多总结。
<!--more-->
#### 一、vue中style标签中的scoped和module
在vue中每个组件都可以定义自己所属的样式，为了各个组件和页面之间的样式不互相影响，可以在style标签上定义两个参数：scoped 和module
#### 1.关于scoped css

- 当 style 标签中有scoped属性，则它的css样式仅对当前组件元素；
- 通过postCss实现转换
- 组件中可以既包含全局样式css也可以包括scoped css
- 使用scoped时，父组件的样式不会关联到子组件中；但是子组件中的根节点将同时受到父组件的作用域CSS和子组件的作用域CSS的影响。这样父组件就可以为布局设计子组件根元素的样式。
- 深度选择器：如果你想让某一个选择器样式影响子组件；你可以使用‘ >>> ’此选择符；（一些css预处理器中，比如sass，可能不支持‘>>>’该选择符，你可以使用/deep/ 或者::v-deep）
- 通过v-html产生的Dom元素并不会受scoped css样式影响；但是仍然可以使用深度选择器来设置它们的样式。

#### 2.关于css modules
css modules是比较流行的css处理系统模块；可以替代以上描述的scoped css；

- 使用方法：首先 css modules 必须通过配置开启：modules: true -> css-loader；然后style标签添加module

- module属性可以将CSS模块局部变量对象作为名称为$style的计算属性注入到组件中。你可以在你的模板中使用动态类绑定:

        <template>
          <p :class="$style.red">
            This should be red
          </p>
        </template>

- 除此之外你也可以在javascript中获取到css modules属性

#### 二、vue-router中的路由模式：history和hash
在单页面应用中可以使用vue-router配置多个路由页面；但是在vue-router中的路由配置有一个选项mode用来指定路由模式；
##### mode
- 类型： string
- 参数值： hash | history | abstract
- hash：使用URL hash值来作路由。支持所有浏览器，包括不支持 HTML5 History Api 的浏览器；页面在任一路由刷新都不会报错；不用增加额外的服务端配置
- history：依赖 HTML5 History API 和服务器配置；在任一路由刷新页面时如果不增加服务端配置重新定向，会造成404；