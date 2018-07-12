---
title: 将git项目使用推送到多个地址
date: 2018-07-12 11:53:45
tags: git 
---
### git设置
公司开发项目迁移，中间大约一周左右的测试期，两边地址都要上传，那么如何方便快捷的把代码托管到多个平台呢？
例如我有下面两个仓库： 
```
https://gitlab.com/***/doctor.git 
https://github.com/***/doctor.git
```
先添加第一个仓库：
``` 
git remote add origin https://gitlab.com/***/doctor.git 
```
再添加第二个仓库： 
```
git remote set-url --add origin https://github.com/***/doctor.git
```
如果还有其他，则可以像添加第二个一样继续添加其他仓库。

然后使用下面命令提交： 
```
git push origin --all
或者特定分支
git push origin [branch]
```
打开.git/config，可以看到这样的配置：
```
  [remote "origin"]
    url = https://github.com/***/doctor.git
    fetch = +refs/heads/*:refs/remotes/origin/*
    url = https://gitlab.com/***/doctor.git
```
刚才的命令其实就是添加了这些配置。如果不想用命令行，可以直接编辑该文件，添加对应的url即可。
**如果两个地址的账号还不一样呢？**
### 多账号ssh设置
首先生成另一个ssh公钥， 比如id_rsa_doctor,id_rsa_doctor.pub，
然后配置**.ssh**文件夹下的config文件，没有的话建立。
```
Host default
    HostName github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa
#second 第二个账号
Host second
    HostName gitlab.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa_doctor
```
这样的话就可以一次操作后，提交到两个仓库地址了。