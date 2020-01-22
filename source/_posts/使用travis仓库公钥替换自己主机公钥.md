---
title: 使用travis仓库公钥替换自己主机公钥
date: 2020-01-22 15:33:01
tags: travis
---
> 每次重装完电脑，自己本机的密钥就没了，在去配置blog的密钥登陆也是一大麻烦，当然也可以重装前备份，那么有没有别的办法，不用每次这么麻烦呢？

[看这里](https://docs.travis-ci.com/user/encryption-keys#fetching-the-public-key-for-your-repository)，可以获得仓库的公钥，然后将其添加到 authorized_keys 即可。

### 获取仓库公钥
```bash
travis pubkey -r wxrbwran/wxrbwran.github.io
```

输出如下。
```bash
Public key for wxrbwran/wxrbwran.github.io

ssh-rsa ......
```

然后我分别加入到github中和腾讯云主机中，这样travis就可以自主登录操作了。