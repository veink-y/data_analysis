

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

    /usr/local/lib/python3.6/dist-packages/pip/_internal/commands/install.py:244: UserWarning: Disabling all use of wheels due to the use of --build-options / --global-options / --install-options.
      cmdoptions.check_install_build_global(options)

IN:

```python
# 读取数据
import pandas as pd
train = pd.read_csv('/content/HIGGS.csv')
train.head()
```

out：
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>1.000000000000000000e+00</th>
      <th>8.692932128906250000e-01</th>
      <th>-6.350818276405334473e-01</th>
      <th>2.256902605295181274e-01</th>
      <th>3.274700641632080078e-01</th>
      <th>-6.899932026863098145e-01</th>
      <th>7.542022466659545898e-01</th>
      <th>-2.485731393098831177e-01</th>
      <th>-1.092063903808593750e+00</th>
      <th>0.000000000000000000e+00</th>
      <th>1.374992132186889648e+00</th>
      <th>-6.536741852760314941e-01</th>
      <th>9.303491115570068359e-01</th>
      <th>1.107436060905456543e+00</th>
      <th>1.138904333114624023e+00</th>
      <th>-1.578198313713073730e+00</th>
      <th>-1.046985387802124023e+00</th>
      <th>0.000000000000000000e+00.1</th>
      <th>6.579295396804809570e-01</th>
      <th>-1.045456994324922562e-02</th>
      <th>-4.576716944575309753e-02</th>
      <th>3.101961374282836914e+00</th>
      <th>1.353760004043579102e+00</th>
      <th>9.795631170272827148e-01</th>
      <th>9.780761599540710449e-01</th>
      <th>9.200048446655273438e-01</th>
      <th>7.216574549674987793e-01</th>
      <th>9.887509346008300781e-01</th>
      <th>8.766783475875854492e-01</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1.0</td>
      <td>0.907542</td>
      <td>0.329147</td>
      <td>0.359412</td>
      <td>1.497970</td>
      <td>-0.313010</td>
      <td>1.095531</td>
      <td>-0.557525</td>
      <td>-1.588230</td>
      <td>2.173076</td>
      <td>0.812581</td>
      <td>-0.213642</td>
      <td>1.271015</td>
      <td>2.214872</td>
      <td>0.499994</td>
      <td>-1.261432</td>
      <td>0.732156</td>
      <td>0.000000</td>
      <td>0.398701</td>
      <td>-1.138930</td>
      <td>-0.000819</td>
      <td>0.000000</td>
      <td>0.302220</td>
      <td>0.833048</td>
      <td>0.985700</td>
      <td>0.978098</td>
      <td>0.779732</td>
      <td>0.992356</td>
      <td>0.798343</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.0</td>
      <td>0.798835</td>
      <td>1.470639</td>
      <td>-1.635975</td>
      <td>0.453773</td>
      <td>0.425629</td>
      <td>1.104875</td>
      <td>1.282322</td>
      <td>1.381664</td>
      <td>0.000000</td>
      <td>0.851737</td>
      <td>1.540659</td>
      <td>-0.819690</td>
      <td>2.214872</td>
      <td>0.993490</td>
      <td>0.356080</td>
      <td>-0.208778</td>
      <td>2.548224</td>
      <td>1.256955</td>
      <td>1.128848</td>
      <td>0.900461</td>
      <td>0.000000</td>
      <td>0.909753</td>
      <td>1.108330</td>
      <td>0.985692</td>
      <td>0.951331</td>
      <td>0.803252</td>
      <td>0.865924</td>
      <td>0.780118</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.0</td>
      <td>1.344385</td>
      <td>-0.876626</td>
      <td>0.935913</td>
      <td>1.992050</td>
      <td>0.882454</td>
      <td>1.786066</td>
      <td>-1.646778</td>
      <td>-0.942383</td>
      <td>0.000000</td>
      <td>2.423265</td>
      <td>-0.676016</td>
      <td>0.736159</td>
      <td>2.214872</td>
      <td>1.298720</td>
      <td>-1.430738</td>
      <td>-0.364658</td>
      <td>0.000000</td>
      <td>0.745313</td>
      <td>-0.678379</td>
      <td>-1.360356</td>
      <td>0.000000</td>
      <td>0.946652</td>
      <td>1.028704</td>
      <td>0.998656</td>
      <td>0.728281</td>
      <td>0.869200</td>
      <td>1.026736</td>
      <td>0.957904</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.0</td>
      <td>1.105009</td>
      <td>0.321356</td>
      <td>1.522401</td>
      <td>0.882808</td>
      <td>-1.205349</td>
      <td>0.681466</td>
      <td>-1.070464</td>
      <td>-0.921871</td>
      <td>0.000000</td>
      <td>0.800872</td>
      <td>1.020974</td>
      <td>0.971407</td>
      <td>2.214872</td>
      <td>0.596761</td>
      <td>-0.350273</td>
      <td>0.631194</td>
      <td>0.000000</td>
      <td>0.479999</td>
      <td>-0.373566</td>
      <td>0.113041</td>
      <td>0.000000</td>
      <td>0.755856</td>
      <td>1.361057</td>
      <td>0.986610</td>
      <td>0.838085</td>
      <td>1.133295</td>
      <td>0.872245</td>
      <td>0.808487</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.0</td>
      <td>1.595839</td>
      <td>-0.607811</td>
      <td>0.007075</td>
      <td>1.818450</td>
      <td>-0.111906</td>
      <td>0.847550</td>
      <td>-0.566437</td>
      <td>1.581239</td>
      <td>2.173076</td>
      <td>0.755421</td>
      <td>0.643110</td>
      <td>1.426367</td>
      <td>0.000000</td>
      <td>0.921661</td>
      <td>-1.190432</td>
      <td>-1.615589</td>
      <td>0.000000</td>
      <td>0.651114</td>
      <td>-0.654227</td>
      <td>-1.274345</td>
      <td>3.101961</td>
      <td>0.823761</td>
      <td>0.938191</td>
      <td>0.971758</td>
      <td>0.789176</td>
      <td>0.430553</td>
      <td>0.961357</td>
      <td>0.957818</td>
    </tr>
  </tbody>
</table>


IN:


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



