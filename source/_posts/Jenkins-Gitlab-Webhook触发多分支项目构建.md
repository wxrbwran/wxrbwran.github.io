---
title: 'Jenkins&Gitlab:Webhook触发多分支项目构建'
date: 2021-09-22 17:38:07
tags:
  - jenkins
---

### 项目

在Jenkinsfile中，添加配置：
```grovvy
triggers {
    GenericTrigger (
        // 构建时的标题
        causeString: 'Triggered by $ref',
        // 获取POST参数中的变量，key指的是变量名，通过$ref来访问对应的值，value指的是JSON匹配值（参考Jmeter的JSON提取器）
        // ref指的是推送的分支，格式如：refs/heads/master
        genericVariables: [[key: 'ref', value: '$.ref']],
        // 打印获取的变量的key-value，此处会打印如：ref=refs/heads/master
        printContributedVariables: true,
        // 打印POST传递的参数
        printPostContent: true,
        // regexpFilterExpression与regexpFilterExpression成对使用
        // 当两者相等时，会触发对应分支的构建
        regexpFilterExpression: '^refs/heads/(master|production)$',
        regexpFilterText: '$ref',
        // 与webhook中配置的token参数值一致
        token: 'mytoken'
    )
}
```

### Jenkins

安装插件：Multibranch Scan Webhook Trigger

在多分支项目配置中的Scan Multibranch Pipeline Triggers选择并添加：
<!--more-->
![jenkins](./jenkins_setting.png)

### Gitlab
在gitlab相应项目的配置->集成，webhook，在Url中填入：
![gitlab](./gitlab_webhooks.png)

结束。