## One-hot编码

```python
pandas.get_dummies(data)
```



## 特征选择

```python
from sklearn.feature_selection import SelectFromModel
from sklearn.ensemble import RandomForestClassifier
from sklearn.ensemble import GradientBoostingClassifier

selectfeature = SelectFromModel(RandomForestClassifier(n_estimators=1050, random_state=42), threshold="median")    
# RandomForestClassifier

# selectfeature = SelectFromModel(GradientBoostingClassifier())    
# GradientBoostingClassifier

selectfeature.fit_transform(X_train, Y_train)

feature_name = X_train.columns[selectfeature.get_support()]
print(len(feature_name))
feature_name
```



## 拉格朗日插值法



```python
from scipy.interpolate import lagrange
x=[1,2,3,4,7]
y=[5,7,10,3,9]
a=lagrange(x,y)
print(a)
print(a(1),a(2),a(3))
print(a[0],a[2],a[3])
```

```
 # 运行结果
 		4         3         2
0.5472 x - 7.306 x + 30.65 x - 47.03 x + 28.13

5.000000000000007 7.000000000000014 10.00000000000005

28.13333333333334 30.65277777777778 -7.3055555555555545
```

结果解释：

第一行：拉格朗日函数

第二行：验证原始数据

第三行：预测未知数据