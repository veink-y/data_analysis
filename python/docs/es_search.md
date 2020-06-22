# ES查询操作

### from / size

```
POST /movies/_search
{
  "query": {
    "match_all": {}
  },
  "from": 0,
  "size": 20
}
```

### sort排序

```
POST /movies/_search
{ "query": {
    "match_all": {}
  },
  "sort": [{"year": {"order": "desc"}},
          {"id.keyword": {"order": "desc"}} ],
  "from": 0,
  "size": 20

}
```

### _source 过滤

```
POST /movies/_search
{
  "sort": [{"year": {"order": "desc"}},
          {"id.keyword": {"order": "desc"}} ],
  "from": 0,
  "size": 20, 
  "_source": ["title"], 
  "query": {
    "match_all": {}
  }
}
```

### 脚本字段

用法： 用于计算

```
POST /movies/_search
{
  "sort": [{"year": {"order": "desc"}},
          {"id.keyword": {"order": "desc"}} ],
  "from": 0,
  "size": 20, 
  "query": {
    "match_all": {}
  }
  , "script_fields": {
    "new_title": {
      "script": {
        "lang": "painless",
         "source": "doc['year'].value+'hello'"
      }
    }
  }
}
```

### match表达式

```
POST /movies/_search
{
  "query": {
    "match": {
      "title": "last christmas"
    }
  }

#and 方式
POST /movies/_search
{
  "query": {
    "match": {
      "title": {
        "query": "last christmas",
        "operator": "and"
      }
      
    }
  }
```

### 短语查询

1. 使用slop=1来表示中间可以间隔一个单词

```
POST /movies/_search
{
  "query": {
    "match_phrase": {
      "title":{
         "query": "one love",
         "slop": 1
      }
    }
  }
}
```

### 返回指定列

```

POST /movies/_search
{
  "query": {
    "match": {
      "title": "last christmas"
    },
      "_source":["name"]
  }

```

### 排序

```
POST /kibana_sample_data_flights/_search
{
  "size": 2,
  "sort": [
    {
      "DistanceMiles": {
        "order": "desc"
      }
    }
  ]
}
```

