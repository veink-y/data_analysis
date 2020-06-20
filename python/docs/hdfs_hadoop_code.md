
## ls 

```shell
# 查看hdfs根目录
hdfs dfs -ls /

# 查看hdfs其它目录(/user/zhang)
hdfs dfs -ls /user/zhang
```

## du

```shell
hdfs dfs -du /user/zhang

# 将字节自动转化成单位
hdfs dfs -du -h /user/zhang
```

## df

```shell
# 查看根目录磁盘空间
hdfs dfs -df /
```

## put 

```shell
# 将client node中的NOTICE.txt文件 put 到hdfs的/user/zhang文件夹中
hdfs dfs -put NOTICE.txt /user/zhang

# 如果文件存在，可以使用-f参数进行覆盖
hdfs dfs -put -f NOTICE.txt /user/zhang
```

## get

```shell
# 获取hdfs中的/user/zhang/test/README.txt.gz文件，到client node当前目录

hdfs dfs -get /user/zhang/test/README.txt.gz

和copyToLocal命令一样
```

## copyFromLocal

```shell
# 这个命令和put作用相同
hdfs dfs -copyFromLocal LICENSE.txt /user/zhang

如果存在LCENSE.txt文件，要进行覆盖，加上-f参数，和put命令一样
```

## opyToLocal 

```shell
# 复制hdfs的/user/zhang/test/README.txt文件到client node当前目录
hdfs dfs -copyToLocal /user/zhang/test/README.txt


# 如果需要复制到指定目录
hdfs dfs -copyToLocal /user/zhang/test/README.txt /指定目录
```

## cp

```shell
# 将/user/zhang/NOTICE.txt 复制到 /user/zhang/test目录
hdfs dfs -cp /user/zhang/NOTICE.txt /user/zhang/test
```

## mkdir

```shell
# 创建一个abc目录
hdfs dfs -mkdir /user/zhang/abc
```

## rm

```shell
# 删除/user/zhang/NOTICE.txt文件
hdfs dfs -rm /user/zhang/NOTICE.txt

# 删除/user/zhang/abc目录
hdfs dfs -rm -r /user/zhang/abc
```

## rmdir

```shell
# 删除/user/zhang/demo目录
hdfs dfs rmdir /user/zhang/demo
```

## cat 命令

```shell
# 查看文件
hdfs dfs -cat /user/zhang/test/README.txt
```

## tail

```shell
hdfs dfs -tail /user/zhang/test/README.txt
```

## mv

```shell
hdfs dfs -mv /user/zhang/test /user/zhang/test2
```

