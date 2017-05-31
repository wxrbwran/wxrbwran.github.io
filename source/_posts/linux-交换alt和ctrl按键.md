---
title: linux 交换alt和ctrl按键
date: 2016-10-14 11:49:11
tags: ubuntu
---
### 为什么要交换alt键和ctrl键呢?
珍爱小拇指,远离ctrl, ctrl键每次按时都要移动整个手掌, 而且还要看一眼键盘,实在是不爽啊
alt键就好了只要大拇指稍微移一下就可以按到了.
方法:
在home目录建一个文件  ``.Xmodmap ``
然后在文件中写入下面几句:
```
 remove control = Control_L                                                  
 remove mod1 = Alt_L
 keysym Control_L = Alt_L
 keysym Alt_L = Control_L
 add control = Control_L
 add mod1 = Alt_L
 ```
然后logout再登录就好了.上面的代码一定要写在 .Xmodmap 文件中才行.