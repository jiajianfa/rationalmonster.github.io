# Elasticsearch的Index索引Restful API 

`<REST Verb> /<Index>/<Type>/<ID>`

# 一、 **DDL 数据定义** 

## 1.  创建索引

**Kibana Dev Tools Console**

```bash
PUT /index_name?pretty
```

  **Curl命令**

```bash
curl -XPUT "http://localhost:9200/index_name?pretty"
```

## 2. 向索引中插入一个文档

向索引中插入一个ID为1的文档

- **Kibana Dev Tools Console**

  ```json
  PUT /index_name/_doc/1 
  {
    "name": "test"
   } 
  ```

- **Curl命令**

  ```json
  curl -XPUT "http://localhost:9200/test/_doc/1" \
  -H 'Content-Type: application/json' \
  -d'{  "name": "test" }'
  ```

## 3. 向索引中批量插入文档

详见[Elasticsearch索引文档批量操作](../origin/elasticsearch-bulk-api.md)

# 二、 **DQL 数据查询** 

| 功能             | Kibana Dev Tools Console命令   | 其他 | Curl命令                                       |
| ---------------- | ------------------------------ | ---- | ---------------------------------------------- |
| 查看索引中的文档 | GET /index_name/_search?pretty |      | curl -XPUT "http://localhost:9200/test?pretty" |
|                  |                                |      |                                                |
|                  |                                |      |                                                |

# 三、 **DML 数据操作** 





- **Kibana Dev Tools Console**

  ```json
  
  ```

- **Curl命令**

  ```json
  
  ```

  

