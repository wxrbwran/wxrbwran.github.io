---
title: 安装ubuntu后要做的几件事
date: 2016-10-08 13:19:10
thumbnail: http://baike.baidu.com/pic/ubuntu/155795/0/024f78f0f736afc31a149928b119ebc4b7451266?fr=lemma&ct=single#aid=0&pic=024f78f0f736afc31a149928b119ebc4b7451266
tags: ubuntu
---
>   这几天自己的笔记本坏了，屏幕闪现，几个按键也失灵了...这次上船的姿势可能不对，哎...

闲话少说，换了公司的备用机，重装ubuntu，之后还有很多事要做，记录在此。
1. 更换安装源。国外的源一是特别慢，第二就是大家都懂得～打开System Setting-> Software & Update,在**Ubuntu Software**tab下"Download from"选择阿里云的源。
2. 挂载NTFS磁盘。现在基本默认自动挂载，但有时也会出错，比如我这次。解决也很简单：
 - 检查ntfs-3g 是否已经安装    locate ntfs-3g  若是有一大堆反馈，那么就是已经安装了，要是没有安装，输入下面代码
    ``sudo apt install ntfs-3g``
 - 挂载的错误。可能一般都是这个吧，除非你一直都不可以访问
    ```sudo ntfsfix /dev/sda5```
   不同的分区，记得更换位置与名称，我的是上图中的sda5
3. 挂载ExFat格式U盘。fat格式不支持~~大文件~~，ntfs或者hfs~~日志格式~~不适合U盘，只好使用exfat咯。支持大文件，并且不是日志式。不过ubuntu默认不支持，需要下载软件支持，一句话解决：
    ``sudo apt install exfat-utils``
4. 安装flash。这个就是纯粹娱乐了，直接去[adobe官网](https://get.adobe.com/flashplayer/?loc=cn),选择APT方式下载就好了。

5. 安装pip3。现在的linux发行版基本都支持python了，人生苦短，我用python~
    ``sudo apt-get install python3-pip``
6. 安装windows字体。好多文件和网页由于字体不全都不怎么好看，那我们直接把windows的字体拷过来就好了。windows字体文件在C:\windows\fonts目录下，cp到/usr/share/fonts目录新建winfonts文件夹中，然后:
 1. mkfontscale
 2. mkfontdir
 3. fc-cache
7. 换ubuntu了，markdown用什么写呢？
    ``sudo apt install retext``
 