## pymysql连接

```python
import pymysql
# 打开数据库链接
connection = pymysql.connect(host='localhost',
                             user='root',
                             password='********',
                             db='sql50',
                             port=3306,
                             encoding='utf8mb4')
```

## cx_Oracle 连接
```python
import cx_Oracle
# 打开数据库链接
connection = cx_Oracle.connect(host='localhost',
                               user='root',
                               password='********',
                               db='sql50',
                               port=3306,
                               encoding='utf-8')
```

## redis连接
```python
import redis
pool = redis.ConnectionPool(host='127.0.0.1',password='helloworld')   #实现一个连接池
r = redis.Redis(connection_pool=pool)
```

## es连接
```python
from elasticsearch import  Elasticsearch
es = Elasticsearch(["0.0.0.0","0.0.0.0"], # 连接集群，以列表的形式存放各节点的IP地址
                    sniff_on_start=True,    # 连接前测试
                    sniff_on_connection_fail=True,  # 节点无响应时刷新节点
                    sniff_timeout=60 )   # 设置超时时间
```



