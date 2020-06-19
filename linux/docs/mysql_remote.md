

###### 参考： 

[https://www.jianshu.com/p/1dab9a4d0d5f](https://www.jianshu.com/p/1dab9a4d0d5f)

[https://segmentfault.com/a/1190000015634108](https://segmentfault.com/a/1190000015634108)

```
use mysql;
update user set host='%' where user='root';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'WITH GRANT OPTION;
FLUSH PRIVILEGES;
```



