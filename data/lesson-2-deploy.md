# 安装与配置

## 安装 kubernetes

1. Master 节点安装部署
  - etcd
  - kube-apiserver
  - kube-controller-manager
  - kube-scheduler
2. Minion 工作节点
  - kubelet
  - kube-proxy

> 额外软件说明

1. etcd: key-value 存储数据库，用来存储 kubernetes 信息,强一致性
2. fannel: CoreOS 针对 Kubernetes 设计的覆盖网络(Overlay Network)

## 配置与启动

> 1. 防火墙设置
firewalld 安全的做法是在防火墙上配置哥哥组件需要互通信的端口号
```bash
# Centos 默认开启防火墙,在安全网络环境下可以关闭防火墙
systemctl disable firewalld
systemctl stop firewalld

# docker 配置
vi /etc/sysconfig/docker
# 增加  gcr.io 来源, 用于 pod 的容器
# OPTIONS='--selinux-enabled=false --log-driver=journald --signature-verification=false --insecure-registry gcr.io'

# 编辑 /etc/kubernetes/apiserver
# 去除 KUBE_ADMISSION_CONTROL中的SecurityContextDeny,ServiceAccount，并重启kube-apiserver服务:

```

>> Note: 默认的 pod container 镜像地址比较慢, 建议使用阿里云镜像

```bash
KUBELET_POD_INFRA_CONTAINER="--pod-infra-container-image=registry.access.redhat.com/rhel7/pod-infrastructure:latest"
# ->
KUBELET_POD_INFRA_CONTAINER="--pod-infra-container-image=registry.aliyuncs.com/archon/pause-amd64:latest"
```

> 2. 启动服务

```shell
# Master
## 安装相关软件
sudo yum install kubernetes-master etcd
sudo systemctl start etcd docker kube-apiserver kube-controller-manager kube-scheduler 


# Minion
## 安装依赖
sudo yum install kubernetes-node etcd flannel -y
sudo systemctl start kubelet kube-proxy
```

> 设置 etcd
[参考](https://www.cnblogs.com/galengao/p/5780938.html)


> 3. Node 工作节点 接入

```shell
# 安装软件
yum install kubernetes-node -y

# 配置相应文件
vi /etc/kubernetes/config
vi /etc/kubernetes/kubelet 

# 启动 node 相关服务
systemctl start kubelet
systemctl start kube-proxy
```

> 4. 网络配置

多个Node 组成的 Kubernetes 集群内, 跨主机的容器网络互通是 kubernetes 集群能够正常工作的前提条件
这里采用 flannel (Overlay Network)实现


```shell
# flannel 依赖 etcd
# 安装 flannel
yum install flannel -y
# 配置相关文件
vi /etc/sysconfig/flanneld
## 注意 FLANNEL_ETCD_KEY


# 为flannel 创建分配网络 只在 master 的 etcd 执行
etcdctl mk /coreos.com/network/config '{"Network": "10.1.0.0/16"}'
# 若要重新建，先删除
etcdctl rm /coreos.com/network/ --recursive

# 启动 flanneld 服务
systemctl restart flanneld
```

测试是否调通?
guesbook 中  redis-master 数据不同步