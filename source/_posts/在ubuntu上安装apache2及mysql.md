---
title: 在ubuntu上安装apache2及mysql
date: 2016-10-09 15:12:20
tags: ubuntu
---
## 安装Apache
``sudo apt-get install apache2``
Apache安装完成后，默认的网站根目录是"/var/www/html"，在终端窗口中输入``ls /var/www/html``,在网站根目录下有一个"index.html"文件,在浏览器中输入"localhost",就可以打开该页面。
##### 修改网站的根目录
1. 在终端窗口中输入``sudo vi /etc/apache2/apache2.conf``,找到**<Directory /var/www/>**的位置,更改"/var/www/"为新的根目录"/var/Sites/"就可以了。
2. 在终端窗口中输入``sudo vi /etc/apache2/sites-available/000-default.conf``,**DocumentRoot /var/www/html**的位置,更改"/var/www/html"为新的根目录就可以了，这里我把它更改为"/var/Sites/"。

## 安装mysql
``sudo apt-get install mysql-server``