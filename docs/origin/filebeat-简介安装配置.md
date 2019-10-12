

# Filebeat的简介. 安装. 配置. Pipeline

# 一. 简介

![Beats design](../assets/filebeat-简介安装配置pipeline-1.png)

Filebeat由两个主要组件组成：

- **Inputs**：

  - 负责管理harvester并找到所有要读取的文件来源。如果输入类型为日志，则查找器将查找路径匹配的所有文件，并为每个文件启动一个harvester。每个Inputs都在自己的Go协程中运行

  - 每个prospector类型可以定义多次

- **Harvesters**：

  - 一个harvester负责读取一个单个文件的内容，每个文件启动一个harvester。harvester逐行读取每个文件（一行一行地读取每个文件），并把这些内容发送到输出。在harvester正在读取文件内容的时候，文件被删除或者重命名了，那么Filebeat会续读这个文件。这就有一个问题了，就是只要负责这个文件的harvester没用关闭，那么磁盘空间就不会释放。默认情况下，Filebeat保存文件打开的状态直到close_inactive到达。
  - 关闭harvester会产生以下结果：
    - 如果在harvester仍在读取文件时文件被删除，则关闭文件句柄，释放底层资源。
    - 文件的采集只会在scan_frequency过后重新开始
    - 如果在harvester关闭的情况下移动或移除文件，则不会继续处理文件





# 二. 安装

默认的安装文件路径

| Type       | Description                                    | Default Location   | Config Option |
| ---------- | ---------------------------------------------- | ------------------ | ------------- |
| **home**   | Home of the Filebeat installation.             |                    | `path.home`   |
| **bin**    | The location for the binary files.             | `{path.home}/bin`  |               |
| **config** | The location for configuration files.          | `{path.home}`      | `path.config` |
| **data**   | The location for persistent data files.        | `{path.home}/data` | `path.data`   |
| **logs**   | The location for the logs created by Filebeat. | `{path.home}/logs` | `path.logs`   |

## YUM/RPM

```properties
[elastic-7.x]
name=Elastic repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md

yum install filebeat-7.4.0
```



RPM下载地址：https://www.elastic.co/cn/downloads/beats/filebeat

```bash
yum localinstall -y filebeat-7*.rpm
```



安装文件路径

| Type       | Description                                    | Location                  |
| ---------- | ---------------------------------------------- | ------------------------- |
| **home**   | Home of the Filebeat installation.             | `/usr/share/filebeat`     |
| **bin**    | The location for the binary files.             | `/usr/share/filebeat/bin` |
| **config** | The location for configuration files.          | `/etc/filebeat`           |
| **data**   | The location for persistent data files.        | `/var/lib/filebeat`       |
| **logs**   | The location for the logs created by Filebeat. | `/var/log/filebeat`       |



## 二进制文件

 zip, tar.gz, tgz 压缩格式的二进制安装包，下载地址：https://www.elastic.co/cn/downloads/beats/filebeat



安装文件路径

| Type       | Description                                    | Location              |
| ---------- | ---------------------------------------------- | --------------------- |
| **home**   | Home of the Filebeat installation.             | `{extract.path}`      |
| **bin**    | The location for the binary files.             | `{extract.path}`      |
| **config** | The location for configuration files.          | `{extract.path}`      |
| **data**   | The location for persistent data files.        | `{extract.path}/data` |
| **logs**   | The location for the logs created by Filebeat. | `{extract.path}/logs` |

## 启动

```bash
# systemD
systemctl enable filebeat
systemctl start filebeat 
systemctl stop filebeat
systemctl status filebeat
journalctl -u filebeat.service
# Cli
filebeat -e
```

## Filebeat命令行参数



```
filebeat test config
```



# 三. Docker镜像

```sh
docker pull docker.elastic.co/beats/filebeat:7.4.0

docker pull filebeat:7.4.0
```



## 镜像中的安装文件路径

| Type       | Description                                    | Location                   |
| ---------- | ---------------------------------------------- | -------------------------- |
| **home**   | Home of the Filebeat installation.             | `/usr/share/filebeat`      |
| **bin**    | The location for the binary files.             | `/usr/share/filebeat`      |
| **config** | The location for configuration files.          | `/usr/share/filebeat`      |
| **data**   | The location for persistent data files.        | `/usr/share/filebeat/data` |
| **logs**   | The location for the logs created by Filebeat. | `/usr/share/filebeat/logs` |

## Kubernetes部署

- 默认部署到kube-system命名空间

- 部署类型是Daemonset，会部署到每一个Node上

- 每个Node上的/var/lib/docker/containers目录会挂载到filebeat容器中

- 默认Filebeat会将日志吐到kube-system命名空间下的elasticsearch中，如果需要指定吐到其他elasticsearch中，修改环境变量

  ```json
  - name: ELASTICSEARCH_HOST
    value: elasticsearch
  - name: ELASTICSEARCH_PORT
    value: "9200"
  - name: ELASTICSEARCH_USERNAME
    value: elastic
  - name: ELASTICSEARCH_PASSWORD
    value: changeme
  ```

```bash
curl -L -O https://raw.githubusercontent.com/elastic/beats/7.4/deploy/kubernetes/filebeat-kubernetes.yaml
kubectl create -f filebeat-kubernetes.yaml
kubectl --namespace=kube-system get ds/filebeat
```

## OKD部署

```bash
curl -L -O https://raw.githubusercontent.com/elastic/beats/7.4/deploy/kubernetes/filebeat-kubernetes.yaml
修改部署文件
securityContext:
    runAsUser: 0
    privileged: true
    
oc adm policy add-scc-to-user privileged system:serviceaccount:kube-system:filebeat

```



# 四. 配置

## 全局配置项

| 配置项                    | 默认值                | 描述                                   |
| ------------------------- | --------------------- | -------------------------------------- |
| registry.path             | ${path.data}/registry | 注册表文件的根路径                     |
| registry.file_permissions | 0600                  | 注册表文件的权限。Window下该配置项无效 |
| registry.flush            | 0s                    |                                        |
| registry.migrate_file     |                       |                                        |
| config_dir                |                       |                                        |
| config_dir                |                       |                                        |
| name                      |                       |                                        |
| tags                      |                       |                                        |
| fields                    |                       |                                        |
| fields_under_root         |                       |                                        |
| processors                |                       |                                        |
| max_procs                 |                       |                                        |
|                           |                       |                                        |
|                           |                       |                                        |



# 五. Input

Input类型

| 类型                                                         | 描述                   | 配置示例                                                     |
| ------------------------------------------------------------ | ---------------------- | ------------------------------------------------------------ |
| [Log](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-log.html) | 从日志文件中读取每一行 | filebeat.inputs: <br/>- type: log<br>  paths:<br/>     - /var/log/messages<br/>     - /var/log/*.log |
| [Stdin](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-stdin.html) |                        | filebeat.inputs:<br/> - type: stdin                          |
| [Container](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-container.html) |                        | filebeat.inputs:<br/> - type: container<br/>   paths:<br/>      - '/var/lib/docker/containers/*/*.log' |
| [Kafka](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-kafka.html) |                        | filebeat.inputs:<br/> - type: kafka<br/>   hosts:<br/>     - kafka-broker-1:9092<br/>     - kafka-broker-2:9092<br/>   topics: ["my-topic"]<br/>   group_id: "filebeat" |
| [Redis](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-redis.html) |                        | filebeat.inputs:<br/> - type: redis<br/>   hosts: ["localhost:6379"]<br/>   password: "${redis_pwd}" |
| [UDP](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-udp.html) |                        | filebeat.inputs:<br/> - type: udp<br/>   max_message_size: 10KiB<br/>   host: "localhost:8080" |
| [ Docker](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-docker.html) |                        | filebeat.inputs:<br/> - type: docker<br/>   containers.ids:<br/>      - 'e067b58476dc57d6986dd347' |
| [TCP](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-tcp.html) |                        | filebeat.inputs:<br/> - type: tcp<br/>   max_message_size: 10MiB<br/>   host: "localhost:9000" |
| [Syslog](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-syslog.html) |                        | filebeat.inputs:<br/> - type: syslog<br/>   protocol.udp:<br/>     host: "localhost:9000" |
| [s3](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-s3.html) |                        | filebeat.inputs:<br/> - type: s3<br/>   queue_url: https://test.amazonaws.com/12/test<br/>   access_key_id: my-access-key<br/>   secret_access_key: my-secret-access-key |
| [NetFlow](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-netflow.html) |                        |                                                              |
| [Google Pub/Sub](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-input-google-pubsub.html) |                        |                                                              |



# 六. Output

| 类型                                                         | 描述 | 配置样例                                                     |
| ------------------------------------------------------------ | ---- | ------------------------------------------------------------ |
| [Elasticsearch](https://www.elastic.co/guide/en/beats/filebeat/current/elasticsearch-output.html) |      | output.elasticsearch:<br/>     hosts: ["https://localhost:9200"]<br/>     protocol: "https"<br/>     index: "filebeat-%{[agent.version]}-%{+yyyy.MM.dd}"<br/>     ssl.certificate_authorities: ["/etc/pki/root/ca.pem"]<br/>     ssl.certificate: "/etc/pki/client/cert.pem"<br/>     ssl.key: "/etc/pki/client/cert.key"<br/>     username: "filebeat_internal"<br/>     password: "YOUR_PASSWORD" |
| [Logstash](https://www.elastic.co/guide/en/beats/filebeat/current/logstash-output.html) |      | output.logstash:<br/>     hosts: ["127.0.0.1:5044"]          |
| [Kafka](https://www.elastic.co/guide/en/beats/filebeat/current/kafka-output.html) |      | output.kafka:<br/>     hosts: ["kafka1:9092", "kafka2:9092", "kafka3:9092"]<br/>     topic: '%{[fields.log_topic]}'<br/>     partition.round_robin:<br/>            reachable_only: false<br/>     required_acks: 1<br/>     compression: gzip<br/>     max_message_bytes: 1000000 |
| [Redis](https://www.elastic.co/guide/en/beats/filebeat/current/redis-output.html) |      | output.redis:<br/>     hosts: ["localhost"]<br/>     password: "my_password"<br/>     key: "filebeat"<br/>     db: 0<br/>     timeout: 5 |
| [File](https://www.elastic.co/guide/en/beats/filebeat/current/file-output.html) |      | output.file:<br/>     path: "/tmp/filebeat"<br/>     filename: filebeat<br/>     #rotate_every_kb: 10000<br/>     #number_of_files: 7<br/>     #permissions: 0600 |
| [Console](https://www.elastic.co/guide/en/beats/filebeat/current/console-output.html) |      | output.console:<br/>     pretty: true                        |
| [Cloud](https://www.elastic.co/guide/en/beats/filebeat/current/configure-cloud-id.html) |      |                                                              |



# 七. Processors

## 配置语法

```yaml
processors:
- if:
    <condition>
  then: 
    - <processor_name>:
        <parameters>
    - <processor_name>:
        <parameters>
    ...
  else: 
    - <processor_name>:
        <parameters>
    - <processor_name>:
        <parameters>
```

可以再Input中添加Processor

```json
- type: <input_type>
  processors:
  - <processor_name>:
      when:
        <condition>
      <parameters>
```



## 条件语法

- [`equals`](https://www.elastic.co/guide/en/beats/filebeat/current/defining-processors.html#condition-equals)

  ```yaml
  equals:
    http.response.code: 200
  ```

- [`contains`](https://www.elastic.co/guide/en/beats/filebeat/current/defining-processors.html#condition-contains)

  ```json
  contains:
    status: "Specific error"
  ```

- [`regexp`](https://www.elastic.co/guide/en/beats/filebeat/current/defining-processors.html#condition-regexp)

  ```json
  regexp:
    system.process.name: "foo.*"
  ```

- [`range`](https://www.elastic.co/guide/en/beats/filebeat/current/defining-processors.html#condition-range)：The condition supports `lt`, `lte`, `gt` and `gte`. The condition accepts only integer or float values.

  ```json
  range:
      http.response.code:
          gte: 400
  ```

- [`network`](https://www.elastic.co/guide/en/beats/filebeat/current/defining-processors.html#condition-network)

  ```json
  network:
      source.ip: private
      destination.ip: '192.168.1.0/24'
      destination.ip: ['192.168.1.0/24', '10.0.0.0/8', loopback]
  ```

- [`has_fields`](https://www.elastic.co/guide/en/beats/filebeat/current/defining-processors.html#condition-has_fields)

  ```json
  has_fields: ['http.response.code']
  ```

- [`or`](https://www.elastic.co/guide/en/beats/filebeat/current/defining-processors.html#condition-or)

  ```json
  or:
    - <condition1>
    - <condition2>
    - <condition3>
    ...
  -----------------------------
  or:
    - equals:
        http.response.code: 304
    - equals:
        http.response.code: 404
  ```

- [`and`](https://www.elastic.co/guide/en/beats/filebeat/current/defining-processors.html#condition-and)

  ```json
  and:
    - <condition1>
    - <condition2>
    - <condition3>
    ...
  -----------------------------
  and:
    - equals:
        http.response.code: 200
    - equals:
        status: OK
  -----------------------------
  or:
   - <condition1>
   - and:
      - <condition2>
      - <condition3>
  ```

- [`not`](https://www.elastic.co/guide/en/beats/filebeat/current/defining-processors.html#condition-not)

  ```json
  not:
    <condition>
  --------------
  not:
    equals:
      status: OK
  ```

##  支持的Processors 

| 类型 | 作用 | 配置样例 |
| ------------ | ----------------------------- | |
| [`add_cloud_metadata`](https://www.elastic.co/guide/en/beats/filebeat/current/add-cloud-metadata.html) | | |
| [`add_docker_metadata`](https://www.elastic.co/guide/en/beats/filebeat/current/add-docker-metadata.html) | | processors:<br/> - add_docker_metadata:<br/>       host: "unix:///var/run/docker.sock" |
| [`add_fields`](https://www.elastic.co/guide/en/beats/filebeat/current/add-fields.html) | | processors:<br/>- add_fields:<br/>       target: project<br/>       fields:<br/>           name: myproject<br/>           id: '574734885120952459' |
| [`add_host_metadata`](https://www.elastic.co/guide/en/beats/filebeat/current/add-host-metadata.html) | | processors:<br/> - add_host_metadata:<br/>       netinfo.enabled: false<br/>       cache.ttl: 5m<br/>       geo:<br/>           name: nyc-dc1-rack1<br/>           location: 40.7128, -74.0060<br/>           continent_name: North America<br/>           country_iso_code: US<br/>           region_name: New York<br/>           region_iso_code: NY<br/>           city_name: New York |
| [`add_kubernetes_metadata`](https://www.elastic.co/guide/en/beats/filebeat/current/add-kubernetes-metadata.html) | | processors:<br/> - add_kubernetes_metadata:<br/>       host: <hostname><br/>       kube_config: ~/.kube/config<br/>       default_indexers.enabled: false<br/>       default_matchers.enabled: false<br/>       indexers:<br/>           - ip_port: <br/>       matchers:<br/>           - fields:<br/>                  lookup_fields: ["metricset.host"] |
| [`add_labels`](https://www.elastic.co/guide/en/beats/filebeat/current/add-labels.html) | | processors:<br/>- add_labels:<br/>       labels:<br/>           number: 1<br/>           with.dots: test<br/>           nested:<br/>                with.dots: nested<br/>           array:<br/>                - do<br/>                - re<br/>                - with.field: mi |
| [`add_locale`](https://www.elastic.co/guide/en/beats/filebeat/current/add-locale.html) | | processors:<br/>- add_locale: ~<br/>processors:<br/>- add_locale:<br/>        format: abbreviation |
| [`add_observer_metadata`](https://www.elastic.co/guide/en/beats/filebeat/current/add-observer-metadata.html) | | |
| [`add_process_metadata`](https://www.elastic.co/guide/en/beats/filebeat/current/add-process-metadata.html) | | |
| [`add_tags`](https://www.elastic.co/guide/en/beats/filebeat/current/add-tags.html) | | processors:<br/>- add_tags:<br/>       tags: [web, production]<br/>       target: "environment" |
| [`community_id`](https://www.elastic.co/guide/en/beats/filebeat/current/community-id.html) | | |
| [`convert`](https://www.elastic.co/guide/en/beats/filebeat/current/convert.html) | | processors:<br/>   - convert:<br/>         fields:<br/>             - {from: "src_ip", to: "source.ip", type: "ip"}<br/>            - {from: "src_port", to: "source.port", type: "integer"}<br/>         ignore_missing: true<br/>         fail_on_error: false |
| [`decode_base64_field`](https://www.elastic.co/guide/en/beats/filebeat/current/decode-base64-field.html) | | |
| [`decode_cef`](https://www.elastic.co/guide/en/beats/filebeat/current/processor-decode-cef.html) | | |
| [`decode_csv_fields`](https://www.elastic.co/guide/en/beats/filebeat/current/decode-csv-fields.html) | | |
| [`decode_json_fields`](https://www.elastic.co/guide/en/beats/filebeat/current/decode-json-fields.html) | | |
| [`decompress_gzip_field`](https://www.elastic.co/guide/en/beats/filebeat/current/decompress-gzip-field.html) | | |
| [`dissect`](https://www.elastic.co/guide/en/beats/filebeat/current/dissect.html) | | processors:<br/>- dissect:<br/>       tokenizer: "%{key1} %{key2}"<br/>       field: "message"<br/>       target_prefix: "dissect" |
| [`dns`](https://www.elastic.co/guide/en/beats/filebeat/current/processor-dns.html) | | |
| [`drop_event`](https://www.elastic.co/guide/en/beats/filebeat/current/drop-event.html) | | processors:<br>- drop_event:<br/>         when:<br/>               condition |
| [`drop_fields`](https://www.elastic.co/guide/en/beats/filebeat/current/drop-fields.html) | | processors:<br/>- drop_fields:<br/>        when:<br/>               condition<br/>         fields: ["field1", "field2", ...]<br/>         ignore_missing: false |
| [`extract_array`](https://www.elastic.co/guide/en/beats/filebeat/current/extract-array.html) | | processors:<br/>  - extract_array:<br/>        field: my_array<br/>        mappings:<br/>             source.ip: 0<br/>             destination.ip: 1<br/>             network.transport: 2 |
| [`include_fields`](https://www.elastic.co/guide/en/beats/filebeat/current/include-fields.html) | | processors:<br/> - include_fields:<br/>        when:<br/>              condition<br/>        fields: ["field1", "field2", ...] |
| [`registered_domain`](https://www.elastic.co/guide/en/beats/filebeat/current/processor-registered-domain.html) | | |
| [`rename`](https://www.elastic.co/guide/en/beats/filebeat/current/rename-fields.html) | | processors:<br/> - rename:<br/>       fields:<br/>          - from: "a.g"<br/>             to: "e.d"<br/>       ignore_missing: false<br/>       fail_on_error: true |
| [`script`](https://www.elastic.co/guide/en/beats/filebeat/current/processor-script.html) | | |
| [`timestamp`](https://www.elastic.co/guide/en/beats/filebeat/current/processor-timestamp.html) | | |



# 八. Pipeline



# 九. Modules



Filebeat modules simplify the collection, parsing, and visualization of common log formats.

A typical module (say, for the Nginx logs) is composed of one or more filesets (in the case of Nginx, `access` and `error`). A fileset contains the following:

- Filebeat input configurations, which contain the default paths where to look for the log files. These default paths depend on the operating system. The Filebeat configuration is also responsible with stitching together multiline events when needed.
- Elasticsearch [Ingest Node](https://www.elastic.co/guide/en/elasticsearch/reference/7.4/ingest.html) pipeline definition, which is used to parse the log lines.
- Fields definitions, which are used to configure Elasticsearch with the correct types for each field. They also contain short descriptions for each of the fields.
- Sample Kibana dashboards, when available, that can be used to visualize the log files.

Filebeat automatically adjusts these configurations based on your environment and loads them to the respective Elastic stack components.

Filebeat modules require Elasticsearch 5.2 or later.



## Modules管理

### 1. 查看所有Modules

```bash
filebeat modules list
```

### 2、开启Modules

```bash
filebeat modules enable module名
```





## 支持的Modules

| 类型 | 作用 | 配置样例 |
| ------------ | ----------------------------- | |
| [*Modules overview*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-modules-overview.html) | | |
| [*Apache module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-apache.html) | | |
| [*Auditd module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-auditd.html) | | |
| [*AWS module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-aws.html) | | |
| [*CEF module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-cef.html) | | |
| [*Cisco module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-cisco.html) | | |
| [*Coredns Module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-coredns.html) | | |
| [*Elasticsearch module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-elasticsearch.html) | | |
| [*Envoyproxy Module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-envoyproxy.html) | | |
| [*Google Cloud module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-googlecloud.html) | | |
| [*haproxy module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-haproxy.html) | | |
| [*IBM MQ module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-ibmmq.html) | | |
| [*Icinga module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-icinga.html) | | |
| [*IIS module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-iis.html) | | |
| [*Iptables module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-iptables.html) | | |
| [*Kafka module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-kafka.html) | | |
| [*Kibana module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-kibana.html) | | |
| [*Logstash module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-logstash.html) | | |
| [*MongoDB module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-mongodb.html) | | |
| [*MSSQL module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-mssql.html) | | |
| [*MySQL module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-mysql.html) | | |
| [*nats module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-nats.html) | | |
| [*NetFlow module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-netflow.html) | | |
| [*Nginx module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-nginx.html) | | |
| [*Osquery module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-osquery.html) | | |
| [*Palo Alto Networks module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-panw.html) | | |
| [*PostgreSQL module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-postgresql.html) | | |
| [*RabbitMQ module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-rabbitmq.html) | | |
| [*Redis module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-redis.html) | | |
| [*Santa module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-santa.html) | | |
| [*Suricata module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-suricata.html) | | |
| [*System module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-system.html) | | |
| [*Traefik module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-traefik.html) | | |
| [*Zeek (Bro) Module*](https://www.elastic.co/guide/en/beats/filebeat/current/filebeat-module-zeek.html) | | |



```json
[{"source":"/root/mysql-slow-sql-log/mysql-slowsql.log","offset":1365442,"timestamp":"2019-10-11T09:29:35.185399057+08:00","ttl":-1,"type":"log","meta":null,"FileStateOS":{"inode":2360926,"device":2051}}]
```



```
source  记录采集日志的完整路径
offset　已经采集的日志的字节数;已经采集到日志的哪个字节位置
timestamp　　日志最后一次发生变化的时间戳
ttl　　采集失效时间，-1表示只要日志存在，就一直采集该日志
type: log
meta
filestateos　　操作系统相关
　　inode　　日志文件的inode号
　　device    日志所在磁盘的磁盘编号
```

硬盘格式化的时候，操作系统自动将硬盘分成了两个区域。

一个是数据区，用来存放文件的数据信息

一个是inode区，用来存放文件的元信息，比如文件的创建者、创建时间、文件大小等等

每一个文件都有对应的inode，里边包含了与该文件有关的一些信息，可以用`stat`命令查看文件的inode信息

```
> stat /var/log/messages
  File: ‘/var/log/messages’
  Size: 56216339        Blocks: 109808     IO Block: 4096   regular file
Device: 803h/2051d      Inode: 1053379     Links: 1
Access: (0600/-rw-------)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2019-10-06 03:20:01.528781081 +0800
Modify: 2019-10-12 13:59:13.059112545 +0800
Change: 2019-10-12 13:59:13.059112545 +0800
 Birth: -
```

2051为十进制数，对应十六进制数803





# 参考链接

1. https://www.cnblogs.com/micmouse521/p/8085229.html