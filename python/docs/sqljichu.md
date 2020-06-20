
mysql面试题：[mysql面试题](https://kl66.top/2019/05/23/mysql面试题/)

### 常见的存储引擎：

MyISAM、InnoDB、MERGE、MEMORY(HEAP)、BDB(BerkeleyDB)等，以及最常用的MyISAM与InnoDB两个引擎

InnoDB（mysql5.5以后默认使用）



## 数据库知识笔记

utf8mb4比utf8多了emoji编码支持.

分析型数据库：OLAP（联机分析处理）

操作型数据库：OLTP（联机事物处理）

特点：面向主题、集成不同源的数据库、历史性、时变性、稳定性

数据分析精细化就需要数据仓库：一个良好的数据记录

ETL：数据工程（抽取不同数据源的数据转换后加载到数据仓库）

数据集市： 面向不同部分、单个主题的小型数据仓库



主题： 事实表+维度表



模型-星型、雪花型



天源层 》数据仓库 》 数据集市 》 数据汇总层 》powerbi可视化
