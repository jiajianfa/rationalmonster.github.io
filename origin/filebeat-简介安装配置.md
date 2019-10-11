# Filebeat的简介、安装、配置、Pipeline

# 一、简介

Filebeat由两个主要组件组成：

- **Inputs**：

  

- **Harvesters**：

  一个harvester负责读取一个单个文件的内容，每个文件启动一个harvester。harvester逐行读取每个文件（一行一行地读取每个文件），并把这些内容发送到输出。在harvester正在读取文件内容的时候，文件被删除或者重命名了，那么Filebeat会续读这个文件。这就有一个问题了，就是只要负责这个文件的harvester没用关闭，那么磁盘空间就不会释放。默认情况下，Filebeat保存文件打开的状态直到close_inactive到达。





# 二、安装





# 三、配置



# 四、Pipeline

[{"source":"/root/mysql-slow-sql-log/mysql-slowsql.log","offset":1365442,"timestamp":"2019-10-11T09:29:35.185399057+08:00","ttl":-1,"type":"log","meta":null,"FileStateOS":{"inode":2360926,"device":2051}}]



