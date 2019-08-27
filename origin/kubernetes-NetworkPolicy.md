# Kubernetes  Network Policy
使用NetworkPolicy需要特定的网络解决方案，如果不启用，即使配置了NetworkPolicy也无济于事。

通过Network Policy，我们不仅能限制哪些Pod能被哪些来源访问，甚至还能控制Pod能访问哪些外部服务。Kubernetes中的Network Policy只定义了规范，并没有提供实现，而是把实现留给了网络插件

# Kubernetes开启Network policy

Calico安装为CNI插件。必须通过传递--network-plugin=cni参数将kubelet配置为使用CNI网络（在kubeadm上，这是默认设置）

网络插件要支持Network Policy，如Calico、Romana、Weave Net和trireme等

# 场景测试

- 限制服务只能被带有特定label的应用访问

    创建namespace，启动一个容器,创建一个service

   ```bash
   kubectl create ns test1
   kubectl -n test1 run nginx --image=nginx
   kubectl -n test1 expose deployment nginx --port=80
   ```

   现在，起一个新应用，访问刚刚创建nginx service
    


- 限制能够访问暴露了公网SLB服务的来源IP
- 限制一个Pod只能访问www.aliyun.com
- 限制Namespace级别的资源访问


# 参考链接

1. https://yq.aliyun.com/articles/640190
2. https://www.jianshu.com/p/c0d2618d2849