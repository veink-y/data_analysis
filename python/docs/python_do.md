

## jupyter 自定义魔法命令

```python
from IPython.core.magic import (register_line_magic, register_cell_magic,
                                register_line_cell_magic,)
@register_line_magic  # 行魔术命令
def lmagic(line):
    "my line magic"
    return line
del lmagic
```

调用方式：

```
%lmagic 123
```

jupyter 启动前会运行的文件位置：

```
.ipython\profile_default\startup  # 该文件夹下的py文件每次会预先运行
```

使用魔法命令进行 pyspark 环境配置：



pyspark导入：



eval：将字符串转化为可执行python语句



## 好玩的自定义函数
eval:
```python
eval('t=5')
print(t)
>>> 5
```

locals:

```python
locals()['t']=5  # 可用于生成变量
print(t)
```

dir:

```python
dir(t) ##列出当前变量可使用的方法，jupyter使用方便
```

