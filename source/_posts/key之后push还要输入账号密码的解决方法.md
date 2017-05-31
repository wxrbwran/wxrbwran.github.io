layout: 
title: github添加ssh-key之后push还要输入账号密码的解决方法
date: 2016-10-08 14:30:09
tags: git
---
> ssh-keygen -t rsa -C "your_email@example.com"将生成的公钥添加后,但是push的时候还是要输入账号密码。

这是因为clone的时候**应该使用ssh的方法**，而现在用的是~~https~~方法。
只需要克隆的时候在github上选择ssh而不是https就可以了(clone的地址下面有)。
如果是已经克隆好的只需要改config就可以
vim .git/config
像下面改url
```
[remote "origin"]
url = git@github.com:wxrbwran/hexo-blog.git
```

