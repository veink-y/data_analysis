# Pandas 骚操作集锦

“”“

df： dataframe

column： 列名

”“”

### Top n 问题

```python
df["column1"].groupby(df["column2"]).head(n)  # 求分窗前n
```



### Groupby 自定义聚合函数

> 日常操作，自定义聚合函数

```python
def function():
	pass

df.groupby(["columns"]).agg({"column2":function})
```



### 分桶

> 便捷对数据进行分割为类别

```python
bins = [0,1,2,3,4,5]
labels = ["zero","one","two","three","four","five"]
df["column2"] = pd.cut(df["column1"],bins,labels=labels)
```



### 分窗排序

> top n 问题

```python
df['row_number'] = df['A'].groupby(df['C']).rank(ascending=True,method='first')
```



### 快速重编码替换数据：

> 机器学习时快速对类别数据进行数字化编码
>
> 另一种处理思路是 one-hot 编码 pd.get_dummied

```python
test_df = pd.DataFrame([["one","one"],["two","two"],["three","three"]],columns=["column1","column2"])
column_encode = {"one":1,"two":2,"three":3}
test_df.column2 = test_df.column2.map(column_encode)
print(test_df)
```

结果：

|      | column1 | column2 |
| ---: | ------: | ------- |
|    0 |     one | 1       |
|    1 |     two | 2       |
|    2 |   three | 3       |

