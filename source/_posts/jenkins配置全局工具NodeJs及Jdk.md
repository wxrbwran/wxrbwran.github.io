---
title: jenkins配置全局工具NodeJs及Jdk
date: 2021-08-10 22:42:56
tags: jenkins
---

### 添加nodejs插件
点击 **系统管理** > **插件管理** > **可选插件** 搜索NodeJs并安装

### 添加全局工具

点击 **系统管理** > **全局工具配置**,此时应该就会出现node配置
<!--more-->
![全局工具配置](https://pic.imgdb.cn/item/611292f25132923bf802f35d.png)

需要注意的是，安装时需在本机先配置好node及jdk环境，因为我们使用的是本地环境，如下图


![jdk](https://z3.ax1x.com/2021/08/10/ft2smQ.png)
![nodejs](https://z3.ax1x.com/2021/08/10/ft25XF.png)