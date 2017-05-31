---
title: 使用nvm安装node
date: 2016-10-10 11:08:25
thumbnail: https://nodejs.org/static/images/logo-header.png
tags: 
  - ubuntu
  - node
---
如果你想长期做 node 开发, 或者想快速更新 node 版本, 或者想快速切换 node 版本,
那么在非 Windows(如 osx, linux) 环境下, 请使用 nvm 来安装你的 node 开发环境, 保持系统的干净.
如果你使用 Windows 做开发, 那么你可以使用 nvmw 来替代 nvm
### git clone nvm

直接从 github clone nvm 到本地, 这里假设大家都使用 ~/git 目录存放 git 项目:
``
$ cd ~/git
$ git clone https://github.com/creationix/nvm.git
``

配置终端启动时自动执行 source ~/git/nvm/nvm.sh,
在 ~/.zshrc 文件添加以下命令:
``source ~/git/nvm/nvm.sh``
重新打开你的终端, 输入 nvm
```
$ nvm
Node Version Manager
...
```

### 通过 nvm 安装任意版本的 node

nvm 默认是从 http://nodejs.org/dist/ 下载的, 国外服务器, 必然很慢,
好在 nvm 以及支持从镜像服务器下载包, 于是我们可以方便地从七牛的 node dist 镜像下载:
```
$ NVM_NODEJS_ORG_MIRROR=https://npm.taobao.org/mirrors/node nvm ls-remote
```
就可以看到远程服务器上的node版本，选择一个进行安装``nvm install v4.6.0(nodevesion)``即可。
于是你就会看到一段非常快速进度条:
```
######################################################################## 100.0%
Now using node v4.6.0
```
如果你不想每次都输入环境变量 NVM_NODEJS_ORG_MIRROR, 那么我建议你加入到 .zshrc 文件中:
```
# nvm
export NVM_NODEJS_ORG_MIRROR=https://npm.taobao.org/mirrors/node
source ~/git/nvm/nvm.sh
```
然后你可以继续非常方便地安装各个版本的 node 了, 你可以查看一下你当前已经安装的版本:

$ nvm ls
         nvm
     v0.8.26
    v0.10.26
    v0.11.11
->  v4.3.2