# Helm简介、安装、配置、使用



# 一、简介



# 二、原理







# 三、安装

## Tiller服务端安装



## Helm客户端安装





# 四、配置



# 五、命令行参数








```bash

helm template -f values-dev.yaml .

helm del --purge logstash-producer-mysql-slowlog

helm install --namespace logger --name logstash-producer-mysql-slowlog -f values.yaml .

helm upgrade logstash-producer-mysql-slowlog -f values.yaml .

```