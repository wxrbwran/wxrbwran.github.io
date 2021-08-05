---
title: Feign 报错：Request method 'POST' not supported
date: 2021-08-05 10:15:16
tags: spring-cloud
---

> 如果Fegin代理的是get请求，那么**请求参数必须带上@RequestParam 或@RequestBody**

错误：

```java
@FeignClient("dashboard")
public interface DashboardClient {

  @GetMapping("projects/getApiKeyByProjectId")
  String getApiKeyByProjectId(Integer projectId);
}
```

正确：
```java
@FeignClient("dashboard")
public interface DashboardClient {

  @GetMapping("projects/getApiKeyByProjectId")
  String getApiKeyByProjectId(@RequestParam Integer projectId);
}
```