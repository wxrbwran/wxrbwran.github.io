---
title: 使用webpack压缩图片中遇到的问题
date: 2016-10-10 15:50:08
tags: 
  - webpack
  - javascript
---

> 因为换了笔记本，所以开发环境从mac变成了linux。从svn把代码拉下来后遇到的问题记录在此。

代码基本运行良好，但是在使用**image-webpack-loader**时，编译出错...
大意如此：png-bin/gifs-bin等等出错。
在so上爬贴，终于找到了解决方案，这个问题看起来是常出现的，随手记录。
[原贴在这里](https://github.com/gruntjs/grunt-contrib-imagemin/issues/96)

解决办法也很简单：
```
I can say the cause is pngquant-bin module. 
I couldn't track it down to a submodule because after specifying a version, it's auto-fixed:

{
    "pngquant-bin": "0.1.6",
    "gifsicle": "0.1.4",
    "jpegtran-bin": "0.2.3",
    "optipng-bin": "0.3.1",
    "pngquant-bin": "0.1.6"    
}

This in my package.json fixed the issue
Important note: specify those in your package.json, CLEAN node_modules 
directory (completely, even .bin directory), restart your computer, 
I think it's an issue with environment or a link to an exe that doesn't 
exist, I don't know, but I didn't want to track it down so restarting 
was ok (one time). After restart, run npm install and it should work.
```
把{
    "pngquant-bin": "0.1.6",
    "gifsicle": "0.1.4",
    "jpegtran-bin": "0.2.3",
    "optipng-bin": "0.3.1",
    "pngquant-bin": "0.1.6"    
}
加入package.json中，删掉node_modules文件夹，重启，重装依赖，重启。