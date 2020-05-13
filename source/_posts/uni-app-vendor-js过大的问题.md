---
title: uni-app vendor.js过大的问题
date: 2020-05-13 10:50:55
tags: uni-app
---

> 年后和小伙伴们开始开发小程序，随着小程序功能逐渐丰富，代码越来越大，我们也开始使用分包来减少主包的文件体积，但是因为我们是使用uni-app来开发的，vendor.js 主文件承担了主要的代码逻辑，所以需要找到方法来优化一下。

<!--more-->

### 启用分包

1. 在src根目录中新建pagesA文件夹

2. 在pages.json加入下面代码

```json
{
  "subPackages" [
  	{
  		"root": "pageA",
  		"pages": [
  			{
  				"path": '页面相对pagesA地址： page-demo/page-demo',
  				"style": {
  					"navigationBarTitleText": "title"
					}
				}
  		]
		}
  ],
	"preloadRule": {
    "pages/some-page/some-page": {
      "network": "all",
      "packages": ["pagesA"]
    }
  },
}
```



分包的配置和主pages配置基本一致，只是上面包裹了一个字段，下面的preloadRule告诉小程序，我们打开some-page页面的时候，需要预加载“pagesA”分包，优化用户体验。

### 优化分包

配置uni-app的manifest.json的“mp-weixin”字段

```json
"optimization": {
  "subPackages": true
}
```

这样就开启了分包优化。

大致意思是如果一个文件或组件只在分包页面中使用，那文件就打包在分包中。如果在多个分包或主包中使用，那代码就会打包在主包中。

### 启动命令优化

官方指引：

- `cli`创建的项目可以在`pacakge.json`中添加参数`--minimize`，示例：`"dev:mp-weixin": "cross-env NODE_ENV=development UNI_PLATFORM=mp-weixin vue-cli-service uni-build --watch --minimize"`

这样的话，就在开发环境开启了代码压缩，立竿见影，代码压缩了50%左右。

