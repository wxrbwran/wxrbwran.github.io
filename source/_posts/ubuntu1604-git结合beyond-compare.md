---
title: ubuntu1604 git结合beyond compare
date: 2016-10-09 11:29:28
tags:
  - ubuntu
  - git
---
在[beyond compare官网](http://www.scootersoftware.com/download.php)下载Debian选项的安装包，然后
``dpkg -i *.deb``安装依赖等。
在~/.gitconfig文件中增加：
```
[diff]
    tool = bc3
[difftool]
    prompt = false
[merge]
    tool = bc3
[mergetool]
    prompt = false
```
当执行git difftool的时候，就会启动bcompare比较