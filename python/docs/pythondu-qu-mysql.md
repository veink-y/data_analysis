## pymysql连接

```python
import pymysql
# 打开数据库链接
connection = pymysql.connect(host='localhost',
user='root',
password='********',
db='sql50',
port=3306)

# 使用cursor()获取游标
cursor = connection.cursor()
# SQL 查询语句
sql2 = "SELECT * FROM Student st JOIN Score B ON st.s_id = B.s_id JOIN Score C ON st.s_id = C.s_id WHERE B.c_id='01' AND C.c_id='02' AND B.s_score > C.s_score"

#执行SQL语句
cursor.execute(sql2)
```

2

```python
# 获取查询结果
cursor.fetchall() 
```

\(\('02', '钱电', '1990-12-21', '男', '02', '01', 70, '02', '02', 60\),

\('04', '李云', '1990-08-06', '男', '04', '01', 50, '04', '02', 30\)\)

```python
# 关闭数据库
connection.close()
```



