# Kubernetes或Openshift的Prometheus监控体系

# 一、Overview

![](/assets/Openshift或Kubernetes的Prometheus监控体系-1.png)


- **cluster-monitoring-operator** ：负责在 OpenShfit 环境中部署基于 Prometheus 的监控系统
    - GIthub：https://github.com/openshift/cluster-monitoring-operator
    - 部署基于 Prometheus 监控系统中的组件
        - Prometheus Operator
        - Prometheus
        - Alertmanager cluster for cluster and application level alerting
        - kube-state-metrics
        - node_exporter
- **prometheus operator**：负责配置、管理Prometheus和Alertmanager 
    - GIthub： https://github.com/coreos/prometheus-operator
    - 相关博客：https://blog.csdn.net/ygqygq2/article/details/83655552
    - 功能：
        - Create/Destroy: 在Kubernetes namespace中更容易启动一个Prometheus实例，一个特定的应用程序或团队更容易使用Operator。
        - Simple Configuration: 配置Prometheus的基础东西，比如在Kubernetes的本地资源versions, persistence, retention policies, 和replicas。
        - Target Services via Labels: 基于常见的Kubernetes label查询，自动生成监控target 配置；不需要学习普罗米修斯特定的配置语言。
    - 架构

    ![](/assets/Openshift或Kubernetes的Prometheus监控体系-2.png)
- **kube-state-metrics**：监听 Kubernetes API server 并自动生成相关对象的metrics信息(并不修改相关对象的配置)，在80端口(默认)暴露出HTTP的endpoint /metric
    - GIthub：https://github.com/kubernetes/kube-state-metrics
- **node-exporter**：以Daemonset的形式部署在Openshift集群的各个节点上，采集OS级别的metrics信息
- **监控的Target**
  - Prometheus itself
  - Prometheus-Operator
  - cluster-monitoring-operator
  - Alertmanager cluster instances
  - Kubernetes apiserver
  - kubelets (the kubelet embeds cAdvisor for per container metrics)
  - kube-controllers
  - kube-state-metrics
  - node-exporter
  - etcd (if etcd monitoring is enabled) 

**Note:**
1. Prometheus Pod 中，除了 Prometheus 容器外，还有一个 prometheus-config-reloader 容器。它负责导入在需要的时候让Prometheus 重新加载配置文件。
    ![](/assets/Openshift或Kubernetes的Prometheus监控体系-3.png)
    ![](/assets/Openshift或Kubernetes的Prometheus监控体系-4.png)
2. 配置文件被以 Secret 形式创建并挂载给 prometheus-config-reloader Pod。一旦配置有变化，它会调用 Prometheus 的接口，使其重新加载配置文件。
    ![](/assets/Openshift或Kubernetes的Prometheus监控体系-5.png)



# 相关链接

1. https://docs.okd.io/3.11/install_config/prometheus_cluster_monitoring.html#prometheus-cluster-monitoring
2. http://www.cnblogs.com/sammyliu/p/10155442.html