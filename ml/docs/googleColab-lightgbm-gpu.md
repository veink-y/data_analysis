

```python
!/opt/bin/nvidia-smi    # 查看google colaboratory gpu型号
```



```python
# 下载数据集并解压
!wget https://archive.ics.uci.edu/ml/machine-learning-databases/00280/HIGGS.csv.gz
!gzip -d HIGGS.csv.gz
```




```python
#编译安装lightgbm-gpu

!pip install cmake

!git clone --recursive https://github.com/Microsoft/LightGBM
%cd LightGBM
!mkdir build
%cd build
!cmake ../../LightGBM
!make -j4

!git clone --recursive https://github.com/Microsoft/LightGBM.git
%cd LightGBM/python-package
!python3 setup.py install --gpu
```



```python
#编译安装lightgbm-gpu
!apt-get -qq install --no-install-recommends nvidia-375
!apt-get -qq install --no-install-recommends nvidia-opencl-icd-375 nvidia-opencl-dev opencl-headers
!apt-get -qq install --no-install-recommends git cmake build-essential libboost-dev libboost-system-dev libboost-filesystem-dev
!pip3 install -qq lightgbm --install-option=--gpu
```



```python
# 读取数据
import pandas as pd
train = pd.read_csv('/content/HIGGS.csv')
train.head()
```


```python
# 训练数据

import lightgbm as lgb
import time

dtrain = lgb.Dataset(train)

#CPU训练
params = {'max_bin': 63,
          'num_leaves': 255,
          'learning_rate': 0.1,
          'tree_learner': 'serial',
          'task': 'train',
          'is_training_metric': 'false',
          'min_data_in_leaf': 1,
          'min_sum_hessian_in_leaf': 100,
          'ndcg_eval_at': [1, 3, 5, 10],
          'sparse_threshold': 1.0,
          'device': 'cpu'
          }
print("*****************************")
t0 = time.time()
gbm = lgb.train(params, train_set=dtrain, num_boost_round=10,
                valid_sets=None, valid_names=None,
                fobj=None, feval=None, init_model=None,
                feature_name='auto', categorical_feature='auto',
                early_stopping_rounds=None, evals_result=None,
                verbose_eval=True,
                keep_training_booster=False, callbacks=None)
t1 = time.time()


print('cpu version elapse time: {}'.format(t1-t0))
time.sleep(20)

print("*****************************")

#GPU训练
params = {'max_bin': 63,
          'num_leaves': 255,
          'learning_rate': 0.1,
          'tree_learner': 'serial',
          'task': 'train',
          'is_training_metric': 'false',
          'min_data_in_leaf': 1,
          'min_sum_hessian_in_leaf': 100,
          'ndcg_eval_at': [1, 3, 5, 10],
          'sparse_threshold': 1.0,
          'device': 'gpu',
          'gpu_platform_id': 1,
          'gpu_device_id': 0
          }

t0 = time.time()
gbm = lgb.train(params, train_set=dtrain, num_boost_round=10,
                valid_sets=None, valid_names=None,
                fobj=None, feval=None, init_model=None,
                feature_name='auto', categorical_feature='auto',
                early_stopping_rounds=None, evals_result=None,
                verbose_eval=True,
                keep_training_booster=False, callbacks=None)
t1 = time.time()

print('gpu version elapse time: {}'.format(t1-t0))
time.sleep(20)
print("*****************************")


params = {'max_bin': 63,
          'num_leaves': 255,
          'learning_rate': 0.1,
          'tree_learner': 'serial',
          'task': 'train',
          'is_training_metric': 'false',
          'min_data_in_leaf': 1,
          'min_sum_hessian_in_leaf': 100,
          'ndcg_eval_at': [1, 3, 5, 10],
          'sparse_threshold': 1.0,
          'device': 'gpu'
          }


t0 = time.time()
gbm = lgb.train(params, train_set=dtrain, num_boost_round=10,
                valid_sets=None, valid_names=None,
                fobj=None, feval=None, init_model=None,
                feature_name='auto', categorical_feature='auto',
                early_stopping_rounds=None, evals_result=None,
                verbose_eval=True,
                keep_training_booster=False, callbacks=None)
t1 = time.time()

print('gpu version elapse time: {}'.format(t1-t0))
```

out：

    *****************************
    cpu version elapse time: 26.87044644355774
    *****************************
    gpu version elapse time: 1.8540704250335693
    *****************************
    gpu version elapse time: 1.5106704235076904



