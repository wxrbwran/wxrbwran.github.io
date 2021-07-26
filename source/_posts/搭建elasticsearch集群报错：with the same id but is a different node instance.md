---
title: 搭建elasticsearch集群报错：with the same id but is a different node instance
date: 2021-07-26 11:53:02
tags: 
    - elasticsearch
    - elasticstash
---

### 记录一下在安装 elastic-search 单机多节点是时候报错解决方案

```bash
[2021-07-26T11:43:03,929][INFO ][o.e.d.z.ZenDiscovery     ] 
[node-2] failed to send join request to master [{node-1}{alKTwZlPRiWECIurrQVWXA}{qwbTz729TNajY6baHiruZA}{172.16.10.25}{172.16.10.25:9300}
{ml.machine_memory=17179869184, ml.max_open_jobs=20, xpack.installed=true, ml.enabled=true}],
reason [RemoteTransportException[[node-1][172.16.10.25:9300][internal:discovery/zen/join]];
nested: IllegalArgumentException
[can't add node {node-2}{alKTwZlPRiWECIurrQVWXA}{8BoLzIHvRsuZLkYCbGd3jA}{172.16.10.25}{172.16.10.25:9301}{ml.machine_memory=17179869184, ml.max_open_jobs=20, xpack.installed=true, ml.enabled=true},
found existing node {node-1}{alKTwZlPRiWECIurrQVWXA}{qwbTz729TNajY6baHiruZA}{172.16.10.25}{172.16.10.25:9300}{ml.machine_memory=17179869184,
xpack.installed=true, ml.max_open_jobs=20, ml.enabled=true}
with the same id but is a different node instance]; ]
```

报错信息提示有相同的 id 但是是不同的实例。

**原因**是： 我把作为 master 的 elastic-search 文件复制了两份，导致数据也包含在了 作为 slave节点里面。

### 解决方案

删除复制的作为 slave 节点的 elastic-search 的data 文件夹即可。
