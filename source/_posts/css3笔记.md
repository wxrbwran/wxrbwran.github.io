---
title: css3笔记
date: 2016-10-14 15:26:35
tags: css3
---
>  仅记录一些常用的css3属性

### 边框
##### border-radius
向元素添加圆角边框。
border-radius: 5px 4px 3px 2px; /* 四个半径值分别是左上角、右上角、右下角和左下角，顺时针 */ 
##### box-shadow
向盒子添加阴影,支持添加一个或者多个。
box-shadow:**X轴偏移** **Y轴偏移** *阴影模糊半径* *阴影扩展半径* *阴影颜色* *投影方式*;
1. 阴影模糊半径与阴影扩展半径的区别
阴影模糊半径：此参数可选，其值只能是为正值，如果其值为0时，表示阴影不具有模糊效果，其值越大阴影的边缘就越模糊；
阴影扩展半径：此参数可选，其值可以是正负值，如果值为正，则整个阴影都延展扩大，反之值为负值时，则缩小；
2. X轴偏移量和Y轴偏移量值可以设置为负数
### 颜色
##### Gradient渐变色彩 
CSS3 Gradient 分为线性渐变(linear)和径向渐变(radial)。由于不同的渲染引擎实现渐变的语法不同，这里我们只针对线性渐变的 W3C 标准语法来分析其用法，其余大家可以查阅相关资料。W3C 语法已经得到了 IE10+、Firefox19.0+、Chrome26.0+ 和 Opera12.1+等浏览器的支持。
``linear-gradient:(to top,#fff,#999)``
 - 第一个参数省略时，默认为“180deg”，等同于“to bottom”。
 - 第二个和第三个参数，表示颜色的起始点和结束点，可以有多个颜色值。

<!--more-->
### 字体
##### text-overflow
用来设置是否使用一个省略标记（...）标示对象内文本的溢出。
但是text-overflow只是用来说明文字溢出时用什么方式显示，要实现溢出时产生省略号的效果，还须定义强制文本在一行内显示（white-space:nowrap）及溢出内容为隐藏（overflow:hidden），只有这样才能实现溢出文本显示省略号的效果，代码如下：
```
text-overflow:ellipsis; 
overflow:hidden; 
white-space:nowrap; 
```
word-wrap也可以用来设置文本行为，当前行超过指定容器的边界时是否断开转行
word-wrap:normal|| break-word
##### text-shadow
可以用来设置文本的阴影效果。
``text-shadow: X-Offset Y-Offset blur color;``
### 背景
##### background-origin
设置元素背景图片的原始起始位置。
语法：
``background-origin ： border-box | padding-box | content-box;``
参数分别表示背景图片是从边框，还是内边距（默认值），或者是内容区域开始显示。
需要注意的是，如果背景不是no-repeat，这个属性无效，它会从边框开始显示。
##### background-size
设置背景图片的大小，以长度值或百分比显示，还可以通过cover和contain来对图片进行伸缩。
语法：
``background-size: auto | <长度值> | <百分比> | cover | contain``
取值说明：
1. auto：默认值，不改变背景图片的原始高度和宽度；
2. <长度值>：成对出现如200px 50px，将背景图片宽高依次设置为前面两个值，当设置一个值时，将其作为图片宽度值来等比缩放；
3. <百分比>：0％~100％之间的任何值，将背景图片宽高依次设置为所在元素宽高乘以前面百分比得出的数值，当设置一个值时同上；
4. cover：顾名思义为覆盖，即将背景图片等比缩放以填满整个容器；
5. contain：容纳，即将背景图片等比缩放至某一边紧贴容器边缘为止。

### 选择器
##### 属性选择器
{% codeblock HTML lang:html %}
<a href="xxx.pdf">我链接的是PDF文件</a>
<a href="#" class="icon">我类名是icon</a>
<a href="#" title="我的title是more">我的title是more</a>
{% endcodeblock %}
{% codeblock CSS lang:css %}  
a[class^=icon]{
  background: green;
  color:#fff;
}
a[href$=pdf]{
  background: orange;
  color: #fff;
}
a[title*=more]{
  background: blue;
  color: #fff;
}
{% endcodeblock %}
##### 结构性伪类选择器—root
``:root``选择器，从字面上我们就可以很清楚的理解是根选择器，他的意思就是匹配元素E所在文档的根元素。在HTML文档中，根元素始终是<html>。
##### 结构性伪类选择器—not
``:not``选择器称为否定选择器，和jQuery中的:not选择器一模一样，可以选择除某个元素之外的所有元素。
##### 结构性伪类选择器—empty
:empty选择器表示的就是空。用来选择没有任何内容的元素，这里没有内容指的是一点内容都没有，哪怕是一个空格。
##### 结构性伪类选择器—target
:target选择器称为目标选择器，用来匹配文档(页面)的url的某个标志符的目标元素。
多个url（多个target）处理：
就像上面的例子，#brand与后面的id="brand"是对应的，当同一个页面上有很多的url的时候你可以取不同的名字，只要#号后对的名称与id=""中的名称对应就可以了。
##### 结构性伪类选择器—first-child
“:first-child”选择器表示的是选择父元素的第一个子元素的元素E。简单点理解就是选择元素中的第一个子元素，记住是子元素，而不是后代元素。
``ol > li:first-child{
  color: red;
}
``
##### 结构性伪类选择器—last-child
“:last-child”选择器与“:first-child”选择器作用类似，不同的是“:last-child”选择器选择的是元素的最后一个子元素。例如，需要改变的是列表中的最后一个“li”的背景色，就可以使用这个选择器.
``ul>li:last-child{background:blue;}``
##### 结构性伪类选择器—nth-child(n)
“:nth-child(n)”选择器用来定位某个父元素的一个或多个特定的子元素。其中“n”是其参数，而且可以是整数值(1,2,3,4)，也可以是表达式(2n+1、-n+5)和关键词(odd、even)，但参数n的起始值始终是1，而不是0。也就是说，参数n的值为0时，选择器将选择不到任何匹配的元素。
经验与技巧:当“:nth-child(n)”选择器中的n为一个表达式时，其中n是从0开始计算，当表达式的值为0或小于0的时候，不选择任何匹配的元素。
``ol > li:nth-child(2n){
  background: orange;
}
``
##### 结构性伪类选择器—nth-last-child(n)
“:nth-last-child(n)”选择器和前面的“:nth-child(n)”选择器非常的相似，只是这里多了一个“last”，所起的作用和“:nth-child(n)”选择器有所区别，从某父元素的最后一个子元素开始计算，来选择特定的元素。
##### first-of-type选择器
“:first-of-type”选择器类似于“:first-child”选择器，不同之处就是指定了元素的类型,其主要用来定位一个父元素下的某个类型的第一个子元素。
*我要改变第一个段落的背景为橙色*
``.wrapper > p:first-of-type {
  background: orange;
}``
通过“:first-of-type”选择器，定位div容器中的第一个p元素（p不一定是容器中的第一个子元素），并设置其背景色为橙色。
##### nth-of-type(n)选择器
“:nth-of-type(n)”选择器和“:nth-child(n)”选择器非常类似，不同的是它只计算父元素中指定的某种类型的子元素。当某个元素中的子元素不单单是同一种类型的子元素时，使用“:nth-of-type(n)”选择器来定位于父元素中某种类型的子元素是非常方便和有用的。在“:nth-of-type(n)”选择器中的“n”和“:nth-child(n)”选择器中的“n”参数也一样，可以是具体的整数，也可以是表达式，还可以是关键词。
##### last-of-type选择器
“:last-of-type”选择器和“:first-of-type”选择器功能是一样的，不同的是他选择是父元素下的某个类型的最后一个子元素。
##### nth-last-of-type(n)选择器
“:nth-last-of-type(n)”选择器和“:nth-of-type(n)”选择器是一样的，选择父元素中指定的某种子元素类型，但它的起始方向是从最后一个子元素开始，而且它的使用方法类似于上节中介绍的“:nth-last-child(n)”选择器一样。
##### only-child选择器
“:only-child”选择器选择的是父元素中只有一个子元素，而且只有唯一的一个子元素。也就是说，匹配的元素的父元素中仅有一个子元素，而且是一个唯一的子元素。
##### only-of-type选择器
“:only-of-type”选择器用来选择一个元素是它的父元素的唯一一个相同类型的子元素。这样说或许不太好理解，换一种说法。“:only-of-type”是表示一个元素他有很多个子元素，而其中只有一种类型的子元素是唯一的，使用“:only-of-type”选择器就可以选中这个元素中的唯一一个类型子元素。
##### :enabled选择器
在Web的表单中，有些表单元素有可用（“:enabled”）和不可用（“:disabled”）状态，比如输入框，密码框，复选框等。在默认情况之下，这些表单元素都处在可用状态。那么我们可以通过伪选择器“:enabled”对这些表单元素设置样式。
``input[type="text"]:enabled {
  background: #ccc;
  border: 2px solid red;
}``
通过“:enabled”选择器，修改文本输入框的边框为2像素的红色边框，并设置它的背景为灰色。
#####:disabled选择器
“:disabled”选择器刚好与“:enabled”选择器相反，用来选择不可用表单元素。要正常使用“:disabled”选择器，需要在表单元素的HTML中设置“disabled”属性。
``
input[type="text"]:disabled {
  background: rgba(0,0,0,.15);
  border: 1px solid rgba(0,0,0,.15);
  color: rgba(0,0,0,.15);
}
``
通过“:disabled”选择器，给不可用输入框设置明显的样式。
##### :checked选择器
在表单元素中，单选按钮和复选按钮都具有选中和未选中状态。（大家都知道，要覆写这两个按钮默认样式比较困难）。在CSS3中，我们可以通过状态选择器“:checked”配合其他标签实现自定义样式。而“:checked”表示的是选中状态。
``
input[type="checkbox"]:checked + span {
  opacity: 1;
}
``
##### ::selection选择器
“::selection”伪元素是用来匹配突出显示的文本(用鼠标选择文本时的文本)。浏览器默认情况下，用鼠标选择网页文本是以“深蓝的背景，白色的字体”显示.
有的时候设计要求,不使用上图那种浏览器默认的突出文本效果，需要一个与众不同的效果，此时“::selection”伪元素就非常的实用。不过在Firefox浏览器还需要添加前缀。
``
::-moz-selection {
  background: red;
  color: green;
}
::selection {
  background: red;
  color: green;
}
``
:read-only选择器
“:read-only”伪类选择器用来指定处于只读状态元素的样式。简单点理解就是，元素中设置了“readonly=’readonly’”
``input[type="text"]:read-only{
  border-color: #ccc;
}``
通过“:read-only”选择器来设置地址文本框的样式。
##### :read-write选择器
“:read-write”选择器刚好与“:read-only”选择器相反，主要用来指定当元素处于非只读状态时的样式。
``
input[type="text"]:read-write{
  border-color: #f36;
}
``
使用“:read-write”选择器来设置不是只读控件的文本框样式。
##### ::before和::after
**::before**和**::after**这两个主要用来给元素的前面或后面插入内容，这两个常和"content"配合使用，使用的场景最多的就是清除浮动。
{% codeblock lang:css %}
.clearfix::before,
.clearfix::after {
    content: ".";
    display: block;
    height: 0;
    visibility: hidden;
}
.clearfix:after {clear: both;}
.clearfix {zoom: 1;}
{% endcodeblock %}
当然可以利用他们制作出其他更好的效果，比如右侧中的阴影效果，也是通过这个来实现的。

关键代码分析：
{% codeblock lang:css %}
.effect::before, .effect::after{
    content:"";
    position:absolute;
    z-index:-1;
    -webkit-box-shadow:0 0 20px rgba(0,0,0,0.8);
    -moz-box-shadow:0 0 20px rgba(0,0,0,0.8);
    box-shadow:0 0 20px rgba(0,0,0,0.8);
    top:50%;
    bottom:0;
    left:10px;
    right:10px;
    -moz-border-radius:100px / 10px;
    border-radius:100px / 10px;
}
{% endcodeblock %}
上面代码作用在class名叫.effect上的div的前（before）后(after)都添加一个空元素，然后为这两个空元素添加阴影特效。
## 变形与动画 transform
##### 变形--旋转 rotate()
旋转rotate()函数通过指定的角度参数使元素相对原点进行旋转。它主要在二维空间内进行操作，设置一个角度值，用来指定旋转的幅度。如果这个值为正值，元素相对原点中心顺时针旋转；如果这个值为负值，元素相对原点中心逆时针旋转。
```
.wrapper div {
  width: 200px;
  height: 200px;
  background: orange;
  -webkit-transform: rotate(45deg);
  transform: rotate(45deg);
}
```
##### 变形--扭曲 skew()
扭曲skew()函数能够让元素倾斜显示。它可以将一个对象以其中心位置围绕着X轴和Y轴按照一定的角度倾斜。这与rotate()函数的旋转不同，rotate()函数只是旋转，而不会改变元素的形状。skew()函数不会旋转，而只会改变元素的形状。
Skew()具有**三种**情况：
1. skew(x,y)使元素在水平和垂直方向同时扭曲（X轴和Y轴同时按一定的角度值进行扭曲变形）；
![X,Y扭曲](http://ww1.sinaimg.cn/large/0060lm7Tgw1f8t3j7iejxj309z066mxr.jpg)
第一个参数对应X轴，第二个参数对应Y轴。如果第二个参数未提供，则值为0，也就是Y轴方向上无斜切。
2. skewX(x)仅使元素在水平方向扭曲变形（X轴扭曲变形）；
![X扭曲](http://ww4.sinaimg.cn/large/0060lm7Tgw1f8t3kqlvs1j30a805t0ta.jpg)
3. skewY(y)仅使元素在垂直方向扭曲变形（Y轴扭曲变形）
![Y扭曲](http://ww4.sinaimg.cn/large/0060lm7Tgw1f8t3l1aem0j30a705saak.jpg)
##### 变形--缩放 scale()
缩放 scale()函数 让元素根据中心原点对对象进行缩放。
缩放 scale 具有**三种**情况：
1. scale(X,Y)使元素水平方向和垂直方向同时缩放（也就是X轴和Y轴同时缩放）
![X,Y缩放](http://img.mukewang.com/53391aff000181f703520211.jpg)
``div:hover {
  -webkit-transform: scale(1.5,0.5);
  -moz-transform:scale(1.5,0.5)
  transform: scale(1.5,0.5);
}``
**注意：Y是一个可选参数，如果没有设置Y值，则表示X，Y两个方向的缩放倍数是一样的。**
2. scaleX(x)元素仅水平方向缩放（X轴缩放）
![X缩放](http://img.mukewang.com/53391b0b00016a7002920170.jpg)
3. scaleY(y)元素仅垂直方向缩放（Y轴缩放）
![Y缩放](http://img.mukewang.com/53391b14000169cf03280183.jpg)
##### 变形--位移 translate()
translate()函数可以将元素向指定的方向移动，类似于position中的relative。或以简单的理解为，使用translate()函数，可以把元素从原来的位置移动，而不影响在X、Y轴上的任何Web组件。
translate我们分为**三种**情况：
1、translate(x,y)水平方向和垂直方向同时移动（也就是X轴和Y轴同时移动），一个参数时仅X位移。
![X,Y位移](http://img.mukewang.com/53391c640001709503850257.jpg)
2、translateX(x)仅水平方向移动（X轴移动）
![X位移](http://img.mukewang.com/53391c920001420703810201.jpg)
3、translateY(Y)仅垂直方向移动（Y轴移动）
![Y位移](http://img.mukewang.com/53391ca70001da5e03570211.jpg)
##### 变形--矩阵 matrix()
matrix() 是一个含六个值的(a,b,c,d,e,f)变换矩阵，用来指定一个2D变换，相当于直接应用一个[a b c d e f]变换矩阵。就是基于水平方向（X轴）和垂直方向（Y轴）重新定位元素,此属性值使用涉及到数学中的矩阵，我在这里只是简单的说一下CSS3中的transform有这么一个属性值，如果需要深入了解，需要对数学矩阵有一定的知识。
示例演示：通过matrix()函数来模拟transform中translate()位移的效果。
{% codeblock lang:html %}
<div class="wrapper">
  <div></div>
</div>
{% endcodeblock %}
{% codeblock lang:css %}
.wrapper {
  width: 300px;
  height: 200px;
  border: 2px dotted red;
  margin: 40px auto;
}
.wrapper div {
  width:300px;
  height: 200px;
  background: orange;
  -webkit-transform: matrix(1,0,0,1,50,50);
  -moz-transform:matrix(1,0,0,1,50,50);
  transform: matrix(1,0,0,1,50,50);
}
{% endcodeblock %}
演示结果：
![矩阵变换](http://img.mukewang.com/53391e000001b60b03890278.jpg)
>  matrix(scaleX(),skewX(),skewY(),scaleY(),translateX(),translateY());

## 动画--过渡属性 transition
早期在Web中要实现动画效果，都是依赖于JavaScript或Flash来完成。但在CSS3中新增加了一个新的模块transition，它可以通过一些简单的CSS事件来触发元素的外观变化，让效果显得更加细腻。简单点说，就是通过鼠标的单击、获得焦点，被点击或对元素任何改变中触发，并平滑地以动画效果改变CSS的属性值。

在CSS中创建简单的过渡效果可以从以下几个步骤来实现：
1. 在默认样式中声明元素的初始状态样式；
2. 声明过渡元素最终状态样式，比如悬浮状态；
3. 在默认样式中通过添加过渡函数，添加一些不同的样式。

CSS3的过度transition属性是一个复合属性，主要包括以下几个子属性：

**transition-property**:指定过渡或动态模拟的CSS属性(width,height,transform...)
**transition-duration**:指定完成过渡所需的时间
**transition-timing-function**:指定过渡函数(ease,ease-in,ease-out,ease-in-out,linear...)
**transition-delay**:指定开始出现的延迟时间
先来看transition-property属性

transition-property用来指定过渡动画的CSS属性名称，而这个过渡属性只有具备一个中点值的属性（需要产生动画的属性）才能具备过渡效果,**特别注意：当“transition-property”属性设置为all时，表示的是所有中点值的属性。**
用一个简单的例子来说明这个问题：
>  假设你的初始状态设置了样式“width”,“height”,“background”,当你在终始状态都改变了这三个属性，那么all代表的就是“width”、“height”和“background”。如果你的终始状态只改变了“width”和“height”时，那么all代表的就是“width”和“height”。

## 动画--animation
##### Keyframes介绍
Keyframes被称为关键帧，其类似于Flash中的关键帧。在CSS3中其主要以“@keyframes”开头，后面紧跟着是动画名称加上一对花括号“{…}”，括号中就是一些不同时间段样式规则。
```
@keyframes changecolor{
  0%{
   background: red;
  }
  100%{
    background: green;
  }
}
```
在一个“@keyframes”中的样式规则可以由多个百分比构成的，如在“0%”到“100%”之间创建更多个百分比，分别给每个百分比中给需要有动画效果的元素加上不同的样式，从而达到一种在不断变化的效果。
经验与技巧：在@keyframes中定义动画名称时，**其中0%和100%还可以使用关键词from和to来代表，其中0%对应的是from，100%对应的是to。**
案例演示
通过“@keyframes”声明一个名叫“wobble”的动画，从“0%”开始到“100%”结束，同时还经历了一个“40%”和“60%”两个过程。“wobble”动画在“0%”时元素定位到left为100px，背景色为green，然后在“40%”时元素过渡到left为150px,背景色为orange,接着在“60%”时元素过渡到left为75px，背景色为blue，最后“100%”时结束动画，元素又回到起点left为100px处，背景色变为red。
``animation: wobble 5s ease .1s;``后三个参数与transition相同。
```
HTML:

<div>鼠标放到我身上</div>
CSS:

@keyframes wobble {
  0% {
    margin-left: 100px;
    background:green;
  }
  40% {
    margin-left:150px;
    background:orange;
  }
  60% {
    margin-left: 75px;
    background: blue;
  }
  100% {
    margin-left: 100px;
    background: red;
  }
}
div {
  width: 100px;
  height: 100px;
  background:red;
  color: #fff;
}
div:hover{
  animation: wobble 5s ease .1s;
}
```
**animation-iteration-count**属性主要用来定义动画的播放次数。
语法规则：
animation-iteration-count: infinite | <number> [, infinite | <number>]*
1. 其值通常为整数，但也可以使用带有小数的数字，其默认值为1，这意味着动画将从开始到结束只播放一次。
2. 如果取值为infinite，动画将会无限次的播放。
##### 设置动画播放方向
**animation-direction**属性主要用来设置动画播放方向，其语法规则如下：
animation-direction:normal | alternate [, normal | alternate]*
其主要有两个值：normal、alternate
1. normal是默认值，如果设置为normal时，动画的每次循环都是向前播放；
2. 另一个值是alternate，他的作用是，动画播放在第偶数次向前播放，第奇数次向反方向播放。
例如：通过animation-direction属性，将move动画播放动画方向设置为alternate，代码为：
``animation-direction:alternate;``
###### 设置动画的播放状态
**animation-play-state**属性主要用来控制元素动画的播放状态。
参数：
其主要有两个值：*running*和*paused*。

其中running是其默认值，主要作用就是类似于音乐播放器一样，可以通过paused将正在播放的动画停下来，也可以通过running将暂停的动画重新播放，这里的重新播放不一定是从元素动画的开始播放，而是从暂停的那个位置开始播放。另外如果暂停了动画的播放，元素的样式将回到最原始设置状态。
例如，页面加载时，动画不播放。代码如下：
``animation-play-state:paused;``
##### 设置动画时间外属性
**animation-fill-mode**属性定义在动画开始之前和结束之后发生的操作。主要具有四个属性值：none、forwards、backwords和both。其四个属性值对应效果如下：

|属性值     |效果                                                                |
|:--------:|-------------------------------------------------------------------|
|none      |默认值，表示动画将按预期进行和结束，在动画完成其最后一帧时,动画会反转到初始帧处 |
|forwards  |表示动画在结束后继续应用最后的关键帧的位置                                |
|backwards |会在向元素应用动画样式时迅速应用动画的初始帧                               |
|both      |元素动画同时具有forwards和backwards效果                                |

在默认情况之下，动画不会影响它的关键帧之外的属性，使用animation-fill-mode属性可以修改动画的默认行为。简单的说就是告诉动画在第一关键帧上等待动画开始，或者在动画结束时停在最后一个关键帧上而不回到动画的第一帧上。或者同时具有这两个效果。
例如：让动画停在最一帧处。代码如下：
``animation-fill-mode:forwards; ``
