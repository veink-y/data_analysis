
## 项目介绍

#### 项目内容：

1、数据集预处理和特征工程

2、随机森林调节参数和10折交叉验证训练

3、预测结果并提交

4、保存模型并部署为FlaskAPI以供调用

#### 项目成果：AUC分数  0.93450063

### 数据集：

1、 大小

        训练集：(25317, 18)
        测试集：(10852, 17)

2、 字段解释

| 字段名称 | 数据类型 | 字段描述 |
| :--- | :--- | :--- |
| ID | Int | 客户唯一标识 |
| age | Int | 客户年龄 |
| job | String | 客户的职业 |
| marital | String | 婚姻状况 |
| education | String | 受教育水平 |
| default | String | 是否有违约记录 |
| balance | Int | 每年账户的平均余额 |
| housing | String | 是否有住房贷款 |
| loan | String | 是否有个人贷款 |
| contact | String | 与客户联系的沟通方式 |
| day | Int | 最后一次联系的时间（几号） |
| month | String | 最后一次联系的时间（月份） |
| duration | Int | 最后一次联系的交流时长 |
| campaign | Int | 在本次活动中，与该客户交流过的次数 |
| pdays | Int | 距离上次活动最后一次联系该客户，过去了多久（999表示没有联系过） |
| previous | Int | 在本次活动之前，与该客户交流过的次数 |
| poutcome | String | 上一次活动的结果 |
| y | Int | 预测客户是否会订购定期存款业务 |


3、 问题描述：  
​           这些数据与葡萄牙银行机构的营销活动相关。这些营销活动以电话为基础，一般，银行的客服人员需要联系客户至少一次，以此确认客户是否将认购该银行的产品（定期存款）。

4、 评测算法： [AUC\(Area Under the Curve\) ](https://en.wikipedia.org/wiki/Receiver_operating_characteristic#Area_under_the_curve)

## 数据预处理

#### 预先导入包：

```python
# 导入包
import pandas as pd
import numpy as np
import warnings
from tqdm import tqdm
%matplotlib inline

from sklearn.preprocessing import LabelEncoder
from scipy import sparse
from sklearn.model_selection import StratifiedKFold
warnings.filterwarnings("ignore")

from datetime import datetime
```

导入数据：

```python
# 导入数据
train=pd.read_csv('./train_set.csv')
test =pd.read_csv('./test_set.csv')
data=pd.concat([train,test])
#将ID 赋值给另一个变量
customerID = data['ID']
target = data['y']

# 查看数据
data.head()
```



### 特征工程

#### 1、将day、month字段和其余离散字段组合后进行聚合计数并合并到原始数据集：

```python
# 特征演变1,day_month_feature
data1 = data
data1['day_month'] = data1['month']+data1['day'].astype(str)

feature_cross1 = []

def feature_day_month(data,feature):
    feature_cross1_name = 'count' + '_day_month_' + feature 
    feature_cross1.append(feature_cross1_name)
    features = [feature]+['day_month']
    temp = data.groupby(features).size().reset_index().rename(columns={0: feature_cross1_name})
    data = data.merge(temp, 'left', on=features)
    return data

for i in tqdm(sparse_feature):
    data1 = feature_day_month(data1,i)
print(feature_cross1)  
data1.drop(['day_month'],axis=1,inplace=True)
data1.head()
```

2、将离散特征组合，并聚合组合合并到原始数据集

```python
data2 =data1

def columns_name_trans(name,columns):
    temp_name = name+'_'
    temp_name_columns=[]
    for l in range(1,len(temp.columns)):
        temp_name_column = temp_name
    #     print(l)
        for i in temp.columns[l]:
    #         print(i)
            temp_name_column = temp_name_column+i+'_'

        temp_name_column = temp_name_column.rstrip(r'_')
        temp_name_columns.append(temp_name_column)

    return temp_name_columns


for d in sparse_feature:
    aggs=['count','nunique']
    sparse_feature_left = list(set(sparse_feature) - set([d]))
    temp=data2.groupby(d)[sparse_feature_left].agg(aggs).reset_index()
    temp.columns=[d]+columns_name_trans(d,temp.columns)
    data2=pd.merge(data2,temp,'left',on=d)


data2.head()
```

3、组合离散特征和连续性特征，

```python
data3 =data2

for d in sparse_feature:
    aggs=['count','nunique']
    temp=data3.groupby(d)[continue_feature].agg(aggs).reset_index()
    temp.columns=[d]+columns_name_trans(d,temp.columns)
    data3=pd.merge(data3,temp,'left',on=d)


data3.head()
```

4、one-hot编码离散特征

```python
data4  = data3

for s in ['campaign','contact','default','education','housing','job','loan','marital','month','poutcome']:
    data4=pd.concat([data4,pd.get_dummies(data3[s],prefix=s+'_')],axis=1)
    data4.drop(s,axis=1,inplace=True)

data4.head()
```

## 机器学习部分

#### 数据转化为训练数据

```python
df_train=data4[data4['y'].notnull()]
df_pred=data4[data4['y'].isnull()]

from sklearn.utils import shuffle
df_train = shuffle(df_train).reset_index(drop=True)          #打乱训练集顺序


df_train_columns=df_train.columns.tolist()
df_train_columns.remove('ID')
df_train_columns.remove('y')

X_train = df_train[df_train_columns]
Y_train = df_train['y']
X_Pred = df_pred[df_train_columns]
```

#### 特征选择/selectfrommodel

```python
from sklearn.feature_selection import SelectFromModel
from sklearn.ensemble import RandomForestClassifier

# SelectFromModel
selectfeature = SelectFromModel(RandomForestClassifier(n_estimators=1050, random_state=42), threshold="median")    # RandomForestClassifier

selectfeature.fit_transform(X_train, Y_train)

feature_name = X_train.columns[selectfeature.get_support()]
print(len(feature_name))
print(feature_name)

X_train_rf = X_train[feature_name]
X_Pred_rf = X_Pred[feature_name]
X_train_rf.head()
```

#### 网格调参

```python
%%time

# 网格搜索调参
param_test = {"n_estimators":  range(100, 1000,100)
              }
gsearch1 = GridSearchCV(estimator = RandomForestClassifier(n_jobs=-1), 
                       param_grid = param_test, scoring='roc_auc',cv=10)
gsearch1.fit(X_train_rf, Y_train)

print(gsearch1.best_score_ ,gsearch1.best_params_,gsearch1.best_estimator_)

paramsDf = pd.DataFrame( 
    { 'params': gsearch1.cv_results_['params'] , 
     'pred':gsearch1.cv_results_['mean_test_score'] })
paramsDf
```

#### 10折交叉验证训练数据

```python
# %%time
# 10折交叉 随机森林
folds = StratifiedKFold(n_splits=10, shuffle=True, random_state=666)
predictions = np.zeros(len(X_Pred))

train_score = []
test_score = []
oob_score = []

for fold_, (train_idx, test_idx) in enumerate(folds.split(X_train_rf, Y_train)):
    random_forest = RandomForestClassifier(n_jobs=-1,n_estimators=700, max_depth=23,min_samples_leaf=6,max_features = 0.3,random_state = 50,oob_score=True)
    random_forest.fit(X_train_rf.iloc[train_idx], Y_train.iloc[train_idx])

    roc_train = roc_auc_score(Y_train.iloc[train_idx],random_forest.predict_proba(X_train_rf.iloc[train_idx])[:,1])
    roc_test = roc_auc_score(Y_train.iloc[test_idx],random_forest.predict_proba(X_train_rf.iloc[test_idx])[:,1])
    print("训练集分数：{}    测试集分数：{}   袋外分数：{}".format(roc_train,roc_test,random_forest.oob_score_))
    train_score.append(roc_train)
    test_score.append(roc_test)
    oob_score.append(random_forest.oob_score_)
    predictions += random_forest.predict_proba(X_Pred_rf)[:,1] / folds.n_splits
print("训练集平均分数：{}    测试集平均分数：{}   袋外平均分数：{}".format(np.mean(train_score),np.mean(test_score),np.mean(oob_score)))
```

#### 结果保存

```python
predDf_rf = pd.DataFrame( 
    { 'ID': test.ID , 
     'pred': predictions} )
path = 'bank_pred_random_forest_tezhen ' + datetime.now().strftime('%Y-%m-%d %H-%M') + ' .csv'
predDf_rf.to_csv( path , index = False )
predDf_rf.head()
```

## 模型部署

环境需求：gunicron、flask、sklearn、numpy、pandas

满足需求： 输入一组数据，获得预测结果集

使用方法：FlaskAPI

调用方法： request ，curl等

### 模型保存和读取方法

1、joblib

```python
from sklearn.externals import joblib #jbolib模块

#保存Model(注:save文件夹要预先建立，否则会报错)
joblib.dump(clf, 'save/clf.pkl')

#读取Model
clf3 = joblib.load('save/clf.pkl')

#测试读取后的Model
print(clf3.predict(X[0:1]))
```

2、pickle

```python
import pickle #pickle模块

#保存Model(注:save文件夹要预先建立，否则会报错)
with open('save/clf.pickle', 'wb') as f:
    pickle.dump(clf, f)

#读取Model
with open('save/clf.pickle', 'rb') as f:
    clf2 = pickle.load(f)
    #测试读取后的Model
    print(clf2.predict(X[0:1]))
```

### Flask serve.py 代码

```python
"""Filename: serve.py
"""
from flask import Flask, jsonify, request
# from flask_restful import reqparse, abort, Api, Resource
from sklearn.externals import joblib
import numpy as np
import pandas as pd
from sklearn.ensemble import RandomForestClassifier
import os

app = Flask(__name__)
# 预处理函数1
def feature_day_month(data,feature):
    feature_cross1_name = 'count' + '_day_month_' + feature 
    features = [feature]+['day_month']
    temp = data.groupby(features).size().reset_index().rename(columns={0: feature_cross1_name})
    data = data.merge(temp, 'left', on=features)
    return data

 #预处理函数2
def columns_name_trans(name,columns):
    temp_name = name+'_'
    temp_name_columns=[]
    for l in range(1,len(columns)):
        temp_name_column = temp_name
        for i in columns[l]:
            temp_name_column = temp_name_column+i+'_'

        temp_name_column = temp_name_column.rstrip(r'_')
        temp_name_columns.append(temp_name_column)

    return temp_name_columns


# 数据预处理
def dataprocess(data):
    customerID = data['ID']
    # 区分离散变量和连续变量
    sparse_feature = [ 'contact','default', 'education','housing','job', 'loan', 'marital','poutcome'] 
    continue_feature = list(set(data.columns) - set(sparse_feature))
    # 特征工程1
    data1 = data
    print("data1")

    data1['day_month'] = data1['month']+data1['day'].astype(str)

    for i in sparse_feature:
        data1 = feature_day_month(data1,i)
    data1.drop(['day_month'],axis=1,inplace=True)

    #特征工程2
    data2 =data1
    print("data2")
    for d in sparse_feature:
        aggs=['count','nunique']
        sparse_feature_left = list(set(sparse_feature) - set([d]))
        temp=data2.groupby(d)[sparse_feature_left].agg(aggs).reset_index()
        temp.columns=[d]+columns_name_trans(d,temp.columns)
        data2=pd.merge(data2,temp,'left',on=d)

    #特征工程3
    data3 =data2
    print("data3")
    for d in sparse_feature:
        aggs=['count','nunique']
        temp=data3.groupby(d)[continue_feature].agg(aggs).reset_index()
        temp.columns=[d]+columns_name_trans(d,temp.columns)
        data3=pd.merge(data3,temp,'left',on=d)

    # 特征工程4
    print("data4")
    data4  = data3
    for s in ['campaign','contact','default','education','housing','job','loan','marital','month','poutcome']:
        data4=pd.concat([data4,pd.get_dummies(data3[s],prefix=s+'_')],axis=1)
        data4.drop(s,axis=1,inplace=True)

    #选取的特征
    feature_name = ['age',
                    'balance',
                    'day',
                    'duration',
                    'pdays',
                    'previous',
                    'count_day_month_contact',
                    'count_day_month_default',
                    'count_day_month_education',
                    'count_day_month_housing',
                    'count_day_month_job',
                    'count_day_month_loan',
                    'count_day_month_marital',
                    'count_day_month_poutcome',
                    'contact_default_count',
                    'contact_housing_count',
                    'contact_education_count',
                    'contact_job_count',
                    'contact_loan_count',
                    'contact_poutcome_count',
                    'contact_marital_count',
                    'education_default_count',
                    'education_housing_count',
                    'education_job_count',
                    'education_loan_count',
                    'education_poutcome_count',
                    'education_marital_count',
                    'education_contact_count',
                    'housing_default_count',
                    'housing_education_count',
                    'housing_loan_count',
                    'housing_poutcome_count',
                    'housing_marital_count',
                    'housing_contact_count',
                    'job_default_count',
                    'job_housing_count',
                    'job_education_count',
                    'job_loan_count',
                    'job_poutcome_count',
                    'job_marital_count',
                    'job_contact_count',
                    'marital_default_count',
                    'marital_housing_count',
                    'marital_education_count',
                    'marital_job_count',
                    'marital_loan_count',
                    'marital_poutcome_count',
                    'marital_contact_count',
                    'poutcome_default_count',
                    'poutcome_housing_count',
                    'poutcome_education_count',
                    'poutcome_job_count',
                    'poutcome_loan_count',
                    'poutcome_marital_count',
                    'poutcome_contact_count',
                    'contact_campaign_count',
                    'contact_balance_count',
                    'contact_balance_nunique',
                    'contact_day_count',
                    'contact_pdays_count',
                    'contact_pdays_nunique',
                    'contact_age_count',
                    'contact_age_nunique',
                    'contact_duration_count',
                    'contact_duration_nunique',
                    'contact_month_count',
                    'contact_previous_count',
                    'contact_previous_nunique',
                    'education_campaign_count',
                    'education_campaign_nunique',
                    'education_balance_count',
                    'education_balance_nunique',
                    'education_day_count',
                    'education_pdays_count',
                    'education_pdays_nunique',
                    'education_age_count',
                    'education_age_nunique',
                    'education_duration_count',
                    'education_duration_nunique',
                    'education_month_count',
                    'education_previous_count',
                    'education_previous_nunique',
                    'housing_campaign_nunique',
                    'housing_balance_count',
                    'housing_balance_nunique',
                    'housing_day_count',
                    'housing_pdays_count',
                    'housing_age_nunique',
                    'housing_duration_count',
                    'housing_duration_nunique',
                    'housing_month_count',
                    'housing_previous_nunique',
                    'job_campaign_count',
                    'job_campaign_nunique',
                    'job_balance_count',
                    'job_balance_nunique',
                    'job_day_count',
                    'job_pdays_count',
                    'job_pdays_nunique',
                    'job_age_count',
                    'job_age_nunique',
                    'job_duration_count',
                    'job_duration_nunique',
                    'job_month_count',
                    'job_previous_count',
                    'job_previous_nunique',
                    'loan_age_count',
                    'loan_previous_nunique',
                    'marital_campaign_count',
                    'marital_campaign_nunique',
                    'marital_balance_count',
                    'marital_balance_nunique',
                    'marital_day_count',
                    'marital_pdays_count',
                    'marital_pdays_nunique',
                    'marital_age_count',
                    'marital_age_nunique',
                    'marital_duration_count',
                    'marital_duration_nunique',
                    'marital_month_count',
                    'marital_previous_count',
                    'marital_previous_nunique',
                    'poutcome_campaign_count',
                    'poutcome_campaign_nunique',
                    'poutcome_balance_count',
                    'poutcome_balance_nunique',
                    'poutcome_day_count',
                    'poutcome_pdays_count',
                    'poutcome_pdays_nunique',
                    'poutcome_age_count',
                    'poutcome_age_nunique',
                    'poutcome_duration_count',
                    'poutcome_duration_nunique',
                    'poutcome_month_count',
                    'poutcome_previous_count',
                    'poutcome_previous_nunique',
                    'campaign__1',
                    'campaign__2',
                    'campaign__3',
                    'campaign__4',
                    'campaign__5',
                    'campaign__6',
                    'campaign__7',
                    'campaign__9',
                    'contact__unknown',
                    'education__primary',
                    'education__secondary',
                    'education__tertiary',
                    'housing__yes',
                    'job__admin.',
                    'job__management',
                    'job__retired',
                    'job__self-employed',
                    'job__services',
                    'job__technician',
                    'loan__no',
                    'marital__married',
                    'marital__single',
                    'month__apr',
                    'month__aug',
                    'month__feb',
                    'month__jan',
                    'month__jul',
                    'month__jun',
                    'month__mar',
                    'month__may',
                    'month__nov',
                    'month__oct',
                    'month__sep',
                    'poutcome__failure',
                    'poutcome__success',
                    'poutcome__unknown']

    X_pred = data4[feature_name]

    return customerID,X_pred



@app.route('/predict', methods=['POST'])
def apicall():
    """API Call

    Pandas dataframe (sent as a payload) from API Call
    """
    try:
        test_json = request.get_json()
        test = pd.read_json(test_json, orient='records')
    except Exception as e:
        raise e

    if test.empty:
        return(bad_request())
    else:
        # 读取模型
        print("Loading the model...")
        loaded_model = joblib.load('./rf_model/rf.pkl')
        # 处理数据
        print("Data Process...")
        customer,test_data = dataprocess(test)
        print(len(test_data.columns))
        # 进行预测
        print("data predict...")
        predictions_proba = loaded_model.predict_proba(test_data)[:,1]

        print("预测结束")
        predDf_api = pd.DataFrame( 
                                { 'ID': customer , 
                                'pred': predictions_proba} )

        predDf_api_json = jsonify(predictions=predDf_api.to_json(orient="records"))

        print("End")

        return predDf_api_json
```

### 启动方法

```bash
gunicorn --bind 0.0.0.0:1234 serve:app
```



