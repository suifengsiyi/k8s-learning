# K8S 架构

k8s作为微服务治理平台，有一个master（管理者）和多个node（被管理者）

**参考**

- [知乎-k8s架构解析](https://zhuanlan.zhihu.com/p/96908130)
- [博客园-k8s组成和资源对象简介](https://blog.frognew.com/2017/04/kubernetes-overview.html)

---

## Master节点的组件

### kube-apiserver

核心功能是对核心对象（例：Pod, Service, RC）的crud操作，集群内模块之间数据交互的枢纽
从上到下分为四层：
API层： restful
访问控制层：
注册表层：选择访问的资源对象，k8s把所有资源对象保存在注册表（Registry）中
etcd数据库：保存创建副本的信息，用来持久化k8s资源对象的key-value数据库

### kube-controller-manager

运维自动化核心，分为8个controller，对不同资源的监控和管理
副本 Replication Controller
节点 Node Controller
资源 ResourceQuota Controller
命名空间 Namespace Controller
服务 Service Controller
    ServiceAccount Controller
    Token Controller
    Endpoint Controller

### kube-scheduler

将待调度的pod按照调度算法和策略绑定到node，绑定信息写入etcd。关注：待调度pod，可用node，调度算法和策略

## Node节点的组件

### kubelet

主要负责本节点上pod的生命周期管理，定期像Master上报节点及pod信息。从apiserver接收pod的创建、启动、停止
通过APIServer监控到Scheduler产生的pod绑定时间，通过pod描述装载镜像文件，启动容器。同时管理pod。在APIServer注册node信息，定期向master汇报node信息，通过cAdvisor监控容器和节点资源，将采集的数据通过restapi暴露给其他资源
cAdvisor 分析资源使用率和性能的工具，

### kube-proxy

kube-proxy支持三种代理模式: userspace,iptables, IPVS；它们各自的操作略有不同。
负责反向代理和负载均衡的实施，在每个node上运行，看作负载均衡器，核心功能是将Service的请求转发到后端的多个pod上
cluster ip和node port是kube-proxy通过iptables的nat转换实现的。kube-proxy在运行过程中动态创建与Service相关的iptables规则
内部访问：cluster ip
外部访问： node port
公有云：LoadBalancer，得到external-ip，通过annotations的service.kubernetes.io/qcloud-loadbalancer-internal-subnetid: subnet-4c8fd5uf
映射到dns名称：ExternalName，说明：您需要 CoreDNS 1.7 或更高版本才能使用ExternalName类型。

---

## 举例
部署mysql
编写rc.yaml，kubectl create -f rc.yaml
k8s内部逻辑：
kubectl 
