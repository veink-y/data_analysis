# postgresql操作指南

## 安装

link： https://www.postgresql.org/download/

参考教程： https://www.postgresqltutorial.com/

## 常用命令

数据库交互命令

```
\password           设置密码。
\q                  退出。
\h                  查看SQL命令的解释，比如\h select。
\?                  查看psql命令列表。
\l                  列出所有数据库。
\c [database_name]  连接其他数据库。
\d                  列出当前数据库的所有表格。
\d [table_name]     列出某一张表格的结构。
\du                 列出所有用户。
\e                  打开文本编辑器。
\conninfo           列出当前数据库和连接的信息。
```

### 用户相关

```
CREATE USER dbuser WITH PASSWORD 'password';
\password dbuser  修改密码
```

### 切换不同模式

```
 show search_path; 查看模式
 set search_path = schema_name; 修改要使用的模式
```





## 备份恢复

```
pg_dump --format=t -d db_name -U user_name -W -h 127.0.0.1 > dump.sql
pg_restore -d db_name -h 127.0.0.1 -U user_name < dump.sql
```
