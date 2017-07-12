---
title: 使用ES2015全局对象--webpack下添加polyfill
date: 2017-01-22 16:32:07
tags: webpack babel
---

>公司使用的技术栈是*react全家桶*，使用*axios*进行ajax通信,axios使用的基于**Promise**实现。在现代浏览器上使用并没有什么问题，但是~~IE全系列不支持Promise~~，包括IE11...

本来想着使用babel后，将代码转换为es5，应该在IE10以上兼容运行，但是实际运行时，报错，没有Promise...orz

长话短说，解决方案：[babel-polyfill](http://babeljs.io/docs/usage/polyfill/)

<!--more-->

------

原来在.babelrc里面添加的是 *babel-plugin-transform-runtime*，但是在这里并不管用，究竟是为什么呢？

- babel-polyfill 使用场景

Babel 默认只转换新的 JavaScript 语法，而不转换新的 API。例如，Iterator、Generator、Set、Maps、Proxy、Reflect、Symbol、Promise 等全局对象，以及一些定义在全局对象上的方法（比如 Object.assign）都不会转译。如果想使用这些新的对象和方法，必须使用 babel-polyfill，为当前环境提供一个垫片。

- babel-runtime 使用场景

Babel 转译后的代码要实现源代码同样的功能需要借助一些帮助函数，例如，{ [name]: 'JavaScript' } 转译后的代码如下所示：
{% codeblock lang:javascript %}
	'use strict';
	function _defineProperty(obj, key, value) {
	  if (key in obj) {
	    Object.defineProperty(obj, key, {
	      value: value,
	      enumerable: true,
	      configurable: true,
	      writable: true
	    });
	  } else {
	    obj[key] = value;
	  }
	  return obj;
	}
	var obj = _defineProperty({}, 'name', 'JavaScript');
{% endcodeblock %}

类似上面的帮助函数 _defineProperty 可能会重复出现在一些模块里，导致编译后的代码体积变大。Babel 为了解决这个问题，提供了单独的包 babel-runtime 供编译模块复用工具函数。

启用插件 babel-plugin-transform-runtime 后，Babel 就会使用 babel-runtime 下的工具函数，转译代码如下：
{% codeblock lang:javascript %}
'use strict';
// 之前的 _defineProperty 函数已经作为公共模块 `babel-runtime/helpers/defineProperty` 使用
var _defineProperty2 = require('babel-runtime/helpers/defineProperty');
var _defineProperty3 = _interopRequireDefault(_defineProperty2);
function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }
var obj = (0, _defineProperty3.default)({}, 'name', 'JavaScript');
{% endcodeblock %}