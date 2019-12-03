# Elasticsearch基础知识

# Document 文档

- **Elasticsearch 是面向Document文档的，文档是所有可搜索数据的最小单位**

- **文档会被序列化JSON格式，保存在Elasticsearch中**
  - JSON对象由字段组成
  - 每个字段都有对应的字段类型 (字符串/数值/布尔/日期/二进制/范围类型)

- **每个文档都有一个Unique ID**
  - 手动指定ID
  - 通过Elasticsearch自动生成
- **一个文档由Meta Data元数据与Source Data原始数据组成**

```json
{
  "_index": "***",					# 文档所存在的索引名
  "_type": "_doc",					# 文档所属的类型名
  "_id": "***",						# 文档的唯一ID
  "_version": 1,					# 文档的版本信息
  "_score": null,					# 文档相关性打分
  "_source": { .... },				# 文档的原始JSON数据
  "fields": { "***": [ "***" ] },	# 额外添加的字段
  "sort": [ 1575256044058 ]			# 排序
}
```

# Index 索引
