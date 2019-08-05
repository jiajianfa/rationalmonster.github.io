# Summary

* [简介](README.md)

## Part Ⅰ - 容器云Openshift
* 安装
    * Allinone[Allinone](origin/openshift-allinone安装.md)
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
    * 数据持久化
    * 管理
        * 资源对象管理
            * [资源对象常见操作](origin/openshift-资源对象常见操作.md)
        * 集群管理
            * [节点管理](origin/openshift-集群节点管理.md)
    * 网络
        * [openshift开启router的haproxy-statisc](origin/openshift-开启router的haproxy-statisc.md)
    * 安全审计
        * [Kubernetes的审计日志功能](origin/openshift-kubernetes的审计日志功能.md)

* 工具应用部署
    * Jenkins
    * Gitlab
    * Nexus
    * [Elasticsearch容器化部署](origin/openshift-elasticsearch容器化部署.md)
    * [Kibana容器化部署](origin/openshift-Kibana容器化部署.md)
* 业务应用部署
    * S2I
        * JavaMaven
    * S2I Template

## Part Ⅱ：容器云Kubernetes
* 安装
* 集群管理

## Part Ⅲ：持续集成与持续部署
* Jenkins
    * 安装
    * 管理
    * Pipeline
        * 语法
        * 样例
    * 插件
        * Kubernetes Plugin
        * Pipeline Utility Plugin
        * Nexus Platform Plugin
        * EMail Extension Plugin
* Gitlab
    * 安装
    * 管理
        * [配置SMTP邮件服务](origin/gitlab-配置SMTP邮件服务.md)
* Nexus
    * 安装
    * 权限管理
        * 权限管理
    * 仓库管理
        * Maven
        * NPM
        * YUM
        * Docker
        * RAW
* SonarQube
    * 安装
    * 使用
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
        * [Grafana](origin/ldap-Grafana对接LDAP.md)

## Part Ⅳ：微服务
* SpringBoot
* SpringCloud
* Consul
* Apollo

## Part Ⅴ：监控体系
* Logging
    * Kafka
        * 安装
        * 基础知识
    * Logstash
        * 安装
        * Pipeline
            * Input
            * Filter
            * Output
    * Fluentd
    * Elasticsearch
        * 基础知识
        * 管理
            * [Xpack](origin/elasticsearch-7.1的xpack权限控制.md)
            * [Snapshots](origin/elasticSearch-索引的快照备份与恢复.md)
        * [问题总结](origin/elasticsearch-问题总结.md)
    * Kibana
* Metrics
    * Prometheus
    * Grafana

## Part Ⅵ：大数据
* Apache
    * Zookeeper
    * HDFS
    * YARN
    * HBase
    * Hive
    * Sqoop
    * Flume
    * Oozie
    * Hue
    * Spark
* Cloudera
    * 安装
    * 管理
* TDH
    * 安装

## Part Ⅶ：基础
* Docker
    * 基础知识
      * [Dockerfile中CMD与ENTRYPOINT命令的区别](origin/docker-Dockerfile中CMD与ENTRYPOINT命令的区别.md)
      * [使用Makefile操作Dockerfile.md](origin/docker-使用Makefile操作Dockerfile.md)
* Shell
* Maven
* Git
* Ceph
    * 安装
        * [Ceph RBD单节点安装](origin/ceph-rbd单节点安装.md)
        * [Ceph FileSystem单节点安装](origin/ceph-filesystem单节点安装.md)
    * 基础知识
* FastDFS
    * 安装
    * 使用
* PXE+Kickstart
    * Linux
    * Windows
* Ansible
    * 安装
    * 使用
    * 配置
    * PlayBooks
* IDEA
* Windows
* Linux
    * CentOS
    * Ubuntu
* 数据库
    * MySQL
    * SQLServer
    * ETCD
    * PostgreSQL
* NPM
    * 安装
    * 使用
    * 插件
        * [npm仓库管理工具nrm](origin/npm仓库管理工具nrm.md)
* GitBook
    * 安装
    * 文档结构
    * 常用插件

