---
title: web错误收集
date: 2021-09-26 14:58:49
tags: 
  - web
  - 异常处理
---

> 现在前端web项目通过使用monorepo及jenkins,基本实现了组件/库的服用和ci/cd。但是项目的错误收集上报仍然没有集成，这里简单记录下web项目的错误采集。

<!--more-->
### window.onerror
window.onerror会全局的在JavaScript运行时错误、语法错误发生时触发。
```js
window.onerror = (msg, url, lineNum, colNum, err) => {
  console.log(`错误发生的异常信息（字符串）:${msg}`)
  console.log(`错误发生的脚本URL（字符串）:${url}`)
  console.log(`错误发生的行号（数字）:${lineNum}`)
  console.log(`错误发生的列号（数字）:${colNum}`)
  console.log(`错误发生的Error对象（错误对象）:${err}`)
};
```
>注意：这里我们可以拿到的是被throw出来，没有被catch过的错误，即同步错误。而不能拿到promise这样的错误，即异步错误。

window.onerror 只能获取script错误，对静态资源如图片/css/script加载错误无能为力，就要使用下面的``window.addEventListener('error')``
### window.addEventListener('error')

对于资源的加载错误事件中，``canBubble: false``，所以理所应当的window.onerror是拿不到资源加载错误的，而addEventListener则可以拿到错误。但是在拿到错误以后需要简单的区分一下是资源加载错误还是其他错误，因为该方法也能够捕获语法错误等一系列其他错误。

方法也很简单，他们之间有一个很明显的区别，其他的普通错误会有一个``message``字段，资源加载错误没有这个字段，这样只要让这一段代码运行在所有资源之前，那就可以拿到这方面的错误了。
```js
window.addEventListener('error', (e) => {
    console.log(e)
    cosnole.log(e.message)
    const { type } = e
    const targetEvent = e
    const { message, error } = targetEvent
    const immutableTarget = e.target || e.srcElement
    if (message) {
      // 构造异常数据格式并上报
      // js 错误
      uncaughtErrorHandler(targetEvent)
    } else if (immutableTarget) {
      // 资源错误
      resourceErrorHandler(targetEvent)
    }
}, true)

```
>需要注意的是这里拿到的是一个event事件，和前面不一样，拿到的并不是一个error对象。

### unhandledrejection
在前文中提到``Promise``中的错误并不能被try...catch和window.onerror捕获。这时候我们就需要unhandledrejection来帮我们捕获这部分错误。
```js
window.addEventListener('unhandledrejection', (e) => {
  console.log(`Promise.reject()中的内容，告诉你发生错误的原因:${e.reason}`);
  console.log(`Promise对象 :${e.promise}`);
  // 构造异常数据格式并上报
});
```

### console.error
``console.error``常常被视为打印的日志，可预知的错误，已经被捕获的错误，已经被处理过的内容。所以往往会被忽视不去处理。
下面这样的代码总是很常见,做了很多事情，用一个大大的try...catch,将异常捕获然后打一个console.error完事，可能对于异常处理这样已经完事，捕获住了错误，没有让程序崩溃，但如果对于错误收集这也是不可缺少的一部分
```js
  try {
    // some code
  } catch (err) {
    console.error(err)
  }
```
所以稍稍改造一下console.error，让每一次触发console.error的时候我们可以做一些事情，例如对错误收集系统做一下上报什么的。
```js
console.error = (func => {
  return (...args) => {
    // 在这里就可以收集到console.error的错误,做一些事情
    func.apply(console, args);
  }
})(console.error);
```
### window.fetch & XMLHttpRequest
这里需要一个replace工具函数来复写原对象。
```js
export function replace(
  source: any,
  name: string,
  behavior: (...args: any[]) => any
) {
  if (!(name in source)) {
    return
  }
  const original = source[name]
  const wrapped = behavior(original)
  source[name] = wrapped

  return original
}
```
**fetch**
``请求状态码``，则可以通过覆写 window.fetch 和 XMLHttpRequest 对象来实现监听，覆写 fetch ，以下是简化后的代码：
```js
fetchOriginal = replace(
    global,
    'fetch',
    (origin) =>
      function call(...args: any[]) {
        return origin
          .apply(this, args)
          .then((res: Response) => {
            const [url, conf] = args
            const detail: FetchErrorDetail = {
              req: {
                url,
                method: conf && conf.method,
                data: (conf && conf.body) || {},
              },
              res: {
                status: res.status,
                statusText: res.statusText,
              },
            }
            client.addAction('fetch', detail, 'fetch')

            if (!res.status || res.status >= 400) {
              networkDispatcher(FETCH_ERROR, detail)
            }
            return res
          })
          .catch((err: Error) => {
            networkDispatcher(FETCH_ERROR, err)
          })
      }
  )
}
```
**XMLHttpRequest**
```js

const xhrOriginal = access
  ? {
      open: XMLHttpRequest.prototype.open,
      send: XMLHttpRequest.prototype.send,
    }
  : {}
const desc = {
    method: '',
    url: '',
  }

  const xhrProto = XMLHttpRequest?.prototype

  xhrOriginal.open = replace(
    xhrProto,
    'open',
    (origin) =>
      function call(...args: any[]) {
        const [method, url] = args
        desc.method = method
        desc.url = url
        return origin.apply(this, args)
      }
  )

  xhrOriginal.send = replace(
    xhrProto,
    'send',
    (origin) =>
      function call(...args: any[]) {
        this.addEventListener('readystatechange', function handle() {
          if (this.readyState === 4) {
            // 上报地址不监听
            if (desc.url !== client._config.endpoint) {
              const detail: AjaxErrorDetail = {
                req: {
                  url: desc.url,
                  method: desc.method,
                  data: args[0] || {},
                },
                res: {
                  status: this.status,
                  statusText: this.statusText,
                  response: this.response,
                },
              }

              client.addAction('ajax', detail, 'ajax')
              if (!this.status || this.status >= 400) {
                // 上报失败请求信息
                networkDispatcher(AJAX_ERROR, detail)
              }
            }
          }
        })
        return origin.apply(this, args)
      }
  )
```

### vue
[Vue.config.errorHandler](https://v3.cn.vuejs.org/api/application-config.html#errorhandler)

### react
[Error Boundaries](https://react.yubolun.com/docs/error-boundaries.html)