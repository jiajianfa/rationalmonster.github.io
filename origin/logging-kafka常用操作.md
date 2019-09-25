# Apache Kafka常用操作

# 一、Topic管理
## 1、列出所有Topic

```bash
kafka-topics.sh --zookeeper 127.0.0.1:2181 --list

kafka-topics.sh --bootstrap-server 127.0.0.1:9092 --list
```

## 2、创建一个topic

```bash
kafka-topics.sh --create --zookeeper  127.0.0.1:2181 --replication-factor 2 --partitions 3 --topic Test
#--replication-factor参数指定Topic的数据副本个数
#--partitions参数指定Topic的分区个数
```

## 3、删除Topic

```bash
kafka-topics.sh --delete --zookeeper 127.0.0.1:2181 --topic Test
或者
#只会删除zookeeper中的元数据，消息文件须手动删除
kafka-run-class.sh kafka.admin.DeleteTopicCommand --zookeeper 172.16.3.12:2181/kafka/q-35aw0fye --topic Test
```


## 4、查看Topic的详细信息

```bash
kafka-topics.sh --describe --zookeeper 127.0.0.1:2181 --topic Test

Topic:Test        PartitionCount:2        ReplicationFactor:1     Configs:
Topic: Test       Partition: 0    Leader: 2  Replicas: 2          Isr: 2
Topic: Test       Partition: 1    Leader: 3   Replicas: 3     Isr: 3

#第一行，列出了topic的名称，分区数(PartitionCount),副本数(ReplicationFactor)以及其他的配置(Config.s) 
#Leader:1 表示为做为读写的broker的节点编号 
#Replicas:表示该topic的每个分区在那些borker中保存 
#Isr:表示当前有效的broker, Isr是Replicas的子集
```


## 5、增加Topic分区个数（只能增加扩容）

```bash
kafka-topics.bat --zookeeper 127.0.0.1:2181 --alter --topic Test --partitions 2 
```

## 6、给Topic增加配置

```bash
kafka-topics.sh --zookeeper 127.0.0.1:2181 --alter --topic Test --config flush.messages=1
```

## 7、删除Topic的配置

```bash
kafka-topics.sh --zookeeper 127.0.0.1:2181 --alter --topic Test
--delete-config flush.messages=1
```

## 8、查看消费者组

```bash
kafka-consumer-groups.sh --bootstrap-server 127.0.0.1:9092 --list
```

## 9、查看Topic各个分区的消息偏移量最大（小）值

```bash
kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list 127.0.0.1:9092 --time -1 --topic Test
# time为-1时表示最大值，time为-2时表示最小值
```

## 10、查看Topic中指定consumer组内消息消费的offset

kafka的offset保存位置分为两种情况 0.9.0.0版本之前默认保存在zookeeper当中 ，0.9.0.0版本之后保存在broker对应的topic当中

```bash
kafka-consumer-offset-checker.sh --zookeeper 127.0.0.1:2181 --group logstash-group --topic Test

GROUP                       TOPIC                PID           OFFSET                LOGSIZE         LAG            Ower
消费者组                    话题id               分区id        当前已消费的条数      总条数      未消费的条数       所有者
console-consumer-98995      Test                 0            112                   318084         317972          none
console-consumer-98995      Test                 1            -1                    318088         unknown         none
```

方式二：

```bash
kafka-consumer-groups.sh --bootstrap-server 127.0.0.1:9092 --describe --offsets --group Group-Name


TOPIC           PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG             CONSUMER-ID                                     HOST            CLIENT-ID
basic_log_k8s   1          127509          333334          205825          logstash-0-490058d7-154f-4111-b514-57de254ecae8 /192.168.3.72   logstash-0
basic_log_k8s   0          127317          333333          206016          logstash-0-2bd85bcc-282e-41a0-a9c3-6d0dbefd547f /192.168.0.40   logstash-0
```

## 11、修改指定消费者分组对应topic的offset
第一种情况offset信息保存在topic中

```bash
$ bin/kafka-consumer-groups.sh --bootstrap-server 127.0.0.1:9092 --group test-consumer-group --topic test --execute --reset-offsets --to-offset 10000
#参数解析： 
#--bootstrap-server 代表你的kafka集群 你的offset保存在topic中
#--group 代表你的消费者分组
#--topic 代表你消费的主题
#--execute 代表支持复位偏移
#--reset-offsets 代表要进行偏移操作
#--to-offset 代表你要偏移到哪个位置 是long类型数值，只能比前面查询出来的小
#还有其他的--to- ** 方式可以自己验证 本人验证过--to-datetime 没有成功
```

第二种方式offset信息保存在zookeeper当中

```bash
$ bin/kafka-consumer-groups.sh --zookeeper kafka_zk1:2181 --group test-consumer-group --topic test --execute --reset-offsets --to-offset 10000
```

# 二、其他
## 1、Kafka自带的测试生产者
```bash
kafka-console-producer.sh --broker-list 127.0.0.1:9092 --topic Test
```

## 2、Kafka自带的测试消费者
```json
kafka-console-consumer.sh --zookeeper 127.0.0.1:2181 --from-beginning --topic Test
```

## 3、kafka自带的性能测试

位于bin/kafka-producer-perf-test.sh.主要参数有以下:

- messages 生产者发送总的消息数量
- message-size 每条消息大小（单位为b）
- batch-size 每次批量发送消息的数量
- topics 生产者发送的topic
- threads 生产者使用几个线程同时发送
  例如

```
bin/kafka-producer-perf-test.sh --messages 100000 --message-size 1000 --batch-size 10000 --topics test --threads 4 --broker-list 127.0.0.1:9092

start.time, end.time, compression, message.size, batch.size, total.data.sent.in.MB, MB.sec, total.data.sent.in.nMsg, nMsg.sec
2015-10-15 18:56:27:542, 2015-10-15 18:56:30:880, 0, 1000, 10000, 95.37, 28.5702, 100000, 29958.0587
```


