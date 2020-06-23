
## 知乎SQL代码无法发布


> \`CONCAT\`和\`FROM\`函数都必须要添加符号“\`”符号

## Matplotlib中文乱码

查看支持字体

```python
from matplotlib.font_manager import FontManager
fm = FontManager()
mat_fonts = set(f.name for f in fm.ttflist)
print (mat_fonts)
```

Mac

```python
import matplotlib.pyplot as plt
plt.rcParams['font.sans-serif'] = ['Arial Unicode MS']
plt.rcParams['axes.unicode_minus']=False​
```

Windwos：

```python
import matplotlib.pyplot as plt
plt.rcParams['font.sans-serif'] = ['SimHei']
plt.rcParams['axes.unicode_minus']=False
```

## mysql 外网访问

参考： 

[https://www.jianshu.com/p/1dab9a4d0d5f](https://www.jianshu.com/p/1dab9a4d0d5f)

[https://segmentfault.com/a/1190000015634108](https://segmentfault.com/a/1190000015634108)

```
use mysql;
update user set host='%' where user='root';
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'WITH GRANT OPTION;
FLUSH PRIVILEGES;
```



## Mackettle无法打开

```
sudo xattr -dr com.apple.quarantine /path/to/Data\ Integration.app
```

