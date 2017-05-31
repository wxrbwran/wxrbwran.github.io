---
title: 使用requestAnimationFrame做动画控制
date: 2016-10-12 11:04:45
tags: html5
---
### requestAnimationFrame是什么？

在浏览器动画程序中，我们通常使用一个定时器来循环每隔几毫秒移动目标物体一次，来让它动起来。如今有一个好消息，浏览器开发商们决定：“嗨，为什么我们不在浏览器里提供这样一个API呢，这样一来我们可以为用户优化他们的动画。”所以，这个requestAnimationFrame()函数就是针对动画效果的API，你可以把它用在DOM上的风格变化或画布动画或WebGL中。

### 使用requestAnimationFrame有什么好处？

浏览器可以优化并行的动画动作，更合理的重新排列动作序列，并把能够合并的动作放在一个渲染周期内完成，从而呈现出更流畅的动画效果。比如，通过requestAnimationFrame()，JS动画能够和CSS动画/变换或SVG SMIL动画同步发生。另外，如果在一个浏览器标签页里运行一个动画，当这个标签页不可见时，浏览器会暂停它，这会减少CPU，内存的压力，节省电池电量。
**requestAnimationFrame的用法**
{% codeblock requestAnimationFrame lang:javascript %}
// usage:
// instead of setInterval(render, 16) ....
var out = document.querySelector('#out'),
    inner = document.querySelector('#inner'),
    count=0,width=0,inCre;
function changeBackground() {
    inner.style.width = width+'%';
    width++;
    if(width<=100){
        inCre = requestAnimationFrame(changeBackground);
    }else{
        cancelAnimationFrame(inCre)
    }
}
inCre=requestAnimationFrame(changeBackground)
{% endcodeblock %}
<!--more-->
### 对requestAnimationFrame更牢靠的封装

Opera浏览器的技术师**Erik Möller** 把这个函数进行了封装，使得它能更好的兼容各种浏览器。你可以读一读这篇文章，但基本上他的代码就是判断使用4ms还是16ms的延迟，来最佳匹配60fps。下面就是这段代码，你可以使用它，但请注意，这段代码里使用的是标准函数，我给它加上了兼容各种浏览器引擎前缀。
{% codeblock requestAnimationFrame lang:javascript %}
(function() {
    var lastTime = 0;
    var vendors = ['webkit', 'moz'];
    for(var x = 0; x < vendors.length && !window.requestAnimationFrame; ++x) {
        window.requestAnimationFrame = window[vendors[x]+'RequestAnimationFrame'];
        window.cancelAnimationFrame =
          window[vendors[x]+'CancelAnimationFrame'] || window[vendors[x]+'CancelRequestAnimationFrame'];
    }

    if (!window.requestAnimationFrame)
        window.requestAnimationFrame = function(callback, element) {
            var currTime = new Date().getTime();
            var timeToCall = Math.max(0, 16 - (currTime - lastTime));
            var id = window.setTimeout(function() { callback(currTime + timeToCall); },
              timeToCall);
            lastTime = currTime + timeToCall;
            return id;
        };

    if (!window.cancelAnimationFrame)
        window.cancelAnimationFrame = function(id) {
            clearTimeout(id);
        };
}());
{% endcodeblock %}

### requestAnimationFrame API
``
window.requestAnimationFrame(function(/* time */ time){
  // time ~= +new Date // the unix time
});``
回调函数里的参数可以传入时间。

### 各种浏览器对requestAnimationFrame的支持情况
[caniuse](http://caniuse.com/#search=requestAnimationFrame)
谷歌浏览器，火狐浏览器，IE10+都实现了这个函数，即使你的浏览器很古老，上面的对requestAnimationFrame封装也能让这个方法在IE8/9上不出错。