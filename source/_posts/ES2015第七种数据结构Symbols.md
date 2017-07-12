---
title: ES2015第七种数据结构Symbols
date: 2016-11-02 23:39:29
tags: es2015
---

1997年JavaScript首次被标准化，那时只有六种原始类型，在ES6以前，JS程序中使用的每一个值都是以下几种类型之一：

 - Undefined 未定义
 - Null 空值
 - Boolean 布尔类型
 - Number 数字类型
 - String 字符串类型
 - Object 对象类型

每种类型都是多个值的集合，前五个集合是有限的。布尔类型只有两个值，true和false，不会再创造第三种布尔值；数字类型和字符串类型的值更多。然而，对象类型值的集合是无限的。每一个对象都像珍贵的雪花一样独一无二，每一次你打开一个Web页面，都会创建一堆对象。

>  ES6新特性中的symbol也是值，但它不是字符串，也不是对象，而是是全新的——第七种类型的原始值。Symbol值通过Symbol函数生成。这就是说，对象的属性名现在可以有两种类型，一种是原来就有的字符串，另一种就是新增的Symbol类型。凡是属性名属于Symbol类型，就都是**独一无二**的，可以保证不会与其他属性名产生冲突。

让我们一起探讨一下symbol的实际应用场景。

### 从一个简单的布尔类型出发

有时候你可以非常轻松地将别人的外部数据存储到一个JavaScript对象中。

举 个例子，假设你正在写一个JS库，可以通过CSS transitions使DOM元素在屏幕上移动。你可能会注意到，当你尝试在一个div元素上同时应用多重CSS transitions时并不会生效。实际效果是丑陋而又不连续的“跳闪”。你认为可以修复这个问题，但前提是你需要一种发现给定元素是否已经移动过的方 法。

###### 应当如何解决这个问题呢？

一种方法是，用CSS API来告诉浏览器元素是否正在移动，但这样简直小题大做。在元素移动的第一时间内你的库就应该记录下移动的状态，所以它自然知道元素正在移动。

你真正想要的是一种持续跟踪某个元素正在移动的方法。你可以维护一个数组，记录所有正在移动的元素，每当你的库被调用来移动某个元素时，你可以检索数组来查看元素是否已经存在，亦即它是否正在移动中。

当然，如果数组非常大的话，线性搜索将会非常缓慢。

实际上你只想为元素设置一个标记：
```javascript
if (element.isMoving) {
  smoothAnimations(element);
}
element.isMoving = true;
```
这样也会有一些潜在的问题，事实上，你的代码很可能不是唯一一段操作DOM的代码。

1. 你创建的属性很可能影响到其它使用了for-in或Object.keys()的代码。
2. 一些聪明的库作者可能已经考虑并使用了这项技术，这样一来你的库就会与已有的库产生某些冲突
3. 当然，很可能你比他们更聪明，你先采用了这项技术，但是他们的库仍然无法与你的库默契配合。
4. 标准委员会可能决定为所有的元素增加一个.isMoving()方法，到那时你需要重写相关逻辑，必定会有深深的挫败感。
当然你可以选择一个乏味而愚蠢的命名（其他人根本不会想用的那些名称）来解决最后的三个问题：
```javascript
if (element.__$jorendorff_animation_library$PLEASE_DO_NOT_USE_THIS_PROPERTY$isMoving__) {
  smoothAnimations(element);
}
element.__$jorendorff_animation_library$PLEASE_DO_NOT_USE_THIS_PROPERTY$isMoving__ = true;
```
这只会造成无畏的眼疲劳。为什么这个问题如此困难？我们只想要一个小小的布尔值啊！

### symbol是最终的解决方案

symbol是程序创建并且可以用作属性键的值，并且它能避免命名冲突的风险。

``var mySymbol = Symbol();``
调用Symbol()创建一个新的symbol，它的值与其它任何值皆不相等。

字符串或数字可以作为属性的键，symbol也可以，它不等同于任何字符串，因而这个以symbol为键的属性可以保证不与任何其它属性产生冲突。
```javascript
obj[mySymbol] = "ok!";  // 保证不会冲突
console.log(obj[mySymbol]);  // ok!
```
想要在上述讨论的场景中使用symbol，你可以这样做：
```javascript
// 创建一个独一无二的symbol
var isMoving = Symbol("isMoving");
...
if (element[isMoving]) {
  smoothAnimations(element);
}
element[isMoving] = true;
```
有关这段代码的一些解释：

 - **Symbol("isMoving")**中的isMoving被称作描述。你可以通过console.log()将它打印出来，对调试非常有帮助；你也可以用.toString()方法将它转换为字符串呈现；它也可以被用在错误信息中。

 - element[isMoving]被称作一个以symbol为键（symbol-keyed）的属性。简而言之，它的名字是symbol而不是一个字符串。除此之外，它与一个普通的属性没有什么区别。

 - 以symbol为键的属性属性与数组元素类似，**不能被类似obj.name的点号法访问，你必须使用方括号访问**这些属性。

 - 如果你已经得到了symbol，那么访问一个以symbol为键的属性同样简单，以上的示例很好地展示了如何获取element[isMoving]的值以及如何为它赋值。如果我们需要，可以查看属性是否存在：``if (isMoving in element)``，也可以删除属性：``delete element[isMoving]``。

 - 另一方面，只有当isMoving在当前作用域中时才会生效。这是symbol的弱封装机制：模块创建了几个symbol，可以在任意对象上使用，无须担心与其它代码创建的属性产生冲突。

symbol键的设计初衷是避免冲突，因此JavaScript中最常见的对象检查的特性会忽略symbol键。例如，for-in循环只会遍历对象的字符串键，symbol键直接跳过，Object.keys(obj)和Object.getOwnPropertyNames(obj)也是一样。但是symbols也不完全是私有的：用新的APIObject.getOwnPropertySymbols(obj)就可以列出对象的symbol键。另一个新的API，Reflect.ownKeys(obj)，会同时返回字符串键和symbol键。（我们将在随后的文章中讲解Reflect(反射) API）。

慢慢地我们会发现，越来越多的库和框架将大量使用symbol，语言本身也会将symbol应用于广泛的用途。

但是，到底什么是symbol呢？
```javascript
> typeof Symbol()
"symbol"
```
确切地说，symbol与其它类型并不完全相像。

symbol被创建后就不可变更，你不能为它设置属性（在严格模式下尝试设置属性会得到TypeError的错误）。他们可以用作属性名称，这些性质与字符串类似。

另一方面，每一个symbol都独一无二，不与其它symbol等同，即使二者有相同的描述也不相等；你可以轻松地创建一个新的symbol。这些性质与对象类似。

ES6中的symbol与Lisp和Ruby这些语言中更传统的symbol类似，但不像它们集成得那么紧密。在Lisp中，所有的标识符都是symbol；在JS中，标识符和大多数的属性键仍然是字符串，symbol只是一个额外的选项。

关于symbol的忠告：symbol不能被自动转换为字符串，这和语言中的其它类型不同。尝试拼接symbol与字符串将得到TypeError错误。
```javascript
> var sym = Symbol("<3");
> "your symbol is " + sym
// TypeError: can't convert symbol to string
> `your symbol is ${sym}`
// TypeError: can't convert symbol to string
```
通过String(sym)或sym.toString()可以显示地将symbol转换为一个字符串，从而回避这个问题。

获取symbol的三种方法

有三种获取symbol的方法。

1. 调用Symbol()。正如我们上文中所讨论的，这种方式每次调用都会返回一个新的唯一symbol。

2. 调用Symbol.for(string)。这种方式会访问symbol注册表，其中存储了已经存在的一系列symbol。这种方式与通过Symbol()定义的独立symbol不同，symbol注册表中的symbol是共享的。如果你连续三十次调用Symbol.for("cat")，每次都会返回相同的symbol。注册表非常有用，在多个web页面或同一个web页面的多个模块中经常需要共享一个symbol。

3. 使用标准定义的symbol，例如：Symbol.iterator。标准根据一些特殊用途定义了少许的几个symbol。


