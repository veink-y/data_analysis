

```python
RandomForestClassifier(
    n_estimators=10,
    criterion=’gini’, 
    max_depth=None,
    min_samples_split=2,
    min_samples_leaf=1,
    min_weight_fraction_leaf=0.0, 
    max_features=’auto’, 
    max_leaf_nodes=None, 
    min_impurity_decrease=0.0,
    min_impurity_split=None, 
    bootstrap=True, 
    oob_score=False, 
    n_jobs=1, 
    random_state=None,
    verbose=0, 
    warm_start=False, 
    class_weight=None
    )
```

### 参数解释

```python
参数(params)：
    n_estimators:数值型取值
        含义：森林中决策树的个数，默认是10

    criterion:字符型取值
        含义：采用何种方法度量分裂质量，信息熵或者基尼指数，默认是基尼指数

    max_features:取值为int型, float型, string类型, or None()，默认"auto"
        含义：寻求最佳分割时的考虑的特征数量，即特征数达到多大时进行分割。
        int:max_features等于这个int值
        float:max_features是一个百分比，每(max_features * n_features)特征在每个分割出被考虑。
        "auto":max_features等于sqrt(n_features)
        "sqrt":同等于"auto"时
        "log2":max_features=log2(n_features)
        None:max_features = n_features

    max_depth:int型取值或者None，默认为None
        含义：树的最大深度

    min_samples_split:int型取值，float型取值，默认为2
        含义：分割内部节点所需的最少样本数量
        int:如果是int值，则就是这个int值
        float:如果是float值，则为min_samples_split * n_samples

    min_samples_leaf:int取值，float取值，默认为1
        含义：叶子节点上包含的样本最小值
        int:就是这个int值
        float:min_samples_leaf * n_samples

    min_weight_fraction_leaf : float，default=0.
        含义：能成为叶子节点的条件是：该节点对应的实例数和总样本数的比值，至少大于这个min_weight_fraction_leaf值

    max_leaf_nodes:int类型，或者None(默认None)
        含义：最大叶子节点数，以最好的优先方式生成树，最好的节点被定义为杂质相对较少，即纯度较高的叶子节点

    min_impurity_split:float取值 
        含义：树增长停止的阀值。一个节点将会分裂，如果他的杂质度比这个阀值；如果比这个值低，就会成为一个叶子节点。

    min_impurity_decrease:float取值，默认0.
        含义：一个节点将会被分裂，如果分裂之后，杂质度的减少效果高于这个值。

    bootstrap:boolean类型取值，默认True
        含义：是否采用有放回式的抽样方式

    oob_score:boolean类型取值，默认False
        含义：是否使用袋外样本来估计该模型大概的准确率

    n_jobs:int类型取值，默认1
        含义：拟合和预测过程中并行运用的作业数量。如果为-1，则作业数设置为处理器的core数。

    class_weight:dict, list or dicts, "balanced"
        含义：如果没有给定这个值，那么所有类别都应该是权重1
        对于多分类问题，可以按照分类结果y的可能取值的顺序给出一个list或者dict值，用来指明各类的权重.
        "balanced"模式，使用y值自动调整权重，该模式类别权重与输入数据中的类别频率成反比，
即n_samples / (n_classes * np.bincount(y))，分布为第n个类别对应的实例数。
        "balanced_subsample"模式和"balanced"模式类似，只是它计算使用的是有放回式的取样中取得样本数，而不是总样本数
```

### 参数属性

```py
属性：
    estimators_:决策树列表
        拟合好的字分类器列表，也就是单个决策树

    classes_:array of shape = [n_features]
        类别标签列表

    n_classes_:int or list
        类别数量

    n_features:int
        拟合过程中使用的特征的数量

    n_outputs:int 
        拟合过程中输出的数量

    featrue_importances:特征重要程度列表
        值越大，说明越重要

    oob_score:array of shape = [n_features]
        使用oob数据集测试得到的得分数

    oob_decision_funtion_:array of shape = [n_features, n_classes]
        oob样本预测结果，每一个样本及相应结果对列表
```

### 参数方法

```py
方法：
    apply(X):用构造好的森林中的树对数据集X进行预测，返回每棵树预测的叶子节点。所以结果应该是二维矩阵，
行为样本第几个样本，列为每棵树预测的叶子节点。 
        参数：    
            X ： 类似数组或稀疏矩阵，shape = [n_samples，n_features]
                输入样本。在内部，它的dtype将被转换为 dtype=np.float32。如果提供稀疏矩阵，则将其转换为稀疏矩阵csr_matrix。

        返回：    
            X_leaves ： array_like，shape = [n_samples，n_estimators]
                对于X中的每个数据点x和林中的每个树，返回叶子x的索引。

    decision_path(X):返回森林中的决策路径

    fit(X, y[, sample_weight]):用训练数据集(x, y)来构造森林

    get_params([deep]):获得分类器的参数

    predict(X):预测X的类别

    predict_log_proba(X):预测X的类的对数概率，和predict_proba类似，只是取了对数

    predict_proba(X):预测X的类别的概率。输入样本的预测类别概率被计算为森林中树木的平均预测类别概率。
单个树的类概率是叶中同一类的样本的比率。因为叶子节点并不是完全纯净的，它也有杂质，
不同种类所占恶比率是不一样的，但肯定有一类纯度很高。返回值是array of shape = [n_samples, n_classes]

    score(X, y[,sample_weight]):返回给定的数据集（数据集指定了类别）的预测准确度

    set_params(**params):设置决策树的参数
```



