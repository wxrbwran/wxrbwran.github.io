---
title: 将具名参数传递给sh
date: 2021-10-20 15:41:39
tags: shell
---

```sh
for ARGUMENT in "$@"
do

    KEY=$(echo $ARGUMENT | cut -f1 -d=)
    VALUE=$(echo $ARGUMENT | cut -f2 -d=)   

    case "$KEY" in
            STEPS)              STEPS=${VALUE} ;;
            REPOSITORY_NAME)    REPOSITORY_NAME=${VALUE} ;;     
            *)   
    esac    


done

echo "STEPS = $STEPS"
echo "REPOSITORY_NAME = $REPOSITORY_NAME"
```
<!--more-->
用法
```sh
bash my_scripts.sh  STEPS="ABC" REPOSITORY_NAME="stackexchange"
```

控制台结果：
```sh
STEPS = ABC
REPOSITORY_NAME = stackexchange
```
准备在脚本中使用**STEPS**和**REPOSITORY_NAME**。

参数的顺序无关紧要。