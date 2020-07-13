## 批量删除和条件删除某字段
```json
POST /index/_update_by_query
{
  "script" : "ctx._source.remove(\"name\")",
  "query": {
    "bool": {
      "must": [
        {
          "exists": {
            "field": "name"
          }
        }
      ]
    }
  }
}
```
