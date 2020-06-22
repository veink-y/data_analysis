# python 骚操作

### jupyter 自定义魔法命令

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





神奇的内置函数：

eval：将字符串转化为可执行python语句

dir：列出当前对象的所有属性

