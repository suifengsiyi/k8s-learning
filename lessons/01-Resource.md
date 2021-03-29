# K8S 资源对象

k8s的资源控制是一种声明+引擎的理念，我们对集群中的某种资源对象作出声明，k8s的自动化资源控制系统会一直尝试将该资源对象维持在我们声明的状态下。

**参考**

- [理解k8s对象](https://kubernetes.io/zh/docs/concepts/overview/working-with-objects/kubernetes-objects/)
- [博客园-k8s组成和资源对象简介](https://blog.frognew.com/2017/04/kubernetes-overview.html)

## Pod(Po)

基本管理单元，最小调度单位
一个pod中可以运行多个容器（container）
同一个命名空间下的不同pod共享资源

## ReplicaSet(RS)

随着k8s的高速发展，官方已经推荐我们使用RS和Deployment来代替RC(ReplicationController)了。
实际上RS和RC的功能基本一致，目前唯一区别就是：RC只支持基于等式的selector（env=dev或environment!=qa），但RS还支持基于集合的selector（version in (v1.0, v2.0)）
在一般情况下，我们推荐使用Deployment而不直接使用ReplicaSet

## Deployment

表示部署，支持滚动升级
在部署Pod的时候会创建一个ReplicaSet，来控制Pod实例的数量
监视当前资源的状态，达到预期状态
Deployment控制器实际操纵的，就是Replicas对象，而不是Pod对象

## Service

微服务
每个Service分配一个固定不变的虚拟IP即ClusterIP
一个Service后边可以有一个或多个Pod
Service将客户端请求转发到后边的某个Pod上，实现负载均衡

## Label

Label是联系各种k8s资源的纽带
一个Service通过Label关联到后端的Pod上
Service定义一个Pod的label选择器，具备这个Label的Pod就会为此Service效力

## Namespace

用于实现多租户的逻辑隔离

## Ingress

将集群外部流量接入到集群内

## Volume


## PV/PVC

持久卷和持久卷请求提供集群的持久存储抽象

## DaemonSet

在集群中的每个Node上启动一个守护进程Pod

## Job/Schedued Job


## ConfigMap/Secret

集中配置中心

## Horizontal Pod Autoscaler

根据负载实现Pod自动弹性伸缩 
