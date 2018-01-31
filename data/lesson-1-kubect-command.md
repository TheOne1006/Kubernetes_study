
# command

1. 关闭防火墙

```
systemctl disable firewalld 
systemctl stop firewalld 
```

2. 安装与启动
```bash
yum install -y etcd kubernetes

# kubernetes 自动依赖安装 docker

sudo systemctl start etcd docker kube-apiserver kube-controller-manager kube-scheduler kubelet kube-proxy
```

```bash
# 查看 ReplicationController
> kubectl get rc
NAME      DESIRED   CURRENT   READY     AGE
mysql     1         1         0         39s
名称       期望      当前       准备       时间
```

> NOTE
>> 第二版 第一章的 Demo, 以及部分印刷 简直差评, 案例都换了 文件名还是 redis-master-xxx.yaml, 如果需要, 自己去docker 改文件。或者使用 第一版本的 案例, 更简单