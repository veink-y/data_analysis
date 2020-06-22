

## 模板操作

```
# 创建模板
PUT /_template/template_test
{
  "index_patterns": [
    "test*"
  ],
  "order": 0,
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 0
  },
  "mappings": {
    "date_detection": false,
    "numeric_detection": true,
     "properties": {
      "id": {
        "type": "long"
      },
      "name": {
        "type": "text"
      },
      "text": {
        "type": "keyword"
      }
    }
  }
}

# 获取模板
GET /_template/template_test
# 删除模板
DELETE /_template/template_test
```



## 索引操作

```json
# 创建索引
PUT /my_index?pretty
{
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 0
  },
  "mappings": {
    "properties": {
      "id": {
        "type": "long"
      },
      "name": {
        "type": "text"
      },
      "text": {
        "type": "keyword"
      }
    }
  }
}

# 查看索引相关信息
GET my_index

# 查看文档的映射信息，类似于sql的表字段信息
GET /my_index/_mapping

# 删除索引
DELETE my_index

# 增加一条数据
PUT /my_index/_doc/2
{
  "id":"2312321",
  "name":"lg",
  "text":"hoa are you"
}

# 查看索引文档总量
GET /my_index/_count

# 查看前10条数据
POST /my_index/_search
{}

# 查看_id为1的数据
GET my_index/_doc/1

# 通过query查询文档
POST /my_index/_search
{
  "query": {
    "term": {
      "name": {
        "value": "lg"
      }
    }
  }
}

# 修改文档,只能增量更新，且不存在时报错
POST my_index/_update/1
{
  "doc": {
    "id":"2134"
  }
}

# 删除索引某条记录
DELETE my_index/_doc/2

# 批量数据操作 _bulk
# index ： 不存在则创建，存在则替换
# create： 创建，存在则报错
# update:  更新数据，格式需要加上doc
# deltet:  删除一条记录
PUT _bulk
{"index":{"_index":"my_index","_id":"4"}}
{"id":"2131453","name":"dukun","text":"dsad"}
{"index":{"_index":"my_index","_id":"5"}}
{"id":"2453","name":"dukun","text":"dsad"}
{"create":{"_index":"my_index","_id":"3"}}
{"id":"21343243","name":"dukun","text":"dsad"}
{"update":{"_index":"my_index","_id":"3"}}
{"doc":{"id":21343243,"name":"dukun","text":"sda"}}
{"delete":{"_index":"my_index","_id":"4"}}
```

