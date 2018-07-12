---
title: hexo＋Travis-ci＋github构建自动化博客
date: 2017-06-01 10:08:52
tags: 
  - ci 
  - hexo
---
> Travis CI：是目前新兴的开源持续集成构建项目，它与jenkins，GO的很明显的特别在于采用yaml格式，简洁清晰。

tarvis－ci是远端服务器构建，跟jenkins 不一样，不用自己搭建一个jenkins服务器。tarvis－ci是用的远端官方提供的服务器。用hexo 搭建博客，然后将博客源码提交到 Git上，用travis－ci 编译git 上的源码，然后将编译后的文件，推送到Github.io仓库下，达到自动化编译构建。

----

#### 构建自动化博客的步骤

由于需要搭建hexo 和本地搭建Travis 所以本地需要有ruby 和 node 环境。本地搭建Travis 是为了和Travis－ci 服务器进行加密解密获得密匙，获得一次即可。

1. 安装rubu 和node.js

2. 搭建hexo

3. 安装 travis

4. 配置git 的ssh key

5. 配置Travis

6. 验证

---

<!--more-->

### 安装ruby和node
略
### 搭建hexo
搭建过程略
注意的是：博客搭建成功后在github 的your_blog_name.github.io 仓库下新建travis分支，并将博客源码（未编译过的代码）提交到该分支，用于以后**Travis-ci编译**使用。
### 安装 travis
```
gem install travis
```
### 配置git 的ssh key
将本机ssh_key 添加github中。
### 配置Travis
1. 进入travis官网Travis 官网传送门用github账号登陆Travis CI
![CI](http://oqum9wezj.bkt.clouddn.com/WX20170601-110251.png)
2. 在右上角你的账户名点击进入 account，在Repositories tab页点击Sync now同步你的github项目。选中项目将默认的off改变为on开启项目的持续集成。
![sync_ci](http://oqum9wezj.bkt.clouddn.com/sync_ci.png)

3. 在你项目的根目录建立一个.travis文件夹
```
 $ cd 博客项目文件夹根目录
 $ mkdir .travis
```

4.  复制id_rsa 和 创建 ssh_config文件
```
    $ cd 博客项目文件夹根目录/.travis
    $ cp ~/.ssh/id_rsa ./
```

5.  创建 ssh_config 文件
```
$ cd 博客项目文件夹根目录/.travis
$ touch ssh_config
```
编辑 ssh_config ,输入以下信息
> Host github.com
User git
StrictHostKeyChecking no
IdentityFile ~/.ssh/id_rsa
IdentitiesOnly yes

6. travis 登陆
```
$ cd 博客项目文件夹根目录/.travis
$ travis login --auto
```
需要填写github的用户名和密码。

7. 加密操作
在博客项目文件夹下对ssh的私钥进行加密
```
$ cd 博客项目文件夹根目录/.travis
$ travis encrypt-file id_rsa --add
```
此操作会生成加密之后的秘钥文件 id_rsa.enc，删除id_rsa密钥文件(私钥不能随便泄露)。
你的travis 目前上已经出现了下面加密解密key : 
(点击第2步图中的仓库名可进入下图界面，点击setting可看到加密)
![enc_code](http://oqum9wezj.bkt.clouddn.com/enc.png)

8. 在你项目的根目录建立一个.travis.yml文件，内容为： 
此步是在我your_blog_name.github.io仓库的travis 分支下编译源码。 
（用第7步中Travis 生成的 encrypted_xxxxxxxxx_key -iv 和encrypted_xxxxxxxxx_iv 替换travis.yml 中openssl的 key 和 -iv）

``` yml
language: node_js
node_js:
- '7'
branches:
  only:
  - travis
cache:
  directories:
  - node_modules
before_install:
- openssl aes-256-cbc -K $encrypted_xxxxxxxxx_key -iv $encrypted_xxxxxxxxx_iv
  -in .travis/id_rsa.enc -out ~/.ssh/id_rsa -d
- chmod 600 ~/.ssh/id_rsa
- eval $(ssh-agent)
- ssh-add ~/.ssh/id_rsa
- cp .travis/ssh_config ~/.ssh/config
- git config --global user.name "527515025"
- git config --global user.email "527515025@qq.com"
install:
- npm install hexo-cli -g
- npm install hexo-deployer-git --save
- npm install
script:
- hexo clean
- hexo g

after_success:
- hexo deploy
```

在travis分支中，操作一下，push到分支上就可以自动构建并发布了~






