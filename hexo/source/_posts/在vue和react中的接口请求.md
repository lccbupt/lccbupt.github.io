---
title: 搞懂ajax、axios和fetch
date: 2020-05-29 18:29:03
tags: Javascript
---
一、Ajax
> Ajax（异步javascript 和 XML），其本身不是一种新技术，而是一种技术合集的新方法，用来处理浏览器与服务器之间的http通信，是一种异步请求的技术。其主要步骤如下：

- 创建XMLHttpRequest实例
- 发出HTTP请求
- 接收服务器传回的数据
- 更新网页数据

XMLHttpRequest对象是 AJAX 的主要接口，用于浏览器与服务器之间的通信，其本身是一个构造函数，可以通过new命令生成实例，新建实例后就可以调用open()方法发出http请求；但是在平时工作中如何用原生js实现http请求代码比较多，因此会使用很多封装好的ajax方法库，如jquery中可以使用$.ajax()发出http请求，但是jquery库比较庞大，如果仅仅是使用ajax请求，可以考虑只专注http请求的库，比如axios
<!--more-->
二、axios
axios是基于Promise的http库，适用于浏览器和nodejs；Axios通过Promise实现XHR封装，其中Promise是控制手段，XHR是实际发送Http请求的客户端；axois可以看成是ajax技术的一种具体实现方法；其主要特点是：
- 浏览器端发起XMLHttpRequests请求
- node端发起http请求
- 支持Promise API
- 拦截请求和返回
- 转化请求和返回（数据）
- 取消请求
- 自动转化json数据
- 客户端支持抵御XSRF（跨站请求伪造）

axios处理http请求提供了很多功能，但是其作者觉得jsonp的方式并不友好，所以并不支持jsonp的请求，因此在vue或react中需要再引入jsonp库来支持jsonp请求
具体使用文档可参考：https://github.com/axios/axios
三、fetch API
fetch API提供获取资源的接口，它类似于XHR，但提供了更强大灵活的功能，fetch不是ajax的进一步封装，而是原生的js API，并基于promise设计。
fetch 是一个html5的API，因此在兼容性上会有一些问题。fetch使用的例子如下：

        fetch(url).then(function(response) {
          return response.json();
        }).then(function(data) {
          console.log(data);
        }).catch(function(e) {
          console.log("Oops, error");
        });
因为支持率不高，可以引入polyfill：

- 由于 IE8 是 ES3，需要引入 ES5 的 polyfill: es5-shim, es5-sham
- 引入 Promise 的 polyfill: es6-promise
- 引入 fetch 探测库：fetch-detector
- 引入 fetch 的 polyfill: fetch-ie8
- 可选：如果你还使用了 jsonp，引入 fetch-jsonp
- 可选：开启 Babel 的 runtime 模式，现在就使用 async/await

四、关于发送http请求的封装
在vue和react项目中可以将http请求封装管理，会使代码结构更清晰，主要从以下几个方面进行封装request.js

- 与服务端约定状态码，请求头，请求超时等
- 增加错误提示信息如toast或者弹框提示，不同状态码提示不是的响应（异常处理程序）
- 设置请求参数，根据业务必须携带不同参数
- 请求拦截器：根据请求头设定，哪些请求处理可以访问
- 响应拦截器：根据服务端状态码执行不同业务

下面是在使用umi开发时封装的http请求示例：

        
        /**
         * 异常处理程序
         */
        const errorHandler = (error: { response: Response }): Response => {
          const { response } = error;

          if (response && response.status) {
            const errorText = codeMessage[response.status] || response.statusText;
            const { status, url } = response;

            notification.error({
              key: 'api-request',
              message: `请求错误 ${status}: ${url}`,
              description: errorText,
            });
          } else if (!response) {
            notification.error({
              key: 'api-request',
              message: '网络异常',
              description: '您的网络发生异常，无法连接服务器',
            });
          }
          return response;
        };

        /**
         * 配置request请求时的默认参数
         */
        const request = extend({
          errorHandler, // 默认错误处理
          credentials: 'include', // 默认请求是否带上cookie
          headers: { 'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8' },
          requestType: 'form',
          params: {
            _: Date.now(),
            _format: 'json',
          },
          paramsSerializer(params) {
            return stringify(params);
          },
        });
        // 请求拦截器
        request.interceptors.request.use((url, { extraOptions, ...options }: RequestOptions) => {
          const state = getState();

          const { href } = window.location;
          const isSamePage = prevHref === href;

          const uriName = extraOptions?.paramName ?? url;
          const prevRequest =
            extraOptions?.usePrevQuery && isSamePage ? state.request?.[uriName] ?? {} : {};
          
          delay(500).then(() => {
            prevHref = href;
          });

          extraOptions!.url = url;
          extraOptions!.uriName = uriName;
  
          options.data = { ...prevRequest.data, ...options.data, ...extradata };

          if (options.requestType === 'form' && options.data) {
            options.data = stringify(options.data);
          }

          return {
            url: (extraOptions?.isServeApi ? proxyPrefix + apiPrefix : apiPrefix) + url,
            options: { extraOptions, ...options },
          };
        });

        // 响应拦截器
        request.interceptors.response.use(
          async (response, { params, data, extraOptions }: RequestOptions) => {
            const dispatch = getDispatch();
            const { errno, errmsg } = await response?.clone().json?.();

            // eslint-disable-next-line @typescript-eslint/camelcase
            if (errno === API_CODE.NO_LOGIN) {
              // 登录态失效同步到state
              dispatch({
                type: 'user/updateCurrentUser',
                payload: {
                  isLogin: false,
                  currentUser: {},
                },
              });
            }

            if (!extraOptions?.ignoreErr && errno === API_CODE.JUMP) {
              const query = qsParse() as IParams;
              const pathname = '/work/task';
              const key = query.dpkey;
              router.push({
                pathname,
                query: {
                  dpkey: key,
                },
              });
              return;
            }

            if (!extraOptions?.ignoreErr && errno !== API_CODE.OK) {
              notification.error({
                key: 'api-request',
                message: '警告',
                description: errmsg,
              });
            }

            // 请求参数写入store
            dispatch({
              type: 'request/updateUri',
              payload: {
                [extraOptions!.uriName!]: {
                  url: extraOptions!.url,
                  page: getPathname(),
                  params,
                  data: parse(data),
                  status: response.status,
                  errno,
                },
              },
            });

            return response;
          },
        );



参考文章：
- https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API
- https://aotu.io/notes/2017/04/10/fetch-API/index.html
- https://github.com/camsong/blog/issues/2