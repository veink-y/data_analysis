

## 关于adventure Works Cycles的公司信息

Adventure Works Cycles是AdventureWorks样本数据库所依据的公司，是一家大型跨国制造公司。该公司生产和销售金属和复合材料自行车到北美，欧洲和亚洲的商业市场。虽然其基地业务位于华盛顿州博塞尔，拥有290名员工，但几个区域销售团队遍布整个市场。

2000年，Adventure Works Cycles收购了一家位于墨西哥的小型制造工厂Importadores Neptuno。Importadores Neptuno为Adventure Works Cycles产品系列制造了几个关键子组件。这些子组件被运送到Bothell位置进行最终产品组装。2001年，Importadores Neptuno成为旅游自行车产品集团的唯一制造商和分销商。

## powerbi展示成果
[adventure时间可视化报告](https://app.powerbi.com/view?r=eyJrIjoiMDVmNDI0NDUtOGI3OS00NDBkLWI4ZDItOWVlOWU5YjYyMWNmIiwidCI6ImJiZDhhYWI5LWUyNTMtNDE3ZS04Njg3LWI3NGU3Y2U3Mjc4NyIsImMiOjN9)


[adventure趋势可视化报告](https://app.powerbi.com/view?r=eyJrIjoiNzZiYTQxZTAtZDMyMi00NzI3LWEwMDEtYWNlODVkMmZlZjU2IiwidCI6ImJiZDhhYWI5LWUyNTMtNDE3ZS04Njg3LWI3NGU3Y2U3Mjc4NyIsImMiOjN9)

[adventure区域销售量可视化](https://app.powerbi.com/view?r=eyJrIjoiNzZiYTQxZTAtZDMyMi00NzI3LWEwMDEtYWNlODVkMmZlZjU2IiwidCI6ImJiZDhhYWI5LWUyNTMtNDE3ZS04Njg3LWI3NGU3Y2U3Mjc4NyIsImMiOjN9)



## 项目介绍

项目数据来源：[https://docs.microsoft.com/en-us/previous-versions/sql/sql-server-2008/ms124438\(v%3dsql.100](https://docs.microsoft.com/en-us/previous-versions/sql/sql-server-2008/ms124438%28v%3dsql.100)\)

项目数据描述：数据来源于adventure Works Cycles公司的的样本数据库，包括了公司4大应用场景的数据：Sales、Finance、Product、Manufacture

项目任务：

1. 将数据导入Hive数据库
2. 探索数据库并罗列分析指标
3. 汇总数据建立数据仓库\(Sales主题\)
4. powerbi可视化

## 数据导入

数据源文件格式是csv+sql脚本，csv文件为utf-16编码，无表头

查看文件编码 代码：

```python
import chardet

# 获取文件编码类型
def get_encoding(file):
    # 二进制方式读取，获取字节数据，检测类型
    with open(file, 'rb') as f:
        encode = chardet.detect(f.read())['encoding']
        f.close()
        return encode

file_name = './adventure/DimDate.csv'
encoding = get_encoding(file_name)

print(encoding)
```

此处两种思路：

1. 将数据按照sql脚本导入sqlserver，再通过ETL转化为HIVE数据库
2. 对sql脚本用正则表达式提取表名和字段，重建Hive导数脚本

此处介绍第二种方法：

步骤如下：

1.将utf-16编码的csv文件转化为utf-8编码，并将源文件的"\|"分隔符改成通用的逗号分隔符，方法如下：

```python
   def transname(file_name):
       file_name_path_old = './adventure/'+file_name    #旧文件路径
       file_name_path_new = './adventure_new/'+file_name    # 新文件路径
       file = pd.DataFrame(pd.read_csv(file_name_path_old,sep='|',encoding = "UTF-16"))
       file.to_csv(file_name_path_new,encoding="UTF-8", index = False )
```

2.因为HIVE在load数据文件的时候，默认不去除表头：因此可以在python中去掉了csv文件的表头，方法如下：

```
   file.to_csv(file_path_out,header= False,index=False)     #file_path_out 为新文件保存地址
```

或者，可以设置HIVE默认不导入表头：方法如下：

```
   tblproperties(
   "skip.header.line.count"="n",  *--跳过文件行首n行*
   "skip.footer.line.count"="n"   *--跳过文件行尾n行* 
   )
```

3.将sql脚本通过正则表达式提取表名和字段，并合成hql建表和导数语句；

主要代码：

```python
   # 删除制表符、换行符、按表名分割
   def del_n_t_split(file):
       for i in list([r'\n\t',r'\n',r'\t']):
           file = re.sub(i," ",file,count=0,flags=0)
       file = re.split(r'GO',file)
       print("表的总数是：",len(file))
       return file
```

```python
   # 分割每个表
   table_name=[]
   for i in file:
       pattern = re.compile(r'\[dbo\]\.\[.*\]')
       u = pattern.findall(i)
       table_name.append(u)
   table_name[0]
```

```python
   # 提取表名
   table_name_2=[]
   for i in table_name:
       i=str(i)
       pattern = re.compile(r'\[dbo\]\.\[.*\]\(\ \[')
       u = pattern.findall(i)
       table_name_2.append(u)

   # 删除左右多余符号
   table_name_final= []
   for i in table_name_2:
       i=str(i)
       i = i.lstrip(r'\'[dbo].[')   
       i = i.rstrip(r']( [\']')
       table_name_final.append(i)
```

```python
   # 建表语句

   for i,j in enumerate(table_name_final):
       sql="drop table "+j+";\n"+"create table "+j+"("    #添加前缀
       for a in table_columns_final[i]:
           for b in a:
               b=str(b)
               if b == 'Date':      #Date是HIVE保留字段，无法用作列名
                   b = 'Date_time'
               sql=sql+b+"  string"+","    
       sql = sql.rstrip(',')    #删除列名最后多余的逗号
       sql= sql+''')row format serde 'org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe'  with serdeproperties('field.delim'=',','serialization.encoding'='UTF-8')  stored as textfile;'''+'\n'
       print(sql)
```

```python
   # 导入数据语句
   for i in all_file_csv:
       file_path = './adventure_new/'+i
       file_name = i.split('.')[0]
       file_name2 = 'ods_demon.'+file_name
       x='load data local inpath \'/root/adventure_demon/{}.csv\' into table {};'.format(file_name,file_name2)
       print(x)
```

4.将数据导入HIVE数据库。

## 探索数据，罗列分析指标

### 数据关系罗列：

![数据关系罗列图](https://i.loli.net/2019/06/15/5d048dad4a7c598826.png)

解释：

* 表主要分为Fact开头的事实表和Dim开头的维度表，通过主键连接，数据仓库类型为雪花型

### 主要分析指标罗列：

1. 地区维度、时间维度、产品种类维度
2. 销售额、订单量、客单价、平均运费、平均税率、预期完成率
3. 随时间变化趋势
4. 地理图销售额分布

## 建立数据汇总仓库\(Sales主题\)

本步骤解读：这一步是汇总需要的数据，并建立事实表、维度表用作powerbi导入数据分析，因此按照所需主题分析，主要分为两部分：

1. 查询建表；
2. 每日定时任务更新数据。

### 查询建表：

查询建表语法：

```sql
drop table dm_demon.dimsalesterritory;     -- 如果表存在，则删除表
create TABLE dm_demon.dimsalesterritory as    -- 创建dm_demon数据库中的dimsalesterritory表
SELECT * from DimSalesTerritory;       -- 查询语句
```

1.时间变化趋势取数：

* 事实表  fact\_time

  ```sql
  SELECT
      a.*,
      b.amount AS amount_last_year,
      b.order_number AS order_number_last_year,
      c.amount AS amount_last_month,
      c.order_number AS order_number_last_month,
      round(((a.amount-c.amount)/c.amount)*100,2) AS amount_comp_last_month,
      round(((a.order_number-c.order_number)/c.order_number)*100,2) AS order_number_comp_last_month,
      round(((a.amount-b.amount)/b.amount)*100,2) AS amount_comp_last_year,
      round(((a.order_number-b.order_number)/b.order_number)*100,2) AS order_number_comp_last_year
  FROM    (
      SELECT
          SalesTerritoryKey,
          from_unixtime(unix_timestamp(cast(orderdatekey AS string),'yyyy-MM-dd'),'yyyy-MM-dd') AS orderdate,
          YEAR (from_unixtime(unix_timestamp(cast(orderdatekey AS string),'yyyy-MM-dd'),'yyyy-MM-dd')) AS time_YEAR,
          QUARTER (from_unixtime(unix_timestamp(cast(orderdatekey AS string),'yyyy-MM-dd'),'yyyy-MM-dd')) AS time_QUARTER,
          MONTH (from_unixtime(unix_timestamp(cast(orderdatekey AS string),'yyyy-MM-dd'),'yyyy-MM-dd')) AS time_MONTH,
          WEEKofyear (from_unixtime(unix_timestamp(cast(orderdatekey AS string),'yyyy-MM-dd'),'yyyy-MM-dd')) AS time_WEEK,
          count( SalesAmount ) AS order_number,
          round(count(SalesAmount)*(0.9+rand ()*0.4),2) AS order_number_forcost,
          round( sum( SalesAmount ), 2 ) AS amount,
          round(sum(SalesAmount)*(0.9+rand ()*0.4),2) AS amount_forcost,
          round(sum(SalesAmount)/count(SalesAmount),2) AS customerunitprice,
          round( avg( TotalProductCost ), 2 ) AS per_productcost,
          round( avg( TaxAmt ), 2 ) AS per_tax,
          round( avg( freight ), 2 ) AS avg_freight 
      FROM
          FactInternetSales 
      GROUP BY
          SalesTerritoryKey,
          OrderDateKey 
      ) a
      LEFT JOIN (
          SELECT
              SalesTerritoryKey,
              OrderDateKey,
              date_add(from_unixtime(unix_timestamp( cast( orderdatekey AS string ), 'yyyy-MM-dd' ),    'yyyy-MM-dd' ), 365 ) AS orderdate,
              count( SalesAmount ) AS order_number,
              round( sum( SalesAmount ), 2 ) AS amount 
          FROM
              FactInternetSales 
          GROUP BY
              SalesTerritoryKey,
              OrderDateKey 
      ) b 
  ON a.SalesTerritoryKey = b.SalesTerritoryKey     AND a.orderdate = b.orderdate
  LEFT JOIN (
          SELECT
              SalesTerritoryKey,
              OrderDateKey,
              date_add(from_unixtime(unix_timestamp(cast(orderdatekey AS string),'yyyy-MM-dd'),'yyyy-MM-dd'),30) AS orderdate,
              count( SalesAmount ) AS order_number,
              round( sum( SalesAmount ), 2 ) AS amount
          FROM
              FactInternetSales 
          GROUP BY
              SalesTerritoryKey,
              OrderDateKey 
      ) c 
  ON b.SalesTerritoryKey = c.SalesTerritoryKey     AND b.orderdate = c.orderdate 
  WHERE
      a.orderdate <= from_unixtime( unix_timestamp( ), 'yyyy-MM-dd' ) 
  ORDER BY
      a.SalesTerritoryKey,
      a.orderdate;
  ```

  * 维度表  DimSalesTerritory

  ```
    SELECT * from DimSalesTerritory;
  ```

2.日报、周报、月报等取数

* 事实表 Factinternet

  ```sql
  select a.*,round(a.order_number/a.order_number_forcost,2) as order_number_forcost_comp,
  round(a.order_number/a.order_number_forcost,2) as amount_forcost_comp
  from (
  SELECT
      a.OrderDatekey as orderdate,
      YEAR ( from_unixtime(unix_timestamp(cast(a.orderdatekey as string),'yyyy-MM-dd'),'yyyy-MM-dd') ) AS time_YEAR,
       QUARTER ( from_unixtime(unix_timestamp(cast(a.orderdatekey as string),'yyyy-MM-dd'),'yyyy-MM-dd') ) AS time_QUARTER,
       MONTH ( from_unixtime(unix_timestamp(cast(a.orderdatekey as string),'yyyy-MM-dd'),'yyyy-MM-dd') ) AS time_MONTH,
       WEEKofyear ( from_unixtime(unix_timestamp(cast(a.orderdatekey as string),'yyyy-MM-dd'),'yyyy-MM-dd') ) AS time_WEEK,
      a.SalesTerritoryKey,
      b.ProductSubcategoryKey,
      count( a.CustomerKey ) AS order_number,
      round(count(a.CustomerKey)* ( 0.9+rand ( ) * 0.4 ), 2 ) AS order_number_forcost,
      round( sum( a.SalesAmount ), 2 ) AS Amount,
      round(sum(SalesAmount)*(0.9+rand ()*0.4),2) AS amount_forcost,
      round(sum(a.SalesAmount)/count(a.SalesAmount),2) AS customerunitprice,
      round( avg( a.TotalProductCost ), 2 ) AS per_productcost,
      round( avg( a.TaxAmt ), 2 ) AS per_tax,
      round( avg( a.freight ), 2 ) AS avg_freight 
  FROM
      FactinternetSales a
  LEFT JOIN DimProduct b ON a.ProductKey = b.ProductKey 
  where from_unixtime(unix_timestamp(cast(a.orderdatekey as string),'yyyy-MM-dd'),'yyyy-MM-dd') <= from_unixtime(unix_timestamp(),'yyyy-MM-dd')
  GROUP BY
      a.OrderDatekey,
      a.SalesTerritoryKey,
      b.ProductSubcategoryKey) a;
  ```

* 维度表DimProductSubcategory

* ```sql
  SELECT
      a.ProductSubcategoryKey,
      a.EnglishProductSubcategoryName,
      b.EnglishProductCategoryName 
  FROM
      DimProductSubcategory a
      LEFT JOIN DimProductCategory b ON a.ProductCategoryKey = b.ProductCategoryKey;
  ```
* 维度表Dimsalesterritory

* ```sql
  SELECT * from DimSalesTerritory;
  ```

3.地理图销售分布取数

* 事实表 fact\_Geography

* ```sql
  select 
      b.GeographyKey,
      sum(a.SalesAmount) as amount,
      count(a.CustomerKey) as order_number
  from FactInternetSales a
  left join DimCustomer b on a.CustomerKey=b.CustomerKey
  GROUP BY 
      b.GeographyKey;
  ```
* 维度表 DimGeography

* ```sql
  SELECT 
      Geographykey,
      SalesTerritoryKey,
      englishcountryregionname,
      city,
      StateProvinceCode,
      StateProvinceName,
      countryregioncode
  FROM DimGeography;
  ```

### 每日定时更新数据：

1.sql文件：

修改建表语句为以下格式

```sql
   insert into table dm_demon.fact_time 
   select * from fact_time;
```

同时设置where语句限制时间

```sql
   WHERE a.orderdate = from_unixtime( unix_timestamp( ), 'yyyy-MM-dd' )
```

2.sh脚本

此处采用shell执行sql文件语句方式：

hive  -f

```shell
   hive -f /root/adventure_demon/insert_data_hive_demon.sql
```

3.crontab 定时任务

    ```shell
   crontab -l    #查看crontab任务清单
   crontab -e    #进入crontab编辑页面
    ```


    ```shell
    crontab时间语法
    .---------------- minute (0 - 59) 
    |  .------------- hour (0 - 23)
    |  |  .---------- day of month (1 - 31)
    |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ... 
    |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7)  OR
    sun,mon,tue,wed,thu,fri,sat 
    |  |  |  |  |
    *  *  *  *  *  command to be executed
    ```
    

    ```shell
    # crontab代码
   0 1 * * * cd /root/adventure_demon/ && ./insert_data_hive_demon.sh  >insert.txt 2>&1 & #每天1点0分执行insert脚本
    ```
    

    ```shell
    #crontab服务状态
    sudo service crond start     #启动服务
    sudo service crond stop      #关闭服务
    sudo service crond restart   #重启服务
    sudo service crond reload    #重新载入配置
    sudo service crond status    #查看服务状态
    ```


## powerbi展示数据

### powerbi连接HIVE数据库导入数据

参考：[PowerBI连接HIVE数据库](http://shanlin.info/?p=175)

服务器需求：需要开启hiveserver或者hiveserver2在后台运行

1. 安装驱动

   ClouderaHiveODBC6，下载地址：[点击下载](https://downloads.cloudera.com/connectors/hive-2.5.12.1005/Windows/ClouderaHiveODBC64.msi)

2. 连接数据库导入数据

   * 在电脑中搜索ODBC，打开64-bit ODBC Administrator
   * 选择”系统DSN”选项卡，点击”添加”，选择cloudera ODBC Driver for Apache Hive DSN Setup，进行配置，点击test测试成功就说明配置成功了
   * ![ODBC配置图](https://i.loli.net/2019/06/15/5d048dda7295d93342.png)
   * 在PowerBI中，点击获取数据→其他→ODBC，数据源选择刚才配置好的hive数据库，然后在数据库选项卡中输入用户名和密码，点击连接就可以获取Hive数据库的数据了

### powerbi作图

1. 右侧窗格介绍

   * 书签窗格

     * 书签窗格开启方法：

       ![视图选项](https://i.loli.net/2019/06/15/5d048dffd210089640.png)

     * 书签作用范围：

       * 根据我的亲身测试：书签窗格会同步单个sheet硬件性的改变，而保持筛选器和隐藏显示的独立性

   * 选择窗格

     * 选择窗口开启方法：

       ![视图选项](https://i.loli.net/2019/06/15/5d048dffd210089640.png)

       

     * 可以选择显示或隐藏某可视化对象

       ![选择窗格](https://i.loli.net/2019/06/15/5d048e1c5647221720.png)

   * 可视化窗格

     * 图表选择

       * 默认图表

         ![可视化图表](https://i.loli.net/2019/06/15/5d048e2c0cf8a87798.png)

       * 可添加图表

         ![导入自定义视觉对象](https://i.loli.net/2019/06/15/5d048e43569b145188.png)

         powerbi部分自动义图表，从网上搜集

         链接：[https://share.weiyun.com/5Y0Wfr0](https://share.weiyun.com/5Y0Wfr0) 密码：dwvpn8

     * 图表内容选择

       * 在字段窗格勾选图表可视化所需的字段数据，如图例、值、轴

       * 可以选择可视化字段和字段数据的处理方式来确定你需要展示的数据，如求和、平均值、最大值、最小值

         ![字段计算方式](https://i.loli.net/2019/06/15/5d048e5bdee4f19270.png)

     * 筛选器

       * 视觉筛选器： 对当前图表限制的筛选器，限制 横纵坐标的范围
       * 页面级筛选器：对当前sheet页进行统一限制的筛选器

   * 字段窗格

     * 包含表和字段名

       ![字段窗格](https://i.loli.net/2019/06/15/5d048e75af7ab50066.png)

2. 数据关系介绍

   各表关系按照字段名相同会自动生成，也可以在管理关系功能中创建所需的关系

   ![数据关系](https://i.loli.net/2019/06/15/5d048e8660fe280675.png)

   如下图：

   ![创建数据关系](https://i.loli.net/2019/06/15/5d048e96db56a62119.png)

3. 数据格式设置

   当我们是导入数据而不是直连数据库的时候，并不是所有的数据格式都会如同我们所想的那样进行转化，因此在可视化作图的时候会存在不同程度的坑，如仪表板显示百分比则需要设置数据格式为百分比，仪表板才能显示百分比。

   设置方法：

   1. 在"建模&gt;&gt;&gt;数据类型” 处修改数据类型和格式
   2. 对数据表进行编辑查询，更改数据类型和格式

4. 可视化图表介绍

   1. 组合图：

      ![组合图样板](https://i.loli.net/2019/06/15/5d048eb0ce78a81697.png)

      主要设定：在可视化窗格设置列值为柱状图数据，行值为折线图数据

   2. 切片器：

      切片器类似于筛选器的作用，但是更加直观，可以勾选从而选择显示的数据

      ![切片器样板](https://i.loli.net/2019/06/15/5d048ebebb3eb53299.png)

   3. 仪表板

      仪表板不会自动计算百分比

      仪表板显示百分比需要设置数据格式为百分比

      ![仪表板样板](https://i.loli.net/2019/06/15/5d048eccb7dc210116.png)

### powerbi按钮书签的运用

参考：[https://www.jianshu.com/p/bd5e897635d6](https://www.jianshu.com/p/bd5e897635d6)

​            [https://zhuanlan.zhihu.com/p/64507583](https://zhuanlan.zhihu.com/p/64507583)

通过按钮和书签的结合，可以制作动态图表、导航栏等等。

![设定书签跳转按钮](https://i.loli.net/2019/06/15/5d048ee0a441790776.png)

在编辑模式下，ctrl+单机则可以跳转到我们书签所保存的页面去。









