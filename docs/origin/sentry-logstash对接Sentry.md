# Logstah与Sentry的对接

# **一、简介**

Sentry作为一个日志异常告警平台，对于异常日志聚合的告警，功能很强大。而基于ELK的日志系统，只能采集、聚合、存储应用日志，无法针对日志中的异常进行检测，聚合告警。所以可以在Logstash采集过程中输出一份日志数据到Sentry中进行聚合告警。

logstash-output-sentry插件：https://github.com/javiermatos/logstash-output-sentry

**相关文章：**

1. https://medium.com/@yscaliskan/how-to-use-logstash-along-with-sentry-6c3d27790a38
2. https://clarkdave.net/2014/01/tracking-errors-with-logstash-and-sentry/

**整体对接思路**

1. **Filebeat file Input(多行采集+打标签)  ------->   Filebeat processor(添加字段) ------->  Filebeat logatash  output(输出到filebeat进行加工处理)**
2. **Logstash beat input (监听) ------->  Logstash dissect filter(判断符合标签的事件+从事件原始日志中映射提取字段) ------->   Logstash sentry Output(输出到Sentry中)**

**Filebeat 采集、输出要求: **

1. 可以多行采集(设置上下日志事件的标识),多行采集的日志信息到统一放到日志事件的“message”字段中
2. 添加采集日志的类型字段
3. 添加与Sentry相关信息(sentry上项目的ID、key、Secret)的字段
4. 删除一些默认添加的字段信息
5. 以日志中该日志产生的时间为事件的时间，而不是采集时的时间为事件时间

**Logtash 接收、处理要求：**

1. 根据filebeat传送过来的事件中的类型字段判断是否进行过滤加工

# **二、上下文**

以API网关Kong的Nginx的错误日志为例（该Nginx安装了LUA模块，错误日志里面有lua模块的错误日志）。该日志文件有199行日志，综合分析，可归为两类错误类型，如下：

> 2018/11/28 18:16:25 [warn] 2201#0: *9081632 [lua] cluster.lua:182: set_peer_down(): [lua-cassandra] setting host at 172.17.1.8 DOWN, context: ngx.timer
> 2018/11/28 18:16:25 [error] 2201#0: *9081632 [lua] init.lua:365: [cluster_events] failed to poll: failed to retrieve events from DB: [Unavailable exception] Cannot achieve consistency level LOCAL_ONE, context: ngx.timer

每一行日志可大致格式分为:

> **时间戳 日志级别 进程号 抛弃该处数据 模块名 具体错误日志**

# **三、配置**

## 1. logstash安装sentry output插件

```bash
/usr/share/logstash/bin/logstash-plugin install logstash-output-sentry
/usr/share/logstash/bin/logstash-plugin list 
```

## 2. Logstash配置

```bash
input {
  file {
    path =>  "/root/Curiouser/test.log"
    start_position => "beginning"
    type => "log-alert"
    add_field => {
      service_name => "kong"
      sentry_project_id => "7"
      sentry_project_key => "***"
      sentry_project_secret => "***"
    }
  }
}
filter {
 if [type] == "log-alert" {
    dissect {
       mapping => {
         "message" => "%{timestamp} %{+timestamp} [%{level}] %{thread} %{} %{message}"
      }
    }
    date {
      match => [ "timestamp", "yyyy/MM/dd HH:mm:ss" ]
      remove_field => "timestamp"
    }
    mutate {
      gsub => [ "level", "warn", "warning" ]
    }
  }    
}
output {
  if [level] == "warning" or [level] == "error" or [level] == "fatal"  {  
    sentry {
      message => "message"
      threads => '%{thread}'
      level => "level"
      tags =>  'service:"service_name"'

      url => "http://sentry.curiouser.com/api"
      key => '%{sentry_project_key}'
      secret => '%{sentry_project_secret}'
      project_id => '%{sentry_project_id}'
    }
  }
}
```





