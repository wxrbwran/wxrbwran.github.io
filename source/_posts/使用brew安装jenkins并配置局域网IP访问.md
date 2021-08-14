---
title: 使用brew安装jenkins并配置局域网IP访问
date: 2021-08-14 09:33:39
tags: jenkins
---

### 安装
使用brew安装jenkins
brew install jenkins-lts

### 常用命令
启动，停止，重启jenkins

```bash
brew services start jenkins-lts
brew services stop jenkins-lts
brew services restart jenkins-lts
```

### 局域网使用IP访问jenkins

使用brew安装jenkins会避免很多其他安装方式产生的用户权限问题，但是会将httpListenAddress默认设置为127.0.0.1，这样我们虽然可以在本地用localhost:8080访问，但是本机和局域网均无法用ip访问。解决办法为修改两个路径下的plist配置。
～/Library/LaunchAgents/homebrew.mxcl.jenkins.plist
/usr/local/opt/jenkins/homebrew.mxcl.jenkins.plist
将上面两个plist中的httpListenAddress后的ip地址，修改为本机IP或者0.0.0.0即可。

上述两个配置文件修改完后，重启Jenkins服务；

