---
title: osx 安装 mariadb
date: 2020-05-01 17:04:52
tags: mariadb
---

### 安装

```bash
brew install mariadb
```

### 启动/停止
启动/停止 mariadb 有两种方式，按需选择其中一种就可以：

1. 方式一： 通过brew启动/停止，
   这种方法可以跟随系统启动（改plist那些操作麻烦并且会造成不稳定因素）
   启动：brew services start mariadb
   停止：brew services stop mariadb
2. 方式二： 通过mysql.server，这个不能够跟随系统启动，每次重启MAC需要手动运行
   启动：mysql.server start
   停止：mysql.server stop

<!--more-->

###  初始化mariadb(mysql)

1. 初始化数据库，将数据库信息安装上去

  sudo mysql_install_db
  可能有的人会提示已经安装，不管它，跑一次这个。

2. 初始化root账号及数据库各种设定
  这一步很重要，下面会列出所有需要设置的选项，运行：

  sudo mysql_secure_installation
  


**运行之后会进行初始化引导，每一步都有详细的说明。**
**下面列出每个选项的翻译，根据自己需要配置：**

```bash
Enter current password for root (enter for none): <– 初次运行直接回车
…
Set root password? [Y/n] <– 是否设置root用户密码，建议设置，输入Y按回车
…
New password: <– 设置root用户的密码
Re-enter new password: <– 再输入一次你设置的root的密码
…
Remove anonymous users? [Y/n] <– 是否删除匿名用户，建议删除，Y删除，n不删除
…
Disallow root login remotely? [Y/n] <– 是否禁止root远程登录，按需配置，Y禁止远程登录，n开启远程登录
…
Remove test database and access to it? [Y/n] <– 是否删除test数据库，Y删除、n留着
…
Reload privilege tables now? [Y/n]<– 是否重新加载权限表，建议重新加载，Y重新加载，n不重新加载
…
All done! If you’ve completed all of the above steps, your MariaDB
installation should now be secure.
Thanks for using MariaDB!
```

通过这个就可以配置好mariadb。
