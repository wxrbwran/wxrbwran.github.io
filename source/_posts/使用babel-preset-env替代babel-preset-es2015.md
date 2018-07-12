---
title: 使用babel-preset-env替代babel-preset-es2015
date: 2017-10-02 19:12:01
tags: babel
---

> **Babel** 是每个 FEer/Node.js 的使用者都会使用的一个代码转(bian)换(yi)器，它可以把 ES6、ES7 等语法转换成 ES5 的语法，使其能在更多环境下运行。

但是随着浏览器和 Node.js 的版本迭代，他们对新语法的支持也越来越好。但是非常尴尬的是，我们总是使用 Babel 把所有代码一股脑转换成 ES5。这意味着我们抛弃了性能优秀的 let、const 关键字，放弃了简短的代码，而选择了又长又丑像坨屎的经过变换后的代码。

即使仅仅将代码跑在对 ES5 支持度在 99% 的 Node 6，一旦使用了 import 关键字，你就得用 Babel 对代码进行转换，一般还是全部转换为 ES5，辣鸡 Node.js 竟然还不支持 import 和 export。
## 那有没有什么工具能智能识别当前运行环境，并且进行适当的转换，以及填充适当的 **polyfill** 呢？
<!--more-->
还真有，而且是 Babel 官方提供的，一个名为 **babel-preset-env** 的插件。它不需要你自行添加任何 preset，比如我们最常用的 es2015，它能根据设置智能转换代码。

具体的配置方法自行看[文档](https://github.com/babel/babel-preset-env#options)，很容易能看懂，这里就提一下值得特别注意的部分。

1. 如果在 .babelrc 中仅仅使用下面的设置的话，那 babel-preset-env 会执行与 babel-preset-latest 一样的转换。
```js
{
  "presets": ["env"]
}
```
2. targets.browsers 需要使用 browserslist 的配置方法，但是其设置会被 targets.[chrome, opera, edge, firefox, safari, ie, ios, android, node, electron] 覆盖。
3. targets.node 设置为 true 或 "current" 可以根据当前 Node.js 版本进行动态转换。也可以设置为具体的数字表示需要支持的最低的 Node.js 版本。
4. targets.uglify 会把代码完全转换为 ES5 以支持压缩 JS 代码。
5. include 和 exclude 可以当作是白名单和黑名单，分别表示一定会执行的转换和一定不会执行的转换，其项是数组。
6. useBuiltIns 可以根据之前的配置自行添加 polyfill，默认不开启。安装 babel-polyfill 后只要引入一次就行：
```js
import "babel-polyfill";
```
 ### 干货
作为一个仅仅使用 Node 执行的项目，你可以用下面的 .babelrc 文件：
```js
{
  "presets": [
    ["env", {
      "targets": {
        "node": "current"
      },
      "useBuiltIns": "usage"
    }]
  ]
}
```
前端项目：
```js
{
  "presets":[
    ["env", {
      "modules": false,
      "targets": {
        "browsers": ["> 1%", "last 2 versions", "not ie <= 8"]
      }
    }],
    "stage-2",
    "react-app"
  ],
  "comments": false,
  "plugins": [
    "transform-runtime"
  ]
}
```
最后，别忘了安装 babel-cli，毕竟你还需要使用 babel-node 来执行代码。



