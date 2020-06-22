# ES聚合操作

### 例子：

```
# 求百分比占位的数字
{
  "size": 0,  # 不输出原文档
  "aggs": {
    "percent_price": {
      "percentiles": {
        "field": "AvgTicketPrice"
      }
    }
  }
}
```

### 函数

- max ：最大值
- min ：最小值
- sum ：求和
- avg ：平均值
- value_count ：值个数
- cardinality ：唯一值个数
- stats   ： 统计count max min avg sum 5个值
- extended_stats ：比stats多4个统计结果： 平方和、方差、标准差、平均值加/减两个标准差的区间
- percentiles ： 默认返回[1, 5, 25, 50, 75, 95, 99 ]分位上的值，指定"percents" : [95, 99, 99.9] 可返回任意百分位值
- percentile_ranks 统计小于等于指定值的文档占比



### 桶聚合（group by）

```
POST /bank/_search?size=0
{
  "aggs": {
    "age_terms": {
      "terms": {
        "field": "age",
         "missing": "N/A" ,
        "size": 20,
        "order": {
          "max_balance": "asc"
        }
      },
      "aggs": {
        "max_balance": {
          "max": {
            "field": "balance"
          }
        },
        "min_balance": {
          "min": {
            "field": "balance"
          }
        }
      }
    }
  }
}
```



### 范围聚合

```
POST /bank/_search?size=0
{
  "aggs": {
    "age_range": {
      "range": {
        "field": "age",
         "ranges": [
          {
            "to": 25,
            "key": "Ld"
          },
          {
            "from": 25,
            "to": 35,
            "key": "Md"
          },
          {
            "from": 35,
            "key": "Od"
          }
        ]
      },
      "aggs": {
        "bmax": {
          "max": {
            "field": "balance"
          }
        }
      }
    }
  }
}
```



### 时间聚合

```
POST /bank/_search?size=0
{
  "aggs": {
    "range": {
      "date_range": {
        "field": "date",
        "format": "MM-yyyy",
        "ranges": [
          {
            "to": "now-10M/M"
          },
          {
            "from": "now-10M/M"
          }
        ]
      }
    }
  }
}
```



### 时间直方图

```
POST /bank/_search?size=0
{
  "aggs": {
    "sales_over_time": {
      "date_histogram": {
        "field": "date",
        "interval": "month"
      }
    }
  }
}
```