---
title: 你可能并不需要jQuery
date: 2016-10-11 10:54:55
tags: 
  - javascript
  - jQuery
---
> jQuery是非常优秀的工具，它能让我们开发项目时变得更容易，但如果你想从零开始开发一个全新的项目，你应该考虑一下你的项目是否真的需要引入jQuery。
- 也许你只需要几行技巧性的代码就能解决问题。如果你的项目是面向最新的现代浏览器，你真的可以考虑其它的一些简单的技术来代替jQuery。
- 浏览器的进步给我们带来了很多先进的JavaScript特征，新出现的原生内置(native)JavaScript功能可以很大程度的实现jQuery提供的功能。如果你能了解这些JavaScript新技术，就能在很多地方用纯JavaScript实现以前需要jQuery才能实现的技术。
- 新的mvvm框架的出现，数据驱动的方式解决了项目数据交互量大的时候，操作dom的代码混乱的问题。
## 元素操作
#### addClass
jQuery
``$(el).addClass(className);``
谷歌浏览器，火狐浏览器，IE8+
```
if (el.classList)
  el.classList.add(className);
else
  el.className += ' ' + className;
```
谷歌浏览器，火狐浏览器，IE10+
``el.classList.add(className);``
#### After
jQuery
``$(el).after(htmlString);``
谷歌浏览器，火狐浏览器，IE8+
``el.insertAdjacentHTML('afterend', htmlString);``
#### Append
jQuery
``$(parent).append(el);``
谷歌浏览器，火狐浏览器，IE8+
``parent.appendChild(el);``
#### Before
jQuery
``$(el).before(htmlString);``
谷歌浏览器，火狐浏览器，IE8+
``el.insertAdjacentHTML('beforebegin', htmlString);``
#### Children
jQuery
``$(el).children();``
谷歌浏览器，火狐浏览器，IE8+
```
var children = [];
for (var i = el.children.length; i--;) {
  // Skip comment nodes on IE8
  if (el.children[i].nodeType != 8)
    children.unshift(el.children[i]);
}```
谷歌浏览器，火狐浏览器，IE9+
``el.children``
<!--more-->
#### Clone
jQuery
`$(el).clone();`
谷歌浏览器，火狐浏览器，IE8+
`el.cloneNode(true);``
#### Contains
jQuery
``$.contains(el, child);``
谷歌浏览器，火狐浏览器，IE8+
``el !== child && el.contains(child);``
#### Contains Selector
jQuery
``$(el).find(selector).length;``
谷歌浏览器，火狐浏览器，IE8+
``el.querySelector(selector) !== null``
#### Each
jQuery
``$(selector).each(function(i, el){});``
谷歌浏览器，火狐浏览器，IE8+
```
function forEachElement(selector, fn) {
  var elements = document.querySelectorAll(selector);
  for (var i = 0; i < elements.length; i++)
    fn(elements[i], i);
}
forEachElement(selector, function(el, i){});```
谷歌浏览器，火狐浏览器，IE9+
```
var elements = document.querySelectorAll(selector);
Array.prototype.forEach.call(elements, function(el, i){});```
#### Empty
jQuery
``$(el).empty();``
谷歌浏览器，火狐浏览器，IE8+
```
while(el.firstChild)
  el.removeChild(el.firstChild);```
谷歌浏览器，火狐浏览器，IE9+
``el.innerHTML = '';``
#### 过滤
jQuery
``$(selector).filter(filterFn);``
谷歌浏览器，火狐浏览器，IE8+
```
function filter(selector, filterFn) {
  var elements = document.querySelectorAll(selector);
  var out = [];
  for (var i = elements.length; i--;) {
    if (filterFn(elements[i])){
      out.unshift(elements[i]);
      }
    }
  }
  return out;
}
filter(selector, filterFn);```
谷歌浏览器，火狐浏览器，IE9+
``Array.prototype.filter.call(document.querySelectorAll(selector), filterFn);``
#### 查找子元素
jQuery
``$(el).find(selector);``
谷歌浏览器，火狐浏览器，IE8+
``el.querySelectorAll(selector);``
#### 查找
jQuery
``$('.my #awesome selector');``
谷歌浏览器，火狐浏览器，IE8+
``document.querySelectorAll('.my #awesome selector');``
#### 获取属性值
jQuery
``$(el).attr('tabindex');``
谷歌浏览器，火狐浏览器，IE8+
``el.getAttribute('tabindex');``
#### 获取Html内容
jQuery
``$(el).html();``
谷歌浏览器，火狐浏览器，IE8+
``el.innerHTML``
#### 获取外层Html内容
jQuery
``$('<div>').append($(el).clone()).html();``
谷歌浏览器，火狐浏览器，IE8+
``el.outerHTML``
#### 获取CSS样式
jQuery
``$(el).css(ruleName);``
谷歌浏览器，火狐浏览器，IE8+
// Varies based on the properties being retrieved, some can be retrieved from

el.currentStyle
// https://github.com/jonathantneal/Polyfills-for-IE8/blob/master/

getComputedStyle.js
谷歌浏览器，火狐浏览器，IE9+
``getComputedStyle(el)[ruleName];``
#### 获取文本内容
jQuery
``$(el).text();``
谷歌浏览器，火狐浏览器，IE8+
``el.textContent || el.innerText``
谷歌浏览器，火狐浏览器，IE9+
``el.textContent``
#### Has Class
jQuery
``$(el).hasClass(className);``
谷歌浏览器，火狐浏览器，IE8+
```
if (el.classList){
  el.classList.contains(className);
}
else{
  new RegExp('(^| )' + className + '( |$)', 'gi').test(el.className);
}```
谷歌浏览器，火狐浏览器，IE10+
``el.classList.contains(className);``
#### 元素比较
jQuery
``$(el).is($(otherEl));``
谷歌浏览器，火狐浏览器，IE8+
``el === otherEl``
#### 比较类名
jQuery
``$(el).is('.my-class');``
谷歌浏览器，火狐浏览器，IE8+
```
var matches = function(el, selector) {
  var _matches = (el.matches || el.matchesSelector || el.msMatchesSelector || el.mozMatchesSelector || el.webkitMatchesSelector || el.oMatchesSelector);
  if (_matches) {
    return _matches.call(el, selector);
  } else {
    var nodes = el.parentNode.querySelectorAll(selector);
    for (var i = nodes.length; i--;) {
      if (nodes[i] === el)
        return true;
    }
    return false;
  }
};
matches(el, '.my-class');```
谷歌浏览器，火狐浏览器，IE9+
```
var matches = function(el, selector) {
  return (el.matches || el.matchesSelector || el.msMatchesSelector || el.mozMatchesSelector || el.webkitMatchesSelector || el.oMatchesSelector).call(el, selector);
};
matches(el, '.my-class');```
#### Next
jQuery
``$(el).next();``
谷歌浏览器，火狐浏览器，IE8+
// nextSibling can include text nodes
```
function nextElementSibling(el) {
  do { el = el.nextSibling; } while ( el && el.nodeType !== 1 );
  return el;
}
el.nextElementSibling || nextElementSibling(el);```
谷歌浏览器，火狐浏览器，IE9+
``el.nextElementSibling``
#### Offset
jQuery
``$(el).offset();``
谷歌浏览器，火狐浏览器，IE8+
```
var rect = el.getBoundingClientRect()
{
  top: rect.top + document.body.scrollTop,
  left: rect.left + document.body.scrollLeft
}```
#### Offset Parent
jQuery
``$(el).offsetParent();``
谷歌浏览器，火狐浏览器，IE8+
``el.offsetParent || el``
#### Outer Height
jQuery
``$(el).outerHeight();``
谷歌浏览器，火狐浏览器，IE8+
``el.offsetHeight``
#### Outer Height With Margin
jQuery
``$(el).outerHeight(true);``
谷歌浏览器，火狐浏览器，IE8+
```
function outerHeight(el) {
  var height = el.offsetHeight;
  var style = el.currentStyle || getComputedStyle(el);

  height += parseInt(style.marginTop) + parseInt(style.marginBottom);
  return height;
}
outerHeight(el);```
谷歌浏览器，火狐浏览器，IE9+
```
function outerHeight(el) {
  var height = el.offsetHeight;
  var style = getComputedStyle(el);

  height += parseInt(style.marginTop) + parseInt(style.marginBottom);
  return height;
}
outerHeight(el);```
#### Outer Width With Margin
jQuery
``$(el).outerWidth(true);``
谷歌浏览器，火狐浏览器，IE8+
```
function outerWidth(el) {
  var width = el.offsetWidth;
  var style = el.currentStyle || getComputedStyle(el);

  width += parseInt(style.marginLeft) + parseInt(style.marginRight);
  return width;
}
outerWidth(el);```
谷歌浏览器，火狐浏览器，IE9+
```
function outerWidth(el) {
  var width = el.offsetWidth;
  var style = getComputedStyle(el);

  width += parseInt(style.marginLeft) + parseInt(style.marginRight);
  return width;
}
outerWidth(el);```
#### Outer Width
jQuery
``$(el).outerWidth();``
谷歌浏览器，火狐浏览器，IE8+
``el.offsetWidth``
#### Parent
jQuery
``$(el).parent();``
谷歌浏览器，火狐浏览器，IE8+
``el.parentNode``
#### Position
jQuery
``$(el).position();``
谷歌浏览器，火狐浏览器，IE8+
``{left: el.offsetLeft, top: el.offsetTop}``
#### Position Relative To Viewport
jQuery
```
var offset = el.offset();
{
  top: offset.top - document.body.scrollTop,
  left: offset.left - document.body.scrollLeft
}```
谷歌浏览器，火狐浏览器，IE8+
``el.getBoundingClientRect()``
#### Prepend
jQuery
``$(parent).prepend(el);``
谷歌浏览器，火狐浏览器，IE8+
``parent.insertBefore(el, parent.firstChild);``
#### Prev
jQuery
``$(el).prev();``
谷歌浏览器，火狐浏览器，IE8+
// prevSibling can include text nodes
```
function previousElementSibling(el) {
  do { el = el.previousSibling; } while ( el && el.nodeType !== 1 );
  return el;
}
el.previousElementSibling || previousElementSibling(el);```
谷歌浏览器，火狐浏览器，IE9+
``el.previousElementSibling``
#### Remove
jQuery
``$(el).remove();``
谷歌浏览器，火狐浏览器，IE8+
``el.parentNode.removeChild(el);``
#### Remove Class
jQuery
``$(el).removeClass(className);``
谷歌浏览器，火狐浏览器，IE8+
```
if (el.classList)
  el.classList.remove(className);
else
  el.className = el.className.replace(new RegExp('(^|\\b)' + className.split(' ').join('|') + '(\\b|$)', 'gi'), ' ');```
谷歌浏览器，火狐浏览器，IE10+
``el.classList.remove(className);``
#### Replace From Html
jQuery
``$(el).replaceWith(string);``
谷歌浏览器，火狐浏览器，IE8+
``el.outerHTML = string;``
#### Set Attributes
jQuery
``$(el).attr('tabindex', 3);``
谷歌浏览器，火狐浏览器，IE8+
``el.setAttribute('tabindex', 3);``
#### Set Html
jQuery
``$(el).html(string);``
谷歌浏览器，火狐浏览器，IE8+
``el.innerHTML = string;``
#### Set Style
jQuery
``$(el).css('border-width', '20px');``
谷歌浏览器，火狐浏览器，IE8+
// Use a class if possible
``el.style.borderWidth = '20px';``
#### Set Text
jQuery
``$(el).text(string);``
谷歌浏览器，火狐浏览器，IE8+
```
if (el.textContent !== undefined)
  el.textContent = string;
else
  el.innerText = string;```
谷歌浏览器，火狐浏览器，IE9+
``el.textContent = string;``
#### Siblings
jQuery
``$(el).siblings();``
谷歌浏览器，火狐浏览器，IE8+
```
var siblings = Array.prototype.slice.call(el.parentNode.children);
for (var i = siblings.length; i--;) {
  if (siblings[i] === el) {
    siblings.splice(i, 1);
    break;
  }
}```
谷歌浏览器，火狐浏览器，IE9+
``Array.prototype.filter.call(el.parentNode.children, function(child){
  return child !== el;
});``
#### Toggle Class
jQuery
``$(el).toggleClass(className);``
谷歌浏览器，火狐浏览器，IE8+
```
if (el.classList) {
  el.classList.toggle(className);
} else {
    var classes = el.className.split(' ');
    var existingIndex = -1;
    for (var i = classes.length; i--;) {
      if (classes[i] === className)
        existingIndex = i;
    }
    if (existingIndex >= 0)
      classes.splice(existingIndex, 1);
    else
      classes.push(className);
  el.className = classes.join(' ');
}```
谷歌浏览器，火狐浏览器，IE9+
```
if (el.classList) {
  el.classList.toggle(className);
} else {
  var classes = el.className.split(' ');
  var existingIndex = classes.indexOf(className);
  if (existingIndex >= 0)
    classes.splice(existingIndex, 1);
  else
    classes.push(className);
  el.className = classes.join(' ');
}```
谷歌浏览器，火狐浏览器，IE10+
``el.classList.toggle(className);``
## 事件
#### #### Off
jQuery
``$(el).off(eventName, eventHandler);``
谷歌浏览器，火狐浏览器，IE8+
```
function removeEventListener(el, eventName, handler) {
  if (el.removeEventListener)
    el.removeEventListener(eventName, handler);
  else
    el.detachEvent('on' + eventName, handler);
}
removeEventListener(el, eventName, handler);```
谷歌浏览器，火狐浏览器，IE9+
``el.removeEventListener(eventName, eventHandler);``
#### On
jQuery
``$(el).on(eventName, eventHandler);``
谷歌浏览器，火狐浏览器，IE8+
```
function addEventListener(el, eventName, handler) {
  if (el.addEventListener) {
    el.addEventListener(eventName, handler);
  } else {
    el.attachEvent('on' + eventName, function(){
      handler.call(el);
    });
  }
}
addEventListener(el, eventName, handler);```
谷歌浏览器，火狐浏览器，IE9+
``el.addEventListener(eventName, eventHandler);``
#### Ready
jQuery
``$(document).ready(function(){});``
谷歌浏览器，火狐浏览器，IE8+
```
function ready(fn) {
  if (document.readyState != 'loading'){
    fn();
  } else if (document.addEventListener) {
    document.addEventListener('DOMContentLoaded', fn);
  } else {
    document.attachEvent('onreadystatechange', function() {
      if (document.readyState != 'loading')
        fn();
    });
  }
}```
谷歌浏览器，火狐浏览器，IE9+
```
function ready(fn) {
  if (document.readyState != 'loading'){
    fn();
  } else {
    document.addEventListener('DOMContentLoaded', fn);
  }
}```
#### 指定事件触发
jQuery
``$(el).trigger('my-event', {some: 'data'});``
谷歌浏览器，火狐浏览器，IE8+
// Custom events are not natively supported, so you have to hijack a rndomevent.
// Just use jQuery.
谷歌浏览器，火狐浏览器，IE9+
```
if (window.CustomEvent) {
  var event = new CustomEvent('my-event', {detail: {some: 'data'}});
} else {
  var event = document.createEvent('CustomEvent');
  event.initCustomEvent('my-event', true, true, {some: 'data'});
}
el.dispatchEvent(event);```
#### Trigger Native
jQuery
``$(el).trigger('change');``
谷歌浏览器，火狐浏览器，IE8+
```
if (document.createEvent) {
  var event = document.createEvent('HTMLEvents');
  event.initEvent('change', true, false);
  el.dispatchEvent(event);
} else {
  el.fireEvent('onchange');
}```
谷歌浏览器，火狐浏览器，IE9+
// For a full list of event types: https://developer.mozilla.org/en-US/docs/Web/API/document.createEvent
```
var event = document.createEvent('HTMLEvents');
event.initEvent('change', true, false);
el.dispatchEvent(event);```
## 技巧
#### Array Each
jQuery
``$.each(array, function(i, item){});``
谷歌浏览器，火狐浏览器，IE8+
```
function forEach(array, fn) {
  for (i = 0; i < array.length; i++)
    fn(array[i], i);
}
forEach(array, function(item, i){});```
谷歌浏览器，火狐浏览器，IE9+
``array.forEach(function(item, i){});``
#### 深度扩展
jQuery
``$.extend(true, {}, objA, objB);``
谷歌浏览器，火狐浏览器，IE8+
```
var deepExtend = function(out) {
  out = out || {};
  for (var i = 1; i < arguments.length; i++) {
    var obj = arguments[i];
    if (!obj)
      continue;
    for (var key in obj) {
      if (obj.hasOwnProperty(key)) {
        if (typeof obj[key] === 'object')
          deepExtend(out[key], obj[key]);
        else
          out[key] = obj[key];
      }
    }
  }
  return out;
};
deepExtend({}, objA, objB);```
#### Bind
jQuery
``$.proxy(fn, context);``
谷歌浏览器，火狐浏览器，IE8+
``fn.apply(context, arguments);``
谷歌浏览器，火狐浏览器，IE9+
``fn.bind(context);``
jQuery
``$.extend({}, objA, objB);``
谷歌浏览器，火狐浏览器，IE8+
```
var extend = function(out) {
  out = out || {};
  for (var i = 1; i < arguments.length; i++) {
    if (!arguments[i])
      continue;
    for (var key in arguments[i]) {
      if (arguments[i].hasOwnProperty(key))
        out[key] = arguments[i][key];
    }
  }
  return out;
};
extend({}, objA, objB);```
#### Index Of
jQuery
``$.inArray(item, array);``
谷歌浏览器，火狐浏览器，IE8+
```
function indexOf(array, item) {
  for (var i = 0; i < array.length; i++) {
    if (array[i] === item)
      return i;
  }
  return -1;
}
indexOf(array, item);```
谷歌浏览器，火狐浏览器，IE9+
``array.indexOf(item);``
#### Is Array
jQuery
``$.isArray(arr);``
谷歌浏览器，火狐浏览器，IE8+
```
isArray = Array.isArray || function(arr) {
  return Object.prototype.toString.call(arr) == '[object Array]';
}
isArray(arr);```
谷歌浏览器，火狐浏览器，IE9+
``Array.isArray(arr);``
#### Map
jQuery
``$.map(array, function(value, index){});``
谷歌浏览器，火狐浏览器，IE8+
```
function map(arr, fn) {
  var results = [];
  for (var i = 0; i < arr.length; i++)
    results.push(fn(arr[i], i));
  return results;
map(array, function(value, index){});```
谷歌浏览器，火狐浏览器，IE9+
``array.map(function(value, index){});``
#### Now
jQuery
``$.now();``
谷歌浏览器，火狐浏览器，IE8+
``new Date().getTime();``
谷歌浏览器，火狐浏览器，IE9+
``Date.now();``
#### Parse Html
jQuery
``$.parseHTML(htmlString);``
谷歌浏览器，火狐浏览器，IE8+
```
var parseHTML = function(str) {
  var el = document.createElement('div');
  el.innerHTML = str;
  return el.children;
};
parseHTML(htmlString);```
谷歌浏览器，火狐浏览器，IE9+
``var parseHTML = function(str) {
  var tmp = document.implementation.createHTMLDocument();
  tmp.body.innerHTML = str;
  return tmp.body.children;
};
parseHTML(htmlString);``
#### 解析 Json
jQuery
``$.parseJSON(string);``
谷歌浏览器，火狐浏览器，IE8+
``JSON.parse(string);``
#### Trim
jQuery
``$.trim(string);``
谷歌浏览器，火狐浏览器，IE8+
``string.replace(/^\s+|\s+$/g, '');``
谷歌浏览器，火狐浏览器，IE9+
``string.trim();``
#### Type
jQuery
``$.type(obj);``
谷歌浏览器，火狐浏览器，IE8+
```
Object.prototype.toString.call(obj).replace(/^\[object (.+)\]$/, "$1").toLowerCase();```
## AJAX
#### JSON
jQuery
``$.getJSON('/my/url', function(data) {});``
谷歌浏览器，火狐浏览器，IE8+
```
var request = new XMLHttpRequest();
request.open('GET', '/my/url', true);
request.onreadystatechange = function() {
  if (this.readyState === 4) {
    if (this.status >= 200 && this.status < 400) {
      // Success!
      var data = JSON.parse(this.responseText);
    } else {
      // Error :(
    }
  }
};
request.send();
request = null;```
谷歌浏览器，火狐浏览器，IE9+
```
var request = new XMLHttpRequest();
request.open('GET', '/my/url', true);
request.onload = function() {
  if (request.status >= 200 && request.status < 400) {
    // Success!
    var data = JSON.parse(request.responseText);
  } else {
    // We reached our target server, but it returned an error
  }
};
request.onerror = function() {
  // There was a connection error of some sort
};
request.send();```
谷歌浏览器，火狐浏览器，IE10+
```
var request = new XMLHttpRequest();
request.open('GET', '/my/url', true);
request.onload = function() {
  if (this.status >= 200 && this.status < 400) {
    // Success!
    var data = JSON.parse(this.response);
  } else {
    // We reached our target server, but it returned an error
  }
};
request.onerror = function() {
  // There was a connection error of some sort
};
request.send();```
#### Post
jQuery
``$.ajax({type: 'POST',url: '/my/url',data: data});``
谷歌浏览器，火狐浏览器，IE8+
```
var request = new XMLHttpRequest();
request.open('POST', '/my/url', true);
request.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded; charset=UTF-8');
request.send(data);```
#### Request
jQuery
``$.ajax({type: 'GET',url: '/my/url',success: function(resp) {},error: function() {}});``
谷歌浏览器，火狐浏览器，IE8+
```
var request = new XMLHttpRequest();
request.open('GET', '/my/url', true);
request.onreadystatechange = function() {
  if (this.readyState === 4) {
    if (this.status >= 200 && this.status < 400) {
      // Success!
      var resp = this.responseText;
    } else {
      // Error :(
    }
  }
};
request.send();
request = null;```
谷歌浏览器，火狐浏览器，IE9+
```
var request = new XMLHttpRequest();
request.open('GET', '/my/url', true);
request.onload = function() {
  if (request.status >= 200 && request.status < 400) {
    // Success!
    var resp = request.responseText;
  } else {
    // We reached our target server, but it returned an error
  }
};
request.onerror = function() {
  // There was a connection error of some sort
};
request.send();```
谷歌浏览器，火狐浏览器，IE10+
```
var request = new XMLHttpRequest();
request.open('GET', '/my/url', true);
request.onload = function() {
  if (this.status >= 200 && this.status < 400) {
    // Success!
    var resp = this.response;
  } else {
    // We reached our target server, but it returned an error
  }
};
request.onerror = function() {
  // There was a connection error of some sort
};
request.send();
```
## 特效
#### 淡入淡出
jQuery
``$(el).fadeIn();``
谷歌浏览器，火狐浏览器，IE8+
``` 
function fadeIn(el) {
  var opacity = 0;
  el.style.opacity = 0;
  el.style.filter = '';
  var last = +new Date();
  var tick = function() {
    opacity += (new Date() - last) / 400;
    el.style.opacity = opacity;
    el.style.filter = 'alpha(opacity=' + (100 * opacity)|0 + ')';
    last = +new Date();
    if (opacity < 1) {
      (window.requestAnimationFrame && requestAnimationFrame(tick)) || setTimeout(tick, 16);
    }
  };
  tick();
}
fadeIn(el);
```
谷歌浏览器，火狐浏览器，IE9+
```function fadeIn(el) {
  el.style.opacity = 0;
  var last = +new Date();
  var tick = function() {
    el.style.opacity = +el.style.opacity + (new Date() - last) / 400;
    last = +new Date();
    if (+el.style.opacity < 1) {
      (window.requestAnimationFrame && requestAnimationFrame(tick)) || setTimeout(tick, 16)
    }
  };
  tick();
}
fadeIn(el);
```
谷歌浏览器，火狐浏览器，IE10+
```
el.classList.add('show');
el.classList.remove('hide');
.show {transition: opacity 400ms;}
.hide {opacity: 0;}```
#### Hide
jQuery
``$(el).hide();``
谷歌浏览器，火狐浏览器，IE8+
``el.style.display = 'none';``
#### Show
jQuery
``$(el).show();``
谷歌浏览器，火狐浏览器，IE8+
``el.style.display = '';``