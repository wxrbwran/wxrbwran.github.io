---
title: canvas 字体颜色设置顺序
date: 2020-05-19 15:34:19
tags: canvas
---

> 上星期在小程序中使用echarts画图，真机体验太卡，四五次情况下就会卡住一次渲染不出来。切换到uni-app推荐的ucharts后，稍微修改下源码，就可以添加自己需要的功能和样式。其中字体的问题，比较有意思，记录一下。

<!--more-->

### 问题

我们折线图上的点和数值需要自定义颜色，比如y轴数值90，这个在折线图上需要显示绿色的圆点和绿色的90文本。将颜色传过去后，点颜色是对的，但是数值的颜色不对，总是相邻的上一个的数值的颜色。

### 解决

经过重重查找筛选后，将问题集中大到“drawPointText”方法中，原始的canvas的文字绘制步骤是：

```javascript
context.beginPath();
context.setFontSize(fontsize);
context.fillText(text);
context.setFillStyle(color);
context.stroke();
context.closePath();
```

在其他地方无明显错误的情况下，将绘制的顺序修改如下：

```javascript
context.beginPath();
context.setFontSize(fontsize);
context.setFillStyle(color);
context.fillText(text);
context.stroke();
context.closePath();
```

将**fillText**移到最后，文本的绘制就正常了。

### 结论

和html/css分工合作不同，canvas对绘制的顺序还是有一定要求的。