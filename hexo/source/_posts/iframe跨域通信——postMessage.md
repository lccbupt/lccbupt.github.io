---
title: iframe跨域通信——postMessage
date: 2019-01-03 17:33:20
tags:
---
_____
　　本次在对接cp的时候，平台通过iframe嵌套cp页面，cp页面中又通过iframe嵌套其他页面，在这多个iframe之间需要进行交互，子页面和父页面都需要进行跨域通信,而在iframe中解决跨域通信的最佳方法是使用window对象下的postMessage方法。
_____
<!--more-->
### 一、postMessage使用方法

		otherwindow.postMessage(message, targetOrigin, [transfer])

　　otherwindow:
　　其他窗口的一个引用，比如iframe的contentWindow属性，执行window.open返回的窗口对象，或者是命名或或数值索引的window.frames
　　message：
　　将要发送到其他window的数据。它将会被结构化克隆算法序列化。这意味着你可以不受什么限制的将数据对象安全的传送给目标窗口而无需自己序列化
　　targetOrigin：
　　通过窗口的origin属性来指定哪些窗口能接收到消息事件，其值可以是字符串\* （表示无限制）或者一个URI。在发送消息的时候，如果目标窗口的协议、主机地址或端口这三者的任意一项不匹配targetOrigin提供的值，那么消息就不会被发送；只有三者完全匹配，消息才会被发送。这个机制用来控制消息可以发送到哪些窗口；例如，当用postMessage传送密码时，这个参数就显得尤为重要，必须保证它的值与这条包含密码的信息的预期接受者的origin属性完全一致，来防止密码被恶意的第三方截获。如果你明确的知道消息应该发送到哪个窗口，那么请始终提供一个有确切值的targetOrigin，而不是\*。不提供确切的目标将导致数据泄露到任何对数据感兴趣的恶意站点。
　　transfer（可选）：
　　是一串和message同时传递的Transferable对象.这些对象的所有权将被转移给消息的接收方，而发送一方将不再保有所有权。
____

### 二、监听message事件
　　执行如下代码，其他window可以监听派遣的message：

			window.addEventListener("message", receiveMessage, false)
			function receiveMessage(event) {
				var origin = event.origin
				if(origin !== "http://xxx.com") {
					return
				}
				...
			}
____
### 三、实例
　　具体参考例子如下

	<!-- 父页面inddex.html -->
	<body>
		<iframe src="./iframe.html" id="iframe"></iframe>
		<script>
			// 获取iframe
			iFrame = document.getElementById(iframe)
			// iframe 加载完成立即发送一条消息
			iFrame.onload = function() {
				iFrame.contentWindow.postMessage('messageFromIndex', '*')
			}
		</script>
	</body>	


	<!-- 子页面 iframe.html -->
	<body>
		<h1>this is a test</h1>
		<script>
			//回调函数
			function receiveMessageFromIndex(event) {
				console.log('receivemessage', event)
			}
			//监听message事件
			window.addEventListener("message", receiveMessageFromIndex, false)
		</script>
	</body>
