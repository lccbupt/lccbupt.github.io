---
title: react Hook学习笔记
date: 2020-06-19 11:01:11
tags: React
categories: React
---
{% asset_img react.png React Hook 学习 %}

### 一、什么是react Hook？
React可以分为两种，一种是继承React.Component的class组件，另一种是函数组件；在class组件中有生命周期以及当前组件的state，react为了能在函数组件中使用state和react特性，在react16.8之后增加了Hook特性；所谓的Hook就是一种特殊函数，可以使函数组件“钩入”react特性。
<!--more-->
### 二、Hook API
#### 1.useState Hook
之前的函数组件是无状态的，但是引入了useState Hook后该函数组件就可以是有状态的，简单的例子如下：

    import React, { useState } from 'react'

    function Example() {
        // 声明一个count变量
        const [count, setCount] = useState(0)

        return (
            <div>
                <p>你点击了{count}次</p>
                <button onClick={ () => setCount(count+1) }>点击</button>
            </div>
        )
    }

- 引入useState Hook，在函数组件中存储内部state
- 可读取state值，并通过setState改变state值
- 当调用setState改变state值是，会重新渲染组件
- 组件中可以设置多个state，多次使用useState Hook

#### 2.Effect Hook(useEffect)
Effect Hook 可以让你在函数组件中执行副作用操作（数据获取，设置订阅以及手动更改 React 组件中的 DOM 都属于副作用）
可以把useEffect Hook 看做class组件中的 componentDidMount，componentDidUpdate 和 componentWillUnmount 这三个函数的组合
（1）无需清除的effect
在react更新Dom 之后运行一些额外的代码，比如发送网络请求，记录日志，这些都是无需清除的，操作之后可以忽略。

        import React, { useState, useEffect } from 'react';

        function Example() {
          const [count, setCount] = useState(0);

          // Similar to componentDidMount and componentDidUpdate:
          useEffect(() => {
            // Update the document title using the browser API
            document.title = `You clicked ${count} times`;
          });

          return (
            <div>
              <p>You clicked {count} times</p>
              <button onClick={() => setCount(count + 1)}>
                Click me
              </button>
            </div>
          );
        }
- 在组件内部调用 useEffect，effect中可以直接访问state变量
- 在默认的情况下 useEffect会每次渲染后都执行

（2）需要清除的effect
如订阅外部数据源，这种情况下要清除，防止内存泄漏，在class组件中componentDidMount设置订阅，在componentWillUnmount 中进行清除；
    
        import React, { useState, useEffect } from 'react';

        function FriendStatus(props) {
          const [isOnline, setIsOnline] = useState(null);

          useEffect(() => {
            function handleStatusChange(status) {
              setIsOnline(status.isOnline);
            }
            ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
            // Specify how to clean up after this effect:
            return function cleanup() {
              ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
            };
          });

          if (isOnline === null) {
            return 'Loading...';
          }
          return isOnline ? 'Online' : 'Offline';
        }

- effect 可选的清除机制，可以返回一个清除函数。
- react会在组件卸载的时候执行清除操作
- effect的第二个参数，控制effect中的副作用执行次数

        useEffect(() => {
          document.title = `You clicked ${count} times`;
        }, [count]); // 仅在 count 更改时更新

        useEffect(() => {
          document.title = `You clicked ${count} times`;
        }, []); 传入空数组时，只运行一次effect（仅在组件挂载和卸载时执行）

#### 3.useContext
> 接收一个 context 对象（React.createContext 的返回值）并返回该 context 的当前值。当前的 context 值由上层组件中距离当前组件最近的 <MyContext.Provider> 的 value prop 决定

        const themes = {
          light: {
            foreground: "#000000",
            background: "#eeeeee"
          },
          dark: {
            foreground: "#ffffff",
            background: "#222222"
          }
        };

        const ThemeContext = React.createContext(themes.light);

        function App() {
          return (
            <ThemeContext.Provider value={themes.dark}>
              <Toolbar />
            </ThemeContext.Provider>
          );
        }

        function Toolbar(props) {
          return (
            <div>
              <ThemedButton />
            </div>
          );
        }

        function ThemedButton() {
          const theme = useContext(ThemeContext);
          return (
            <button style={{ background: theme.background, color: theme.foreground }}>
              I am styled by theme context!
            </button>
          );
        }

#### 4.其他额外的Hook
- useReducer
- useCallback
- useMemo
- useRef
- useImperativeHandle
- useLayoutEffect
- useDebugValue

这些api都是在特殊情况下使用，可查阅官网