---
title: 使用SSH登录远程服务器
date: 2018-07-31 09:58:11
tags: ssh
---

一般使用ssh访问远程服务器的命令是：
```
ssh username@ip地址
```
例如：ssh root@112.232.12.12，然后输入密码即可访问。
<--more-->
### 以Mac OS为例，别名登录

使用cd ~/.ssh/ 进入ssh目录，会看到有config、known_hosts两个文件，一般的没有设置的话，只会有known_hosts，可以使用touch config命令创建config文件。

使用vim编辑config文件，添加如下内容：
```
Host txy
HostName 1xx.1xx.1xx.1
User root
IdentitiesOnly yes
```
其中： 
1、Host 后边的是别名，以后可以根据这个名称进行访问； 
2、HostName是你的主机IP地址； 
3、User为主机登录的用户名； 
4、IdentitiesOnly yes固有配置；

设置完成，后期的话使用:ssh txy 然后输入密码，就可以登录到远程主机啦！

### 密钥登录

每次登录SH都需要输入密码很麻烦，而且可能不太安全。SSH还能使用另外一种登录方式，也就是使用密钥登录。这种登录方式需要客户端生成一堆公钥私钥对，然后将公钥添加到服务器中，这样下次就可以直接登录了。

首先生成SSH密钥，依照提示输入信息即可。默认生成在用户主目录中的.ssh文件夹中。带pub的是公钥，接下来需要添加到服务器中。

然后登陆服务器，打开.ssh文件夹，在authorized_keys中添加到对应公钥。然后退出SSH重新登陆试试，成功的话不需要输入密码就会直接进入远程服务器。


