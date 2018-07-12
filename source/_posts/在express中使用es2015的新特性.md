---
title: 在express中使用es2015的新特性
date: 2017-06-01 18:20:55
tags:
    - express
    - es2015
---

> 在前端开发中使用es2015及正在stage中的js特性已经屡见不鲜，但是在express中使用还是第一次尝试，稍微配置下也是可以的。有没有坑，等使用后再补齐。

### 安装babel
```
"babel-cli": "^6.24.1",
"babel-preset-es2015": "^6.24.1",
"babel-preset-stage-2": "^6.24.1",
```

###根目录下创建.babelrc文件
```
{
  "presets": ["es2015", "stage-2"],
  "plugins": []
}
```
### 安装nodemon
不使用supervisor是因为配合不了babel
```
npm install nodemon -g
```
### 修改package.json
```
"scripts": {
    "start": "nodemon ./bin/www.js --exec babel-node"
  },
```

之后就可以使用es新的语法特性了~

