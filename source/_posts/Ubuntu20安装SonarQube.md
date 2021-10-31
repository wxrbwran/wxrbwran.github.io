---
title: Ubuntu20安装SonarQube
date: 2021-10-31 22:49:08
tags: 
  - ubuntu
  - sonarqube
---
> SonarQube是一个基于web的开源工具。用来管理代码质量和代码分析。还可以跟踪统计数据并创建图表，使开发人员能够快速识别代码中的问题。

### 先决条件
1. Ubuntu 20.04 LTS，至少2GB内存和1核CPU
2. PostgreSQL 版本 9.3或更高
3. 使用sudo权限访问ssh
4. 开放端口9000
<!--more-->

5. 增加vm.max_map_count内核...执行下面四行命令
```
sudo sysctl -w vm.max_map_count=262144

sudo sysctl -w fs.file-max=65536

ulimit -n 65536

ulimit -u 4096
```

6. 打开文件/etc/security/limits.conf并插入下面两行内容
sudo vim /etc/security/limits.conf
插入内容
sonarqube   -   nofile   65536
sonarqube   -   nproc    4096

### 在安装之前先更新系统，执行下面的命令
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install wget unzip -y

### 安装jdk11 略（sdkman）

### 安装和配置PostgreSQL10数据库
添加和下载PostgreSQL
```sh
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'

wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add -
```
安装数据库
```sh
sudo apt-get -y install postgresql postgresql-contrib
```
开启数据库服务
```sh
sudo systemctl start postgresql
```
设置开机自启数据库
```sh
sudo systemctl enable postgresql
```
修改PostgreSQL默认用户密码
```sh
sudo passwd postgres
```
切换到postgres用户
```sh
su - postgres
```
创建一个新用户sonar
```sh
createuser sonar
```
进入PostgreSQL shell
```
psql
```

为新建的用户sonar设置一个密码
```
ALTER USER sonar WITH ENCRYPTED password 'sonar';
```
创建一个新的数据库
```
CREATE DATABASE sonarqube OWNER sonar;
```
授予数据库sonarqube的所有权限给sonar用户
```
grant all privileges on DATABASE sonarqube to sonar;
```
退出psql shell
\q

回到sudo权限用户
exit

### 在Ubuntu安装SonarQube
下载sonaqube安装文件 [最新版地址](https://www.sonarqube.org/downloads/)

cd /tmp
```sh
sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.1.0.zip
```

将下载的文件解压到/opt目录
sudo unzip sonarqube-7.9.3.zip -d /opt
移动解压的文件到/opt/sonarqube
sudo mv /opt/sonarqube-7.9.3 /opt/sonarqube

配置SonarQube
创建组sonar
sudo groupadd sonar

添加用户访问权限
sudo useradd -c "user to run SonarQube" -d /opt/sonarqube -g sonar sonar

sudo chown sonar:sonar /opt/sonarqube -R

打开SonarQube配置文件

添加下面三行数据（数据库用户名和密码以及数据库连接地址）
sonar.jdbc.username=sonar

sonar.jdbc.password=sonar

sonar.jdbc.url=jdbc:postgresql://yourip:5432/sonarqube

编辑sonar脚本文件并设置RUN_AS_USER（第一行为编辑文件，第二行为文件设置内容），编辑完成后按CTRL+X来保存和关闭文件
sudo nano /opt/sonarqube/bin/linux-x86-64/sonar.sh

RUN_AS_USER=sonar

切换到sonar用户
sudo su sonar

进入到脚本目录
cd /opt/sonarqube/bin/linux-x86-64/

运行脚本来启动SonarQube
./sonar.sh start

### 配置Systemd服务
首先停止SonarQube
cd /opt/sonarqube/bin/linux-x86-64/

./sonar.sh stop

创建systemd服务文件，并添加如下内容
sudo nano /etc/systemd/system/sonar.service

内容
```
[Unit]

Description=SonarQube service

After=syslog.target network.target

 

[Service]

Type=forking

 

ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start

ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop

 

User=sonar

Group=sonar

Restart=always

 

LimitNOFILE=65536

LimitNPROC=4096

 

[Install]

WantedBy=multi-user.target
```

现在可以使用systemctl来管理服务了
- 开启服务

sudo systemctl start sonar

- 开机启动服务

sudo systemctl enable sonar

- 查看运行状态

sudo systemctl status sonar

访问SonarQube：打开浏览器，在地址栏输入 http://localhost:9000 或将localhost换成ip地址，
默认用户名和密码都是admin

