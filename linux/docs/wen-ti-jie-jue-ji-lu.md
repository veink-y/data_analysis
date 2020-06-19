
## 知乎SQL代码无法发布

在知乎发表文章的时候：

\`CONCAT\`和\`FROM\`函数都必须要添加符号“\`”符号

## Matplotlib中文乱码

查看支持字体

```py
from matplotlib.font_manager import FontManager
fm = FontManager()
mat_fonts = set(f.name for f in fm.ttflist)
print (mat_fonts)
```

Mac

```py
import matplotlib.pyplot as plt
plt.rcParams['font.sans-serif'] = ['Arial Unicode MS']
plt.rcParams['axes.unicode_minus']=False​
```

Windwos：

```py
import matplotlib.pyplot as plt
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus']=False
```

## mysql 外网访问

```
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
```

解决：/etc/my.cnf 下\[mysqld\]后添加 `skip-grant-tables`

```
ERROR 1290 (HY000): The MySQL server is running with the --skip-grant-tables option so it cannot execute this statement
```

解决：刷新权限： `flush privileges;`



## Mackettle无法打开

```
sudo xattr -dr com.apple.quarantine /path/to/Data\ Integration.app
```

