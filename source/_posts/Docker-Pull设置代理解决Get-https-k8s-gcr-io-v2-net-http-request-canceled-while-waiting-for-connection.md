---
title: >-
  Docker Pull设置代理解决Get https://k8s.gcr.io/v2/: net/http: request canceled while
  waiting for connection
date: 2021-11-02 14:10:48
tags: 
  - k8s
  - docker
---
今天搭建k8s集群时，发现一系列k8s.gcr.io的镜像无法pull：
```sh
-<%>- docker pull k8s.gcr.io/kube-proxy
Using default tag: latest
Error response from daemon: Get https://k8s.gcr.io/v2/: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)
```
原来是万恶的g/f....w，需要为docker设置代理，并重启docker daemon：

```sh
sudo mkdir -p /etc/systemd/system/docker.service.d 
sudo touch /etc/systemd/system/docker.service.d/proxy.conf
sudo chmod 777 /etc/systemd/system/docker.service.d/proxy.conf
sudo echo '
[Service]
Environment="HTTP_PROXY=http://proxy.xxx.com:8888/" 
Environment="HTTPS_PROXY=http://proxy.xxx.com:8888/"
' >> /etc/systemd/system/docker.service.d/proxy.conf
sudo systemctl daemon-reload
sudo systemctl restart docker
sudo systemctl restart kubelet

```
