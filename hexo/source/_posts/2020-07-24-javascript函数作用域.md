---
title: 【翻译】关于javascript函数作用域解释
date: 2020-07-24 10:20:14
tags:
---
### 一、Quiz Time
如下的代码执行后输出什么呢？为什么？

        function jerry() {
            console.log(name)
        }

        function tom() {
            var name = 'tom';
            jerry();
        }

        var name = 'cartoon';
        tom();

上面的代码执行后是会输出 cartoon、tom或者undefined？但更重要的是，你是如何决定答案的呢?你是按Scope吗?还是执行上下文execution context呢?

### 二、Scope(作用域)
上面代码执行的答案是 cartoon，让我们来探讨深入理解一下。
> In JavaScript, Scope is the mechanism to determine the accessibility of variables throughout its existence. It could be inside or outside of a function call.
> 在JavaScript中，作用域是在整个过程中确定变量可访问性的机制。它可以在函数调用的内部或外部。

我们来拆分一下上面的代码，去理解变量的可访问性随着变量的声明和函数的创建如何改变。
<!--more-->
#### 1、Recap（概述）
下面是理解关于javascript执行上下文的几个关键点
- There is something called, Global Execution Context and Function Execution Context.（有全局执行上下文和函数执行上下文。）
- Each execution context has a special thing called, this and the reference to the Outer Environment.（每个执行上下文都有一个特殊的东西，this和对外部环境的引用。）
- When we invoke a function, the JavaScript engine creates an outer reference for the current Function Execution Context.（当我们调用一个函数时，JavaScript引擎为当前函数执行上下文创建一个外部引用。）
- The function has access to the variables defined in the Outer reference and JavaScript engine does a look-up when it is unable to find it in the current execution context.（函数可以访问在外部引用中定义的变量，如果在当前执行上下文中找不到它，JavaScript引擎会进行查找。）

#### 2.Scope and Scope chain
在上面的示例中，有两个函数调用，tom()和jerry()。因此，将创建两个不同的函数执行上下文。
记住，在关键字this等于Window对象时，总会创建一个全局执行上下文，因此示例中我们总共有三个执行上下文，一个是全局执行上下文和两个函数执行上下文tom（）和jerry（）；

- 变量 name在全局执行上下文中创建，并赋值cartoon；
- 当调用tom()函数时，JavaScript引擎为tom()创建了一个执行上下文和一个对外部环境(全局执行上下文)的引用。
- 当tom调用jerry时，JavaScript引擎识别jerry()的词法位置并执行相同的操作。它创建了jerry()的执行上下文和对外部环境的引用。

等等，谁是jerry的外部环境呢？是tom的执行上下文还是全局的执行上下文呢？这取决于另一个问题的答案。

> Who created jerry()? Where is it sitting lexically? 是谁创造了杰里()?它的词法在哪里?

jerry是全局上下文创建的，虽然它在tom的执行上下文中被调用；我们发现，jerry()在词法上位于全局执行上下文并由它创建。按照这个理论，jerry()有一个指向全局执行上下文的指针。

我们还发现，jerry()中没有声明名为name的变量。在执行阶段，它尝试记录name变量。
在执行阶段，JavaScript引擎根据jerry()的外部引用启动查找过程，并在全局执行上下文中查找带有值cartoon的变量name。

在当前执行上下文中查找变量和外部引用的整个过程形成了一个称为作用域链的链。我们还可以得出结论，变量名在函数jerry()的作用域内，因为它在作用域链中被成功找到。

### 3.Change in the Chain

再一次Quiz time，下面代码执行输出什么呢？

        function tom() {
            var name = 'tom';
            function jerry() {
                console.log(name);
            }
            jerry();
        }
        var name = 'cartoon';
        tom();


我们将之前的例子稍作调整，现在函数jerry在tom函数内被创建。jerry执行上下文的外部环境参考会指向tom函数的执行上下文。因此变量name会在作用域链中找到并且在tom函数内定义，因此上面的实例答案是 输出tom。

### 4.Block Scope
当我们了解了作用域的基本原理后，让我们来理解块作用域是什么。代码块由这些大括号{…}定义。如果在代码块中使用关键字let声明变量，则仅在该代码块中可见。

        {
            let name = "tom";
            console.log(name); // tom
        }
        console.log(name) // Error: name is not defined

        // 如果我们用var而不是let来创建变量名，就不会存在这个块作用域的限制

甚至对于if，for，while等等，变量在块级作用域内声明都只能在块级作用域内可见，下面的例子针对for循环；

        for (let counter = 0; counter<10; counter++) {
            console.log(counter);
        }
        console.log(counter): Error,counter is not defined

### 5.总结
理解作用域的基本概念，如执行上下文、外部引用、词汇定位等，将有助于轻松调试复杂的bug。