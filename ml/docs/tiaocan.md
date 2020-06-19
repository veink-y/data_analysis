

函数：GridSearchCV

使用示例：

```python
# 网格搜索调参
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import GridSearchCV

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

