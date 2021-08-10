---
title: 单点kafka的安装配置
date: 2021-08-10 14:28:06
tags: kafka
---


### 下载及安装

  > kafka依赖zookeeper，所以也需下载zookeeper。

  去[zookeeper](http://zookeeper.apache.org/)下载相应的版本并配置Path，略过。

  去[kafka官网](http://archive.apache.org/dist/kafka/)下载相应的版本并配置Path，略过。

### 配置zookeeper

  1. 将zoo_sample.cfg复制一份，重命名为zoo.cfg.
  2. 配置 dataDir=/Users/wuxiaoran/env/bin/apache-zookeeper-3.6.3-bin/data
   
### 配置kafka
  1. 打开server.properties
  2.  broker.id=0
      port=9092
      host.name=localhost
      advertised.host.name=localhost
      log.dirs=/Users/wuxiaoran/logs/kafka_2.12
<!--more-->
## 启动
  1. 启动zookeeper
      ```bash
      zkServer.sh start
      ```
  2. 启动kafka
      ```bash
      ./kafka-server-start.sh  -daemon /Users/wuxiaoran/env/bin/kafka_2.13-2.8.0/config/server.properties
      ```

### kafka常用命令

  1. 创建topic， 分区为1，副本为1

    ```bash
    kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
    ```
  2. 查看topic

    ```bash
    kafka-topics.sh --bootstrap-server localhost:9092 --list
    ```
  3. 删除topic

    ```bash
    kafka-topics.sh --zookeeper localhost:2181 --delete --topic test
    ```
  4. 查看指定topic详细信息
    ```bash
    kafka-topics.sh --zookeeper localhost:2181 --describe --topic test
    ```
  5. 查看所有topic详细信息
    ```bash
    kafka-topics.sh --zookeeper localhost:2181 --describe
    ```
  6. 查看指定分组的详细信息
    ```bash
        kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group group02
    ```