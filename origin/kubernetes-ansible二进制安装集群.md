# 使用Ansible安装二进制Kubernetes集群

# 一、Prerequisite

|          | Master 1                                                     | Master 2                                                     | Master 3                                                     | Node1                       |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------- |
| 物理配置 | 2C4G80G                                                      | 2C4G80G                                                      | 2C4G80G                                                      | 8C32G100G                   |
| 集群组件 | etcd1、kube-apiserver、kube-controller-manager、kube-scheduler<br/>docker、kubelet、kube-proxy | etcd1、kube-apiserver、kube-controller-manager、kube-scheduler<br/>docker、kubelet、kube-proxy | etcd1、kube-apiserver、kube-controller-manager、kube-scheduler<br/>docker、kubelet、kube-proxy | docker、kubelet、kube-proxy |
|          |                                                              |                                                              |                                                              |                             |
|          |                                                              |                                                              |                                                              |                             |



# 二、Operation

