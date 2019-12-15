# Summary

* [简介](README.md)

## Part Ⅰ - 容器云Openshift
* 安装
    * [Allinone](origin/openshift-allinone安装.md)
    * 集群
* 基础知识
    * POD
    * Service
    * PersistentVolumeClaim
    * PersistentVolume
    * DeploymentConfig
    * StatefulSet
    * Daemonset
    * ServiceAccount
* 集群管理
    * [数据持久化](origin/openshift-Kubernetes的持久化存储.md)
        * [NFS Client provisioner](origin/openshift-Kubernetes-provisioner-nfs-client.md)
        * [NFS Server Provisioner](origin/openshift-Kubernetes-provisioner-nfs-server.md)
        * [Glusterfs Provisioner](origin/openshift-Kubernetes-provisioner-glusterfs.md)
        * [Ceph FileSystem Provisioner](origin/openshift-Kubernetes-provisioner-cephfs.md)
        * [Ceph RBD Provisioner](origin/openshift-Kubernetes-provisioner-cephrbd.md)
    * 管理
        * 资源对象管理
            * [常见资源对象操作](origin/openshift-资源对象常见操作.md)
            * [将Secret和ConfigMap以文件的形式挂载到容器](origin/openshift-将Secret和ConfigMap以文件的形式挂载到容器.md)
        * 集群管理
            * [节点管理](origin/openshift-集群节点管理.md)
            * [节点状态监控](origin/openshift-使用Cockpit监控集群节点的系统状态.md)
            * [集群组件TLS证书管理](origin/openshift-集群组件TLS证书管理.md)
            * [定制WebConsole界面](origin/openshift-WebConsole定制化.md)
            * [集群管理遇到的问题](origin/openshift-no-IP-addresses-available-in-range-set解决方案.md)
            * [用户认证](origin/openshift-openshift的用户认证.md)
            * [用户权限管理实例](origin/openshift-openshift用户权限管理实例.md)
    * 网络
        * [openshift开启router的haproxy-statisc](origin/openshift-开启router的haproxy-statisc.md)
        * [openshift的多租户网络](origin/openshift-多租户网络.md)
    * 安全审计
        * [Kubernetes的审计日志功能](origin/openshift-kubernetes的审计日志功能.md)

* 工具应用部署
    * Jenkins
    * Gitlab
    * Nexus
    * logstash
    * [Elasticsearch容器化部署](origin/openshift-elasticsearch容器化部署.md)
    * [Kibana容器化部署](origin/openshift-Kibana容器化部署.md)
* 业务应用部署
    * S2I
        * JavaMaven
    * S2I Template

## Part Ⅱ：容器云Kubernetes
* 基础
  * [Kubernetes的集群角色及插件](origin/kubernetes-集群角色及插件.md)
* 安装
  * [Kubeadm安装单机版Kubernetes](origin/kubernetes-使用Kubeadm安装单机版Kubernetes.md)
* 集群管理
  * [kubernetes集群性能监控](origin/prometheus-Kubernetes或Openshift的Prometheus监控体系.md)
  * [kubernetes集群组件](origin/kubernetes-集群组件.md)
  * [kubectl多集群上下文配置](origin/kubernetes-kubectl多集群上下文配置.md)
  * [Network Policy容器流量管理](origin/kubernetes-NetworkPolicy.md)
* [K8S应用管理工具Helm](origin/kubernetes-helm.md)
  * [helm charts编写规则](origin/Kubernetes-helm-charts编写规则.md)
* 问题
  * [Service与SpringBoot应用启动参数冲突的问题排查及解决方案](origin/Service与SpringBoot应用启动参数冲突的问题排查及解决方案.md)

## Part Ⅲ：持续集成与持续部署

* Jenkins
    * [Jenkins API](origin/jenkins-api.md)
    * 管理
        * [Jenkins共享库Shared Libraries](origin/jenkins-SharedLibraries.md)
    * Pipeline
        * [声明式Declarative语法](origin/jenkins-声明式Declarative-pipeline语法.md)
    * 插件
        * [Kubernetes Plugin](origin/Jenkins-在Kubernetes上使用Kubernetes插件动态创建Slave节点.md)
        * [Pipeline Utility Steps](origin/jenkins-pipeline-utility-steps.md)
        * [Nexus Platform Plugin](origin/jenkins-Nexus-Platform的使用.md)
        * [Mail Plugin](origin/jenkins-配置SMTP邮箱服务.md)
        * [Mail Extension](origin/jenkins-Mailer邮箱功能扩展插件Email-Extension.md)
        * [Gitlab](origin/jenkins-gitlab插件的使用.md)
        * [Generic Webhook Trigger](origin/jenkins-generic-webhook-trigger插件.md)
* Gitlab
    * 安装
    * 管理
        * [配置SMTP邮件服务](origin/gitlab-配置SMTP邮件服务.md)
        * [代码仓库配置事件触发器Webhook](origin/gitlab-配置代码仓库事件触发器Webhook.md)
* [Nexus](origin/nexus-简介.md)
    * 配置
        * [使用OrientDB Console在DB层面修改配置](origin/nexus-使用OrientDB Console在DB层面修改配置.md)
        * [设置SMTP邮件服务](origin/nexus-设置SMTP邮件服务.md)
    * 权限
    * 仓库管理
        * [Maven](origin/nexus-maven仓库的配置与使用.md)
        * [NPM](origin/nexus-npm仓库的配置与使用.md)
        * [YUM](origin/nexus-yum仓库的配置与使用.md)
        * Docker
        * RAW
    * [数据备份恢复](origin/nexus-数据的备份恢复.md)
    * [API](/origin/nexus-api.md)
    * [Jenkins相关插件](origin/nexus-使用jenkins插件上传CI流程制品到Nexus仓库.md)
* [SonarQube静态代码扫描分析](origin/SonarQube静态代码扫描分析简介.md)
    * [SonarScanner-将扫描结果以comment的形式回写到gitlab](origin/sonarscanner-将扫描结果以comment的形式回写到gitlab.md)
* LDAP
    * 安装
    * 客户端
        * Apache Directory Studio
        * PHPLDAPAdmin
    * 第三方系统集成
        * [Jenkins](origin/ldap-Jenkins对接LDAP.md)
        * [SonarQube](origin/ldap-SonarQube对接LDAP.md)
        * [Gitlab](origin/ldap-Gitlab对接LDAP.md)
        * [Nexus](origin/ldap-Nexus对接LDAP.md)
        * [Grafanaj](origin/ldap-Grafana对接LDAP.md)
        * [Jira](origin/ldap-jira接LDAP.md)
* 项目管理工具
    * Jira
        * [Jira的部署](origin/jira-部署.md)

## Part Ⅳ：微服务
* SpringBoot
* SpringCloud
* Consul
* Apollo

## Part Ⅴ：监控体系
* Logging
    * [日志系统技术概览简介](origin/logging-日志系统技术概览简介.md)
    * [日志系统数据在个组件中的流转格式](origin/logging-日志系统数据在个组件中的流转格式.md)
    * Kafka
        * 安装
        * [基础知识](origin/logging-kafka基础知识.md)
        * [kafka常用操作](origin/logging-kafka常用操作.md)
    * Filebeat
        * [简介安装配置](origin/filebeat-简介安装配置.md)
        * [多实例安装部署](origin/filebeat-多实例部署.md)
        * [Filebeat Modules](origin/filebeat-modules模块.md)
            * [Nginx Module](origin/filbeat-nginx-module.md)
    * Logstash
        * [简介安装配置Pipeline](origin/logstash-简介安装配置Pipeline.md)
        * [Pipeline示例--采集MySQL慢查询日志到Elasticsearch](origin/logstash-采集MySQL慢查询日志到Elasticsearch.md)
    * Fluentd
    * Elasticsearch
        * [基础知识](origin/elasticsearch-基础知识.md)
            * API Endpoints
                * [_cat](origin/elasticsearch--_cat-API.md)
                * [index](origin/elasticsearch-index-api.md)
                * search
                * [bulk](origin/elasticsearch-bulk-api.md)
            * [Ingest节点](origin/elasticsearch-ingest节点.md)
            * [数据的分配路由](origin/elasticsearch-数据的分配路由.md)
        * 管理
            * [Xpack](origin/elasticsearch-7.1的xpack权限控制.md)
            * [Snapshots](origin/elasticSearch-索引的快照备份与恢复.md)
            * [插件管理](origin/elasticsearch-插件管理.md)
        * [性能优化](origin/elasticsearch-optimizing.md)
        * [问题总结](origin/elasticsearch-问题总结.md)
    * Kibana
* Metrics
    * Prometheus
    * Grafana
    * Exporter
        * [Ceph Exporter](origin/prometheus-Ceph-Exporter对接Prometheus以监控ceph集群.md)
* Tracing
    * SkyWalking
* [Sentry日志聚合告警平台](origin/sentry.md)
    * [Logstash与Sentry对接](origin/sentry-logstash对接Sentry.md)

## Part Ⅵ：基础
* Docker
    * 基础知识
      * [Docker原理](origin/docker原理.md)
      * [Dockerfile中CMD与ENTRYPOINT命令的区别](origin/docker-Dockerfile中CMD与ENTRYPOINT命令的区别.md)
      * [使用Makefile操作Dockerfile.md](origin/docker-使用Makefile操作Dockerfile.md)
* Shell
    * [变量](origin/shell-变量.md)
    * 运算判断
        * [文件目录的判断](origin/shell-文件目录的判断.md)
        * [数值型的判断](origin/shell-数值型的判断.md)
    * 语句控制
        * [if判断](origin/shell-if判断.md)
        * [for循环语句](origin/shell-for循环语句.md)
        * [while循环语句](origin/shell-while循环语句.md)
        * [until循环语句](origin/shell-until循环语句.md)
    * 字符串操作
        * [字符串的截取拼接](origin/shell-字符串的截取拼接.md)
        * [字符串的包含判断关系](origin/shell-字符串的包含判断关系.md)
    * [自定义函数](origin/shell-自定义函数.md)
* Maven
    * Maven POM项目对象模型
    * [Mave Settings文件详解](origin/maven-Settings配置文件详解.md)
    * [Maven 生命周期阶段](origin/maven-生命周期阶段.md)
    * Maven 多模块构建
* Git
* [正则表达式](origin/regular-expression详解.md)
* Ceph
    * 安装
        * [Ceph RBD单节点安装](origin/ceph-rbd单节点安装.md)
        * [Ceph FileSystem单节点安装](origin/ceph-filesystem单节点安装.md)
    * 基础知识
* FastDFS
    * 安装
    * 使用
* PXE+Kickstart
    * [PXE-Kickstart无人值守部署OS](origin/pxe-kickstart无人值守部署OS.md)
    * [Kickstart文件参数详解](origin/pxe-kickstart文件参数详解.md)
    * [PXE引导配置文件参数详解](origin/pxe-引导配置文件参数详解.md)
* Ansible
    * 安装
    * 使用
    * 配置
    * PlayBooks
* Tool
    * [Sublime Text 3](origin/tool-SublimeText.md)
    * IDEA
* Windows
    * [CMD发送SMTP邮件](origin/windows-cmd发送SMTP邮件.md)
    * [Windows小技巧](origin/windows-小技巧.md)
* Linux
    * [Linux小技巧](origin/linux-小技巧.md)
    * [文本处理](origin/linux-文本处理.md)
    * [htpasswd](origin/linux-htpasswd.md)
    * [YAML文本处理工具shyaml](origin/linux-shyaml.md)
    * [JSON文本处理工具jq](origin/linux-jq.md)
    * [Curl命令详解](origin/linux-curl.md)
    * [LVM原理及使用](origin/linux-lvm.md)
    * [Linux交换分区](origin/linux-交换分区.md)
    * [Linux硬盘读写性能测试](origin/linux-硬盘读写性能测试.md)
    * [Vim小技巧](origin/vim-小技巧.md)
    * [Yum-RPM包管理](origin/linux-yum.md)
    * [ZSH](origin/linux-zsh.md)
    * [Systemd-进程管理](origin/linux-进程管理工具SystemD.md)
* 数据库
    * MySQL
    * SQLServer
    * ETCD
    * PostgreSQL
* [代理服务器](origin/正反向代理服务的区别.md)
    * [正向代理](origin/常见正向代理服务软件之间的区别.md)
        * Squid
            * [简介安装日志](origin/squid-简介安装.md)
            * [ACL访问权限](origin/squid-acl访问权限控制.md)
        * Varnish
        * Nginx第三方正向代理插件
    * [反向代理](origin/常见反向代理服务软件之间的区别.md)
        * [Nginx](origin/nginx.md)
        * HAProxy
        * Apache
* iSCSI
    * [群晖Synology的iSCSI](origin/iSCSI-简介配置使用.md)
* [GitBook](origin/gitbook-简介安装配置.md)
* [Telegram机器人](origin/telegram-Bot机器.md)