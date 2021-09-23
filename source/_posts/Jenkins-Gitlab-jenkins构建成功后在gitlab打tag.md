---
title: 'Jenkins&Gitlab:jenkins构建成功后在gitlab打tag'
date: 2021-09-23 14:18:42
tags: 
  - jenkins
---
### 插件？
首先想找找jenkins有没有合适的插件：
  - git publisher长时间无人维护；
  - git push 并不适合目前的多分支流水线构建。
此路不通。
<!--more-->
## api

那只能走[gitlab-doc](https://docs.gitlab.com/12.10/ee/api/tags.html)这条路了。

1. 创建gitlab token, setting->access tokens,创建个人token，确保有访问构建的项目权限。
   ![token](./gitlab-token.png)

2. jenkins 安装 ``http request``插件，创建**全局凭据**，类型切记选择**secret text**,不要选~~gitlab api token~~.
   ![凭据](./jenkins-cert.png)

3. 在share library ``jenkins-library`` 创建目录src/com/xzlcorp,创建文件gitlab.groovy.
   ```groovy
   package com.xzlcorp

//封装HTTP请求
def HttpReq(reqType,reqUrl,reqBody){
    def gitServer = "https://git.xzlcorp.com/api/v4"
    withCredentials([string(credentialsId: 'gitlab-token', variable: 'gitlabToken')]) {
      result = httpRequest customHeaders: [[maskValue: true, name: 'PRIVATE-TOKEN', value: "${gitlabToken}"]], 
                httpMode: reqType, 
                contentType: "APPLICATION_JSON",
                consoleLogResponseBody: true,
                ignoreSslErrors: true, 
                requestBody: reqBody,
                url: "${gitServer}/${reqUrl}"
                //quiet: true
    }
    return result
}

//创建tag
def CreateTag(projectId, tag, branchName){
    def apiUrl = "projects/${projectId}/repository/tags"
    reqBody = """{"tag_name": "${tag}","ref":"${branchName}", "message": "${branchName}"}"""
    response = HttpReq('POST',apiUrl,reqBody)
    println(response)
}
   ```


4. 在项目的jenkinsfile中加入代码：
  ```groovy
    @Library('jenkins-libs-web@master') _

    def gitlab = new com.xzlcorp.gitlab()
    def tool = new com.xzlcorp.tools()

    String projectName = "project-name"

    pipiline {
      ...
      post {
        always {
          // echo "构建结束"
        }

        success {
          // echo "构建成功"
          script {
            if (env.BRANCH == "master") {
              projectId = gitlab.GetProjectID(projectName)
              tool.PrintMsg("打tag start","blue")
              String tagString = "v${new Date().format("yy.MMdd.HHmm")}"
              gitlab.CreateTag(projectId, tagString, env.BRANCH_NAME)
              tool.PrintMsg("打tag end","blue")
            }
          }
        }

        failure {
          echo "构建失败"
        }

        aborted {
          echo "构建中断"
        }
      }
    }
  ```

