
原文链接：https://blog.csdn.net/u012643122/article/details/52890772

## 主键

### 建表设置主键

```sql
create table tableName(
    id int primary key
);
或：
create table tableName(
    id int,
    primary key (id)
);
```

### 单独设置主键

```sql
alter table tablename add primary key (id);
```

### 删除主键

```sql
alter table tablename drop primary key ;
```



## 外键

### 建表添加外键

```sql
create table tableName1(
    tableName2_id int not null,
    foreign key(tableName2_id) references tableName2(id)
);
```

单独添加外键

```sql
alter table tableName1 
add constraint tableName1_ref_tableName2(foreignKeyName) 
foreign key(tableName2_id) references tableName2(id);
```

### 删除外键

```sql
alter table table1 drop foreign key foreignKeyName;
```



## 唯一键

### 建表时添加唯一键

```sql
create table tableName(
    columnName int unique
);
或：
create table tableName(
    columnName int,
    unique key(columnName)
);
```

单独添加唯一键

```sql
alter table tableName add unique key(columnName)
```

删除唯一键

```sql
alter table tableName drop index columnName;
```



## 索引

大部分情况下，mysql中的索引index和键key同义

### 建表时添加索引

```sql
create table tableName(
    columnName int key//这里只能用key不能用index
);
或：
create table tableName(
    columnName int,
    key/index (columnName)
);
或：
create table tableName(
    columnName int,
    key/index indexName(columnName)
);
多列索引：
create table tableName(
    columnName1 int,
    columnName2 int,
    key/index indexName(columnName1,columnName2)
);
```

### 单独添加索引

```sql
alter table tableName add key/index indexName(columnName)//单列索引
alter table tableName add key/index indexName(columnName1,columnName2)//多列索引
```

### 删除索引

```sql
alter table tableName drop key/index columnName;
```



## 自增

auto_increment必须要求该列是主键

### 建表时添加自增

```sql
create table tableName(
    columnName int unique auto_increment
);
或：
create table tableName(
    columnName int primary key auto_increment
);
```

### 单独添加自增

```sql
alter table tableName change columnName columnName int unique auto_increment;
或：
alter table tableName change columnName columnName int primary key auto_increment;
```



### 删除自增

```sql
alter table tableName change columnName columnName int;
```

### 设置自增初始值

```sql
create table tableName(
    columnName int primary key auto_increment
)auto_increment=1;
或：
alter table tableName auto_increment=1;
```

