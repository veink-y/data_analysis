

来源：[廖雪峰的SQL教程](https://www.liaoxuefeng.com/wiki/1177760294764384)

## 主键

主键是关系表中记录的唯一标识。主键的选取非常重要：主键不要带有业务含义，而应该使用BIGINT自增或者GUID类型。主键也不应该允许`NULL`。可以使用多个列作为联合主键，但联合主键并不常用。

```sql
Alter table tb add primary key(id);    -- 添加主键
Alter table tb change id id int(10) not null auto_increment=1;   -- 自增主键
alter table tableName drop primary key;  -- 删除主键
```



## 外键

关系数据库通过外键可以实现一对多、多对多和一对一的关系。外键既可以通过数据库来约束，也可以不设置约束，仅依靠应用程序的逻辑来保证。

多对多：通过设立中间表实现

```SQL
ALTER TABLE students           -- 修改表
ADD CONSTRAINT fk_class_id      -- 外键约束的名称
FOREIGN KEY (class_id)        -- 外键列
REFERENCES classes (id);         -- 关联到什么表的什么列

DROP FOREIGN KEY fk_class_id    -- 删除外键
DROP COLUMN …       --删除列
```

## 索引：优化数据库，提高查询速度

```SQL
ALTER TABLE students            
ADD INDEX idx_score (score);    -- 创建名为idx_score使用score列的索引

ALTER TABLE students
ADD INDEX idx_name_score (name, score);   -- 多列索引
```

对于主键，关系数据库会自动对其创建主键索引。使用主键索引的效率是最高的，因为主键会保证绝对唯一。

添加唯一索引

```SQL
ALTER TABLE students
ADD UNIQUE INDEX uni_name (name);    -- 添加唯一索引主键
```

唯一约束：没有索引，但具有唯一性保证

```SQL
ALTER TABLE students
ADD CONSTRAINT uni_name UNIQUE (name);    -- 添加唯一约束：没有索引，但具有唯一性保证
```

# 查询数据

## 查询表

使用SELECT \* FROM students时，SELECT是关键字，表示将要执行一个查询，\*表示“所有列”，FROM表示将要从哪个表查询

```SQL
SELECT * FROM <表名> # *号可以替换为列名
SELECT 1 #测试数据库连接
```

许多检测工具会执行一条SELECT 1;来测试数据库连接。

计算表达式

```SQL
SELECT 100+200
```

## 条件查询

```SQL
SELECT * FROM students WHERE score >= 80;
```

WHERE 后跟随条件

```SQL
SELECT * FROM <表名> WHERE <条件表达式> #条件查询语法
```

选择多个条件

```SQL
<条件1> AND <条件2>
<条件1> OR <条件2>
NOT <条件>
```

NOT class\_id = 2其实等价于class\_id &lt;&gt; 2

要组合三个或者更多的条件，就需要用小括号\(\)表示如何进行条件运算。eg：

```SQL
SELECT * FROM students WHERE (score < 80 OR score > 90) AND gender = 'M';
```

如果不加括号条件运算按照NOT、AND、OR的优先级进行

## 投影查询

如果我们只希望返回某些列的数据，而不是所有列的数据，我们可以用SELECT 列1, 列2, 列3 FROM ...，让结果集仅包含指定列。这种操作称为投影查询

投影查询别名

```SQL
SELECT 列1 别名1, 列2 别名2, 列3 别名3 FROM ...
```

## 排序

语法： ORDER BY 列名\(加上DESC表示“倒序”\)

```SQL
SELECT id, name, gender, score FROM students ORDER BY score;
SELECT id, name, gender, score FROM students ORDER BY score DESC;
```

进一步排序：

```SQL
SELECT id, name, gender, score FROM students ORDER BY score DESC, gender;
```

如果有WHERE子句，那么ORDER BY子句要放到WHERE子句后面

## 分页查询

语法

```SQL
LIMIT <M> OFFSET <N>
 #M填最多显示数量、N填起始位置（此处从0开始计算位置）
```

LIMIT 15 OFFSET 30还可以简写成LIMIT 30 15

## 聚合搜索

COUNT  SUM  MAX  MIN  AVG

分组聚合：GROUP BY

如果聚合查询的WHERE条件没有匹配到任何行，COUNT\(\)会返回0，而MAX\(\)、MIN\(\)、MAX\(\)和MIN\(\)会返回NULL：

```
SELECT COUNT(*) FROM students;
SELECT COUNT(*) num FROM students;  #重命名计算的行
SELECT COUNT(*) boys FROM students WHERE gender = 'M';  #Where条件查询并计算行
SELECT COUNT(*) num FROM students GROUP BY class_id;   #分组聚合
SELECT class_id, COUNT(*) num FROM students GROUP BY class_id;   #分组聚合并按照id生成新的数组
SELECT class_id, gender, COUNT(*) num FROM students GROUP BY class_id, gender; #多个分组聚合
```

## 多表查询

查询结果是列之和，行之积

```SQL
SELECT
    s.id sid,    #给列名设置别名防止列名重复
    s.name,
    s.gender,
    s.score,
    c.id cid,
    c.name cname
FROM students s, classes c  #给表名设置别名
WHERE s.gender = 'M' AND c.id = 1;   #添加where 条件查询
```

## 连接查询

语法：

1. 先确定主表，仍然使用`FROM<表1>`的语法；
2. 再确定需要连接的表，使用`INNER JOIN<表2>`的语法；
3. 然后确定连接条件，使用`ON<条件...>`，这里的条件是`s.class_id = c.id`，表示`students`表的`class_id`列与`classes`
   表的`id`列相同的行需要连接；
4. 可选：加上`WHERE`子句、`ORDER BY`等子句。

内连接 语法： `SELECT ... FROM <表1> INNER JOIN <表2> ON <条件...>`

eg： 内连接

```SQL
SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
FROM students s             #确定主表
INNER JOIN classes c        #确定需要连接的表
ON s.class_id = c.id;        #确定连接条件
```

eg：外连接

```SQL
SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
FROM students s
RIGHT OUTER JOIN classes c
ON s.class_id = c.id;
```

各种连接类型区别：

INNER JOIN只返回同时存在于两张表的行数据，由于`students`表的`class_id`包含1，2，3，`classes`表的`id`包含1，2，3，4，所以，INNER JOIN根据条件`s.class_id = c.id`返回的结果集仅包含1，2，3。 **（交集）**

RIGHT OUTER JOIN返回右表都存在的行。如果某一行仅在右表存在，那么结果集就会以`NULL`填充剩下的字段。**（交集+副表）**

LEFT OUTER JOIN则返回左表都存在的行。如果我们给students表增加一列，并添加class\_id=5，由于classes表并不存在id=5的列，所以，LEFT OUTER JOIN的结果会增加一列，对应的`class_name`是`NULL`：**（交集+主表）**

FULL OUTER JOIN，它会把两张表的所有记录全部选择出来，并且，自动把对方不存在的列填充为NULL：**（并集）**

# 修改数据

关系数据库的基本操作就是增删改查，即CRUD：Create、Retreive、Update、Delete。

对应SQL语句：

* INSERT：插入新记录；
* UPDATE：更新已有记录；
* DELETE：删除已有记录

## INSERT

语法： `INSERT INTO <表名> (字段1, 字段2, ...) VALUES (值1, 值2, ...);`

eg：

```SQL
INSERT INTO students (class_id, name, gender, score) VALUES (2, '大牛', 'M', 80);
-- 查询并观察结果:
SELECT * FROM students;
```

```SQL
INSERT INTO students (class_id, name, gender, score) VALUES
  (1, '大宝', 'M', 87),
  (2, '二宝', 'M', 81);      -- 一次性添加多条记录

SELECT * FROM students;
```

## UPDATE

语法：`UPDATE <表名> SET 字段1=值1, 字段2=值2, ... WHERE ...;`

eg：

```SQL
UPDATE students SET name='大牛', score=66 WHERE id=1;
-- 查询并观察结果:  id=1 代表需要更新的行
SELECT * FROM students WHERE id=1;
```

UPDATE语句的WHERE条件和SELECT语句的WHERE条件其实是一样的，因此完全可以一次更新多条记录：eg：

```SQL
UPDATE students SET name='小牛', score=77 WHERE id>=5 AND id<=7;
--选择不同的行
SELECT * FROM students;
```

在UPDATE语句中，更新字段时可以使用表达式。

```SQL
UPDATE students SET score=score+10 WHERE score<80;
-- 查询并观察结果: -- 例如，把所有80分以下的同学的成绩加10分：
SELECT * FROM students;
```

如果WHERE条件没有匹配到任何记录，UPDATE语句不会报错，也不会有任何记录被更新

_**UPDATE没有添加WHERE条件会更新整个表，最好先用SELECT...WHERE...来确认 筛选出了期望的记录值**_

## DELETE

语法： `DELETE FROM <表名> WHERE ...;`

eg：

```SQL
DELETE FROM students WHERE id=1;
-- 查询并观察结果:
SELECT * FROM students;
```

不带WHERE条件的DELETE语句会删除整个表的数据



# MySQL

mysql启动：`mysql -u root -p`

断开mysql服务器： `exit`

MySQL Client的可执行程序是mysql，MySQL Server的可执行程序是mysqld。

本机MySQL地址： `127.0.0.1:3306`

连接远程MySQL： `mysql -h 10.0.1.99 -u root -p`

## 管理MySQL

可视化图形界面： MySQL Workbench

列出所有数据库： `SHOW DATABASES`

information\_schema、mysql、performance\_schema和sys是系统库，不要去改动它们。其他的是用户创建的数据库。

数据库

创建数据库： `CREATE DATABASE test; --创建名为test的数据库`

删除数据库： `DROP DATABASE test; --删除名为test的数据库`

切换到当前数据库： `USE test： --对名为test的数据库进行操作`

表

列出所有的表： `SHOW TABLES;`

查看表结构： `DESC students；`

查看创建表的SQL语句： `SHOW CREATE TABLE students;`

删除表： `DROP TABLE students;`

修改表：

```SQL
`ALTER TABLE students ADD COLUMN birth VARCHAR(10) NOT NULL;
--给students表新增一列birth
ALTER TABLE students CHANGE COLUMN birth birthday VARCHAR(20) NOT NULL;
--将birth列名修改为birthday，饼更改类型为VARCHAR(20)
ALTER TABLE students DROP COLUMN birthday;
--删除列`
```

## 实用SQL语句

插入或替换

```SQL
REPLACE INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99);
--若id=1的记录不存在，REPLACE语句将插入新记录，否则，当前id=1的记录将被删除，然后再插入新记录。
```

插入或更新

语法： `INSERT INTO ... ON DUPLICATE KEY UPDATE ...`

```SQL
INSERT INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99) ON DUPLICATE KEY UPDATE name='小明', gender='F', score=99;
--若id=1的记录不存在，INSERT语句将插入新记录，否则，当前id=1的记录将被更新，更新的字段由UPDATE指定。
```

插入或忽略

语法：`INSERT IGNORE INTO ...`

```SQL
INSERT IGNORE INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99);
--若id=1的记录不存在，INSERT语句将插入新记录，否则，不执行任何操作。
```

快照：复制一份当前表的数据到一个新表

```SQL
-- 对class_id=1的记录进行快照，并存储为新表students_of_class1:
CREATE TABLE students_of_class1 SELECT * FROM students WHERE class_id=1;
```

写入查询结果集

```SQL
--创建一个统计成绩的表statistics，记录各班的平均成绩：
CREATE TABLE statistics (
    id BIGINT NOT NULL AUTO_INCREMENT,
    class_id BIGINT NOT NULL,
    average DOUBLE NOT NULL,
    PRIMARY KEY (id)
);
--用一条语句写入各班的平均成绩：
INSERT INTO statistics (class_id, average) SELECT class_id, AVG(score) FROM students GROUP BY class_id;
```

# 事务

把多条语句作为一个整体进行操作的功能，被称为数据库事务。

对于单条SQL语句，数据库系统自动将其作为一个事务执行，这种事务被称为\*`隐式事务`\*。

要手动把多条SQL语句作为一个事务执行，使用`BEGIN`开启一个事务，使用`COMMIT`提交一个事务，这种事务被称为\*`显式事务`\*

```SQL
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

用ROLLBACK回滚事务，整个事务会失败：

