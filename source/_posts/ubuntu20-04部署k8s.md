---
title: ubuntu20.04部署k8s
date: 2021-10-28 18:24:22
tags: k8s
---

## 前置步骤
### 禁止swap分区
K8s的要求，在每个宿主机上执行：
```sh
sudo swapoff -a
```
#修改/etc/fstab，注释掉swap那行，持久化生效
```sh
sudo vi /etc/fstab
```
重启。

### 确保sysctl配置中net.bridge.bridge-nf-call-iptables的值设置为了1
<!--more-->
在Ubuntu 20.04 Server上，这个值就是1。如果你的系统上不一致，使用下面的命令来修改：
```sh
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sudo sysctl --system
```

### 安装docker，具体过程略。
   修改docker Cgroup Driver为systemd
```sh
cat <<EOF | sudo tee /etc/docker/daemon.json

{
"exec-opts":["native.cgroupdriver=systemd"]
}

EOF

sudo systemctl restart docker.service
```
### 节点上安装  kubectl kubelet kubeadm

添加阿里云k8s源

```sh
curl -fsSL https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | sudo apt-key add -

sudo vim /etc/apt/sources.list

deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main

sudo apt-get update

sudo apt-get -y install kubectl kubelet kubeadm

sudo apt-mark hold kubelet kubeadm kubectl
```
### 初始化master
注意，这里使用了阿里云的镜像，然后使用了flanel（法兰绒）网络
```sh
sudo kubeadm init --image-repository registry.aliyuncs.com/google_containers --apiserver-advertise-address="其他节点可连接的ip地址" --pod-network-cidr=10.244.0.0/16
```



上面的命令执行成功后，会输出一条设置命令和kubeadm join相关的命令，后面加入worker node的时候要使用。
```sh
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
### 安装pod网络插件（cni） 法兰绒网络
```sh
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

kubeadm join ip:6443 --token xxx \
--discovery-token-ca-cert-hash sha256:yyy
```

## 遇到的问题
1. Ubuntu20.04无法启动kubelet的问题 ：[Ubuntu20.04无法启动kubelet的问题](https://www.coder4.com/archives/7344)
2. kubeadm init 命令 端口被占用的问题：使用kubeadm reset 重置
3. 设置网络后，节点一直是NotReady:
  ‒ ```kubectl describe node "nodename"```
  ‒ Container runtime network not ready: NetworkReady=false reason:NetworkPluginNotReady message:docker: network plugin is not ready: cni config uninitialized
  ‒ 出现这个错误提示信息已经很明显,网络插件没有准备好.我们可以执行命令docker images|grep flannel来查看flannel镜像是否已经成功拉取下来.经过排查,flannel镜像拉取的有点慢,稍等一会以后就ok了.