
pdf文件：[download link](https://github.com)

## 正态性检验

#### W检验：

检验样本是否具有高斯分布

```py
from scipy.stats import shapiro
data = [21,12,12,23,19,13,20,17,14,19]
stat,p = shapiro(data)
print("stat为：%f" %stat,"p值为：%f" %p)
```


## 相关性检验

#### 皮尔逊相关系数：

检测两个样本是否具有相关关系

假设

* 每个样本中的观察是独立同分布的
* 每个样本的观察都是正态分布的
* 每个样本的观察具有相同的方差

```py
from scipy.stats import pearsonr
data1 = [21,12,12,23,19,13,20,17,14,19]
data2 = [12,11,8,9,10,15,16,17,10,16]
corr,p = pearsonr(data1,data2)
print("corr为：%f" %corr,"p值为：%f" %p)
```

#### 卡方检验：

检验两个分类变量是相关的还是独立的

假设

* 用于计算列联表的观察是独立的
* 列联表的每个单元格中有25个或更多个实例

```py
from scipy.stats import chi2_contingency
data1 = [21,12,12,23,19,13,20,17,14,19]
data2 = [12,11,8,9,10,15,16,17,10,16]
stat,p,dof,expected = chi2_contingency(data1,data2)
print("stat为：%f" %stat,"p值为：%f" %p)
```

#### 

## 参数统计假设检验

#### T检验：

检验两个独立样本的均值是否存在显著差异

假设

* 每个样本中的观察是独立同分布的
* 每个样本的观察都是正态分布的
* 每个样本中的观察具有相同的方差

```py
from scipy.stats import ttest_ind
data1 = [21,12,12,23,19,13,20,17,14,19]
data2 = [12,11,8,9,10,15,16,17,10,16]
stat,p = ttest_ind(data1,data2)
print("stat为：%f" %stat,"p值为：%f" %p)
```

#### 配对T检验

假设

* 每个样本中的观察是独立同分布的
* 每个样本的观察都是正态分布的
* 每个样本中的观察具有相同的方差
* 每个yang样本的观察结果是成对的

检验两个配对样本的均值是否存在显著差异

```py
from scipy.stats import ttest_rel
data1 = [21,12,12,23,19,13,20,17,14,19]
data2 = [12,11,8,9,10,15,16,17,10,16]
stat,p = ttest_rel(data1,data2)
print("stat为：%f" %stat,"p值为：%f" %p)
```

#### 方差分析

测试两个或两个以上独立样本的均值是否存在显著差异

假设：

* 每个样本中的观察是独立同分布的

* 每个样本的观察都是正态分布的

* 每个样本中的观察具有相同的方差

```py
from scipy.stats import f_oneway
data1 = [21,12,12,23,19,13,20,17,14,19]
data2 = [12,11,8,9,10,15,16,17,10,16]
stat,p = f_oneway(data1,data2)
print("stat为：%f" %stat,"p值为：%f" %p)
```

## 非参数统计假设检验

#### 曼-惠特尼U检验：

检验两个独立样本的分布是否相等。

假设

* 每个样本中的观察是独立同分布的
* 可以对每个样本中的观察进行排序

```py
from scipy.stats import mannwhitneyu
data1 = [21,12,12,23,19,13,20,17,14,19]
data2 = [12,11,8,9,10,15,16,17,10,16]
stat,p = mannwhitneyu(data1,data2)
print("stat为：%f" %stat,"p值为：%f" %p)
```

#### 威尔科克森符号秩检验

检验两个配对样本的分布是否均等

假设

* 每个样本中的观察是独立同分布
* 可以对每个样本中的观察进行排序

```py
from scipy.stats import wilcoxon
data1 = [21,12,12,23,19,13,20,17,14,19]
data2 = [12,11,8,9,10,15,16,17,10,16]
stat,p = wilcoxon(data1,data2)
print("stat为：%f" %stat,"p值为：%f" %p)
```

#### Kruskal-wallis H检验

检验两个或多个独立样本的分布是否相等

假设

* 每个样本中的观察是独立同分布的
* 可以对每个样本中的观察进行排序

```py
from scipy.stats import kruskal
data1 = [21,12,12,23,19,13,20,17,14,19]
data2 = [12,11,8,9,10,15,16,17,10,16]
stat,p = kruskal(data1,data2)
print("stat为：%f" %stat,"p值为：%f" %p)
```



