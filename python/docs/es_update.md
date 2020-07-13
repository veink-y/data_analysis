## 批量删除和条件删除某字段
```
POST /index/_update_by_query
{
  "query": {
    "match_all": {}
  }, // 条件
  "script": {
      "inline":"ctx._source.remove(\"some_column\") "
} //删除some_column这个字段
}
```