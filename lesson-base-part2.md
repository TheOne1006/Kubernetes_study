# kubernetes 基本概念 part 2

## Deployment(部署)

Kubernetes 1.2 引入的新概念, __目的是更好的解决 Pod 的编排问题__.
因此 Deployment 内部使用 Replica Set 来实现目的, RC 的一次升级

功能:
1. 可以随时知道当前 Pod 部署的进度。

典型的使用场景:
1. 创建 Deployment 对象来生成对应的 ReplicaSet 并完成 Pod 副本的创建过程
2. 检查 Deployment 的状态来看部署动作是否完成
3. 更新 Deployment 以创建新的 Pod(比如镜像升级)
4. 回滚: 当前 Deployment 不稳定，则会滚到上一个版本
5. 挂起、回复一个 Deployment

类似的定义: Deployment 定义 与 Replica Set 定义

```yaml
# Deployment
apiVersion: extensions/v1bate1
kind: Deployment
metadata:
  name: nginx-deplyment

# Replica Set
apiVersion: v1
kind: ReplicaSet
metadata:
  name: nginx-repset
```

## Horizontal Pod Autoscaler (HPA)

Pod 横向自动扩容, 与之前的 RC, Deployment 一样, 也属于一种 Kubernetes 资源对象。 通过追踪分析 RC 控制的所有目标 Pod 的负载变化，来确定是否需要针对性的调整 Pod 的副本数。  

> 当前 HPA 可以有一下两种范式作为 Pod 负载的度量指标。  

- CPUUtilizationPercentage。
  - 算数平均值， 所有目标Pod副本的自身cpu 平均使用量
  - 通常是1分钟内的平均值
  - 目前通过查询 Heapster 扩展来得到这个值, 所以需要部署 Heapster, 未来计划由 Kubernetes 自身实现一个基础性能采集模块。
- 程序自定义度量指标,比如服务在每秒内的相应请求数(TPS/QPS)


> 历史

- Kubernetes 1.1 实现这一特性
- 1.2 升级为稳定八本 ( apiVersion: autoscaling/v1 )

## Service (服务)

Service 也是 Kubernetes 最核心的资源对象之一, Kubernetes 里每个 Service 其实就是我们经常提起的微服务架构中的一个 "微服务", 之前所说的
Pod, RC 等资源其实就是__为__这个 "服务" -- Kubernetes Sercice

![](./images/service-pod-rc.jpeg)

- Service 定义了一个服务的访问入口地址
- 通过改 Service 与其 Pod 集群通信

> 如何访问?

既然每个Pod 都会被分配到一个单独的IP地址, 而每个 Pod 都提供了一个独立的 Endpoint( Pod IP + ContainerPort )已被 客户端访问。  
现在有多个 Pod 副本组成集群，那么客户端如何访问?

一般的做饭是部署一个负载均衡(软件或者硬件)， 为这组 Pod 开启一个对外的服务端口如 8000 端, 并将这些 Pod 的 Endpoint 列表加入 8000 的转发列表中。

Kubernetes 也遵循上述常规做法，运行在每个 Node 的 kube-proxy 进程其实就是一个智能软件负载均衡器,   
它负责把对 Service 的请求转发到后端的某个 Pod 实例上， 并在实现服务器的负载均衡与绘画保持机制。

> 巧妙又影响深远的设计: Service 共用一个负载均衡器的IP 地址, 而是每个 Service 分配一个全局唯一的虚拟 IP 地址, 这个虚拟IP 成为 ClusterIP (集群IP)。

这样每个服务就变成一个具备唯一IP地址的 "通信节点", 服务调用就变成最基础的 TCP 网络通信。


