# Jenkins Generic Webhook Trigger插件

之前写过在Jenkins中使用Gitlab插件接受gitlab仓库代码指定事件触发的webhook来触发Job或Pipeline的执行构建,详见:[Jenkins的Gitlab插件](jenkins-gitlab插件的使用.md)。这种方式在某种场景下有些限制，无法满足一些功能性复杂的webhook触发。所以可以使用Jenkins Generic Webhook Trigger插件监听包含自定义设置的HTTP请求来触发job/pipeline的构建！对比Gitlab插件，有以下可自定义的特性：

- 暴露出来的回调API URL统一，不同的job/pipeline使用不同的Token或者指定特殊的请求参数进行区分
- 可使用不同的方式提取HTTP请求中的各种信息，然后通过环境变量的形式传递给job/pipeline使用
- 可设置白名单，只允许接收指定IP地址的Webhook请求

# 一、简介

- Generic Webhook Trigger 是一款Jenkins插件，简称GWT，安装后会暴露出来一个公共API，GWT插件接收到 JSON 或 XML 的 HTTP POST 请求后，根据我们配置的规则决定触发哪个Jenkins项目

- 安装的话在Jenkins的插件管理中心直接搜索安装即可，下载HPI文件手动安装, [插件下载地址](https://updates.jenkins.io/download/plugins/generic-webhook-trigger) 
- 插件Github地址：https://github.com/jenkinsci/generic-webhook-trigger-plugin

# 二、使用说明

GenericTrigger 触发条件分为5部分：

1. 从 HTTP POST 请求中提取参数值。
2. Token, GWT 插件用于标识Jenkins项目的唯一性。
3. 根据请求参数值判断是否触发Jenkins项目的执行。
4. 日志打印控制。
5. Webhook 响应控制。

![](../assets/jenkins-generic-webhook-trigger-1.png)

## 1. 从 HTTP POST 请求中提取参数值

一个 HTTP POST 请求可以从三个维度提取参数，即 POST Body、URL参数和header。GWT 插件提供了三个参数分别从这三个维度的数据进行提取。

- **genericRequestVariables**：从URL参数中提取值。
- **genericVariables**： 从HTTP POST的body 中提取值。
- **genericHeaderVariables**：从HTTP header 中提取值。用法和genericRequestVariables一样。

## 2. Token 参数







# 三、设置白名单

可在Jenkins Generic Webhook Trigger的全局配置中，配置IP地址白名单，指定、验证请求的来源IP地址，IP地址格式可为**CIDR** 或 **ranges**。

- ***1.2.3.4***
- ***2.2.3.0/24***
- ***3.2.1.1-3.2.1.10***
- ***2001:0db8:85a3:0000:0000:8a2e:0370:7334***
- ***2002:0db8:85a3:0000:0000:8a2e:0370:7334/127***

同时还支持HMAC验证([HMAC百度百科](https://baike.baidu.com/item/hmac/7307543?fr=aladdin))。

`Jenkins -->  Manage Jenkins --> Configure System` 

![1573445368175](../assets/jenkins-generic-webhook-trigger-ipwhitelist.png)