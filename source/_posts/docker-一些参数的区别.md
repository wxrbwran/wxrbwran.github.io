---
title: docker 一些参数的区别
date: 2021-10-25 18:03:07
tags: docker
---

# docker 

### ADD 和 COPY 的区别

COPY和ADD命令，在复制普通文件的时候，并没有什么太大的不同，两个命令都可以把本地文件，复制到镜像里。（如果复制的路径不存在，则会自动创建）

ADD 构建镜像和COPY最直观的一点不同，是ADD命令可以直接解压gzip压缩文件，这当我们有很多文件要上传操作的时候，就会变的简单很多。但是表现不一致，可能也不会解压，所以**官方建议使用copy**。

### ENV 和 ARG 的区别
<!--more-->

ARG 和ENV 是经常容易被混淆的两个Dockerfile语法，它们都可以用来设置一个“变量”。
总的来说ARG和ENV有两点不同，第一点是声明变量的作用域不同，第二点是ARG声明后，可以在构建时修改变量。
1. ARG是构建环境 ， ENV可带到镜像中

用交互模式进入到ipconfig-env镜像中，然后输入env可以看到当前镜像的信息。

2. ARG可以在构建镜像时改变变量值

在构建时，可以使用—build-arg 参数来更改变量的值，比如现在要把变量VERSION的值进行修改,就可以使用下面的命令。
```sh
docker image build -f Dockerfile.ARG -t ipinfo-arg-2.0.0 --build-arg VERSION=2.0.0 .
```
这时候我们再使用交互模式，开启ipinfo-arg-2.0.0容器。
```sh
docker container run -it ipinfo-arg-2.0.0 
```
然后再通过shell命令，ipinfo verison查看ipinfo的版本，可以看到版本已经变成了2.0.0了。

### CMD 和 ENTRYPOINT 的区别

ENTRYPOINT 命令很容易和 CMD命令混淆。ENTRYPOINT也可以设置容器启动时要执行的命令。

CMD设置的命令，可以在 docker container run 时传入其它命令，覆盖掉 CMD 的命令，但是ENTRYPOINT所设置的命令时一定会被执行的。
ENTRYPOINT 和 CMD 可以联合使用， ENTRYPOINT 设置执行的命令，CMD传递参数。
[镜像区别测试](http://jspang.com/detailed?id=75#toc375)
