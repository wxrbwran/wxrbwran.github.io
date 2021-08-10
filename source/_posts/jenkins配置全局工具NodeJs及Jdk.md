---
title: jenkins配置全局工具NodeJs及Jdk,Maven
date: 2021-08-10 22:42:56
tags: jenkins
---

### 添加nodejs插件
点击 **系统管理** > **插件管理** > **可选插件** 搜索NodeJs并安装

### 添加全局工具

点击 **系统管理** > **全局工具配置**,此时应该就会出现node配置
<!--more-->
![全局工具配置](https://pic.imgdb.cn/item/611292f25132923bf802f35d.png)

需要注意的是，安装时需在本机先配置好node及jdk环境，因为我们使用的是本地环境，如下图

![jdk](https://z3.ax1x.com/2021/08/10/ft2smQ.png)
![maven](https://z3.ax1x.com/2021/08/10/ftWYGt.png)
![nodejs](https://z3.ax1x.com/2021/08/10/ft25XF.png)

### pipiline验证
配置流水线代码，输出各工具版本号

```groovy
pipeline {
    agent { node { label "build" } }

    environment {
        CC = "clang"
    }
    
    tools {
        nodejs "NodeJs_14"
        jdk "Jdk_8"
        maven "Maven"
    }
    options {
        timeout (time: 1, unit: "HOURS")
    }

    stages {
        stage('Hello') {
            steps {
                echo "Hello World ${CC}"
                sh "node -v"
                sh "java -version"
                sh "mvn -version"
            }
        }
    }

    post {
        always {
            echo "Hello World always ${CC}"
        }

        success {
            echo 'Hello World success'
        }

        failure {
            echo 'Hello World failure'
        }

        aborted {
            echo 'Hello World abroted'
        }
    }
}

```

完成后保存，并点击构建，console如下：
![console](https://z3.ax1x.com/2021/08/10/ftWfLF.png)

配置成功。

