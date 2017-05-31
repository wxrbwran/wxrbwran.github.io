---
title: linux中使用deepinQQ
date: 2016-10-10 10:09:38
thumbnail: https://sqimg.qq.com/qq_product_operations/im/qqlogo/logo.png
banner: 
tags: 
  - ubuntu
  - software
---
### linux 运行QQ

我们知道在deepin linux上面，deepin团队做了很多wine的应用程序，但是在其他的linux发行版上却没有这种待遇，下面介绍一下，如何在继续debian的linux发行版上运行deepin的wine应用程序。

---
>       本文基于ubuntu16.04

### 安装crossover

[crossover百度网盘下载](http://pan.baidu.com/s/1gflTQd9#list/path=%2F)

注：资源文件夹里面有多个版本的crossover，后缀加了free是临时破解版，请支持正版，但是存在部分功能性问题，不影响deepinwine的软件包使用。没有free的没有功能性问题，但是可能出现deepinwine软件兼容性问题，试用15天的，如果支持crossover的朋友请购买正版。建议大家来回覆盖安装这两个版本使用，基本可以互补。如果有兴趣的朋友可以和我一起讨论研究解决问题。
对于什么是来回覆盖安装，举个例子：free版是不能创建容器的，那么我们先安装不是free的版本也就是试用版，把容器创建好了以后，在覆盖安装free版就可以使用了。所以两个版本功能是互补的，虽然麻烦，但还是不影响正常使用的。

##### 添加32位库的支持

如果是64位系统，先添加对32位库的支持：
```
sudo dpkg --add-architecture i386
sudo apt-get update
###### 可能需要添加下列32位库
sudo apt-get install lib32z1 lib32ncurses5
```

##### 安装crossover

如果要安装14版本，
从上面的分享地址里下载crossover_14.1.11-1_all.deb crossover_14.1.11-1_all-free.deb deepin-crossover_0.5.14_all.deb三个文件,依次安装。
如果安装15，crossover-15_15.0.3-1_all.deb crossover-15_15.0.3-1_all-free.deb deepin-crossover-helper_1.0deepin0_all.deb 并依次安装。

### 安装deepin的wine程序

[QQ 8.x，需要Crossover 15](http://packages.deepin.com/deepin/pool/non-free/a/apps.com.qq.im/)
[QQ 7.x 支持Crossover 14](http://packages.deepin.com/deepin/pool/non-free/d/deepinwine-qq/)
[其他的deepin wine程序](http://packages.deepin.com/deepin/pool/non-free/d/) 下载，实际上这就是deepin的源

都是deb包，可以直接下载安装。

### 遇到的问题
 - 安装后启动不起来：重启系统。
 - 点击QQ的退出后，QQ在后台并没有真正关闭。这是需要``ps -A |grep QQ``,列出所有QQ程序，然后kill掉就可以了。