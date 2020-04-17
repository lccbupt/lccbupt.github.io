---
title: rem实现自适应布局
date: 2019-01-10 14:02:16
tags: 
	- CSS
	- Javascript
---
### 一、rem是指什么？
　　rem是指相对于根元素的字体大小单位（font size of the root element），它是一个相对单位，与之相似的还有em单位，em是指相对于父元素的字体大小单位。
_____
<!--more-->
### 二、为什么要用rem？rem适用于哪些场景？
　　因为浏览器以及其他兼容性问题，rem比较常用于webapp开发或者h5页面开发，而且在移动端的开发中rem更能发挥其优点。现在自适应布局的有很多种方法，比如固定宽度，百分比布局，viewport进行缩放，响应布局等，因为现在的各类显示器屏幕尺寸特别多，移动端的手机屏幕大小也都不统一，设计师在出图的时候只会出一份标准的设计图，如果采用固定宽度就必须要保证设计图中尽量少出现一些图片，对于设计来说就会有很大的局限，因为在不同尺寸屏幕的时候设计图就要出现一定比例的缩放，为了保证能等比例的实现设计图中的交互，这时我们就需要使用rem了，rem的方法简单又高效。
____
### 三、rem实现原理
　　rem主要是相对根元素的字体大小，我们可以自己实现一个小demo，比如
	
	html{
		font-size:20px;
	}
	button{
		width:6rem;
		height:3rem;
		font-size:1.2rem;
		line-height:3rem;
		background:#ccc;
	}// 此时按钮的宽度为120px,高度为60px，若html的font-size为10px;则按钮的宽度为60px,高度为30px;

　　因此从上面的例子中我们可以推算：
　　1rem = 10px根元素(font-size:10px)
　　1rem = 20px根元素(font-size:20px)
　　如上面所得出rem与根元素的字体font-size大小有关，改变根元素的字体大小就可以等比例的改变其他元素的大小。
　　在实际开发中，我首先和定义一个标准的设计图尺寸，比如以宽度为640px的为准，先计算如下表格：

| 宽度 | 屏幕比例 | html font-size | 元素宽度rem | 元素宽度px |
| ------ | ------ | ------ | ------ | ------ |
|640|1|20px|200px|10rem|
|480|0.75|15px|150px|10rem|
|320|0.5|10px|100px|10rem|
|384|0.6|12px|120px|10rem|

　　具体自适应rem的js实现方法：

	//designWidth:设计稿的实际宽度值，需要根据实际设置
	//maxWidth:制作稿的最大宽度值，需要根据实际设置
	//这段js的最后面有两个参数记得要设置，一个为设计稿实际宽度，一个为制作稿最大宽度，例如设计稿为750，最大宽度为750，则为(750,750)
	;(function(designWidth, maxWidth) {
		var doc = document,
		win = window,
		docEl = doc.documentElement,
		remStyle = document.createElement("style"),
		tid;

		function refreshRem() {
			var width = docEl.getBoundingClientRect().width;
			maxWidth = maxWidth || 540;
			width>maxWidth && (width=maxWidth);
			var rem = width * 100 / designWidth;
			remStyle.innerHTML = 'html{font-size:' + rem + 'px;}';
		}

		if (docEl.firstElementChild) {
			docEl.firstElementChild.appendChild(remStyle);
		} else {
			var wrap = doc.createElement("div");
			wrap.appendChild(remStyle);
			doc.write(wrap.innerHTML);
			wrap = null;
		}
		//要等 wiewport 设置好后才能执行 refreshRem，不然 refreshRem 会执行2次；
		refreshRem();

		win.addEventListener("resize", function() {
			clearTimeout(tid); //防止执行两次
			tid = setTimeout(refreshRem, 300);
		}, false);

		win.addEventListener("pageshow", function(e) {
			if (e.persisted) { // 浏览器后退的时候重新计算
				clearTimeout(tid);
				tid = setTimeout(refreshRem, 300);
			}
		}, false);

		if (doc.readyState === "complete") {
			doc.body.style.fontSize = "16px";
		} else {
			doc.addEventListener("DOMContentLoaded", function(e) {
				doc.body.style.fontSize = "16px";
			}, false);
		}
	})(750, 750);

_____
	;(function(win, lib) {
	    var doc = win.document;
	    var docEl = doc.documentElement;
	    var metaEl = doc.querySelector('meta[name="viewport"]');
	    var designEl = doc.querySelector('meta[name="design"]');
	    var dpr = 0;
	    var scale = 0;
	    var dsw = 480;
	    var tid;
	    var flexible = lib.flexible || (lib.flexible = {});
	    var maxWidth = 640;
	    
	    if (metaEl) {
	        console.warn('将根据已有的meta标签来设置缩放比例');
	        var match = metaEl.getAttribute('content').match(/initial\-scale=([\d\.]+)/);
	        if (match) {
	            scale = parseFloat(match[1]);
	            dpr = parseInt(1 / scale);
	        }
	    }
	    // 通过design设计稿
	    if (designEl) {
	        var content = designEl.getAttribute('content');
	        if (content) {
	            var designWidth = content.match(/design\-width=([\d\.]+)/);
	            maxWidth = content.match(/max\-width=([\d\.]+)/);
	            if (designWidth) {
	                dsw = parseFloat(designWidth[1]);
	            }
	            if (maxWidth) {
	                maxWidth = maxWidth[1];
	                var css = 'body { max-width:' + maxWidth +'px; }',
	                style = document.createElement('style');
	                style.type = 'text/css';
	                if (style.styleSheet){
	                  style.styleSheet.cssText = css;
	                } else {
	                  style.appendChild(document.createTextNode(css));
	                }
	                writeHead(style)
	            }
	        }
	    }
	    if (!dpr && !scale) {
	        var isAndroid = win.navigator.appVersion.match(/android/gi);
	        var isIPhone = win.navigator.appVersion.match(/iphone/gi);
	        var devicePixelRatio = win.devicePixelRatio;
	        if (isIPhone) {
	            // iOS下，对于2和3的屏，用2倍的方案，其余的用1倍方案
	            if (devicePixelRatio >= 3 && (!dpr || dpr >= 3)) {                
	                dpr = 3;
	            } else if (devicePixelRatio >= 2 && (!dpr || dpr >= 2)){
	                dpr = 2;
	            } else {
	                dpr = 1;
	            }
	        } else {
	            // 其他设备下，仍旧使用1倍的方案
	            dpr = 1;
	        }
	        scale = 1 / dpr;
	    }
	    docEl.setAttribute('data-dpr', dpr);
	    if (!metaEl) {
	        metaEl = doc.createElement('meta');
	        metaEl.setAttribute('name', 'viewport');
	        metaEl.setAttribute('content', 'initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');
	        writeHead(metaEl);
	    }
	    // 写入头部
	    function writeHead(el) {
	        if (docEl.firstElementChild) {
	            docEl.firstElementChild.appendChild(el);
	        } else {
	            var wrap = doc.createElement('div');
	            wrap.appendChild(el);
	            doc.write(wrap.innerHTML);
	        }
	    }
	    // 设置html的rem
	    function refreshRem(){
	        // docEl.clientWidth客户端宽度
	        // docEl.getBoundingClientRect().width 文档宽度
	        var width = docEl.clientWidth || docEl.getBoundingClientRect().width;
	        if (width / dpr > maxWidth) {
	            width = maxWidth * dpr;
	        }
	        // var rem = width / 10;
	        var rem = 100 * (width / dsw);
	        docEl.style.fontSize = rem + 'px';
	        flexible.rem = win.rem = rem;
	    }
	    refreshRem();
	    win.addEventListener('resize', function() {
	        clearTimeout(tid);
	        tid = setTimeout(refreshRem, 300);
	    }, false);
	    win.addEventListener('pageshow', function(e) {
	        if (e.persisted) {
	            clearTimeout(tid);
	            tid = setTimeout(refreshRem, 300);
	        }
	    }, false);
	    // 设置body字体大小
	    
	    flexible.dpr = win.dpr = dpr;
	    flexible.refreshRem = refreshRem;
	    flexible.rem2px = function(d) {
	        var val = parseFloat(d) * this.rem;
	        if (typeof d === 'string' && d.match(/rem$/)) {
	            val += 'px';
	        }
	        return val;
	    }
	    flexible.px2rem = function(d) {
	        var val = parseFloat(d) / this.rem;
	        if (typeof d === 'string' && d.match(/px$/)) {
	            val += 'rem';
	        }
	        return val;
	    }
	})(window, window['lib'] || (window['lib'] = {}));

