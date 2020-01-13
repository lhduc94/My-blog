---
title: Cơ bản về Data Science
permalink: 2020/01/09/The tables of all
category: Data Science
---
Một số thông tin hữu ích cho Data Science Project
## Model

### 1. LightGBM
[link paper](https://papers.nips.cc/paper/6907-lightgbm-a-highly-efficient-gradient-boosting-decision-tree.pdf)

[link source code](https://lightgbm.readthedocs.io/en/latest/)

**import**
```python
import lightgbm as lgb
```
**regression**
```python
params = {
    'objective': 'regression_l1',
    'nthread': 10,
    'max_depth': 8,
    'task': 'train',
    'boosting_type': 'gbdt',
    'metric': 'mape', # this is abs(a-e)/max(1,a)
    'num_leaves': 31,
    'learning_rate': 0.25,
    'feature_fraction': 0.9,
    'bagging_fraction': 0.8,
    'bagging_freq': 5,
    'lambda_l1': 0.06,
    'lambda_l2': 0.1,
    'device_type': 'gpu'
    'verbose': -1
 }
 ```
 **classification**
 ```python
 params = {
    'objective': 'multiclass',
    "num_class" : 4,
    'nthread': 10,
    'max_depth': 8,
    'task': 'train',
    'boosting_type': 'gbdt',
    'metric': 'softmax',
    'num_leaves': 31,
    'learning_rate': 0.25,
    'feature_fraction': 0.9,
    'bagging_fraction': 0.8,
    'bagging_freq': 5,
    'verbose': -1
 }
 ```
 **train model**
 ```python 
lgb_train = lgb.Dataset(train_x,train_y)
lgb_valid = lgb.Dataset(valid_x,valid_y)
# train
gbm = lgb.train(params=params,
                data=lgb_train,
                n_esimators=1000,
                valid_sets=[lgb_train, lgb_valid],
                early_stopping_rounds=100,
                verbose_eval=100)
```
### 2. XGBoost
### 3. CatBoost
### 4. RandomForest
### 5. Extra Tree
### 6. Regularized Greedy Forest
### 7. Field-aware Factorization Machines
### 8. Denoise Auto Encoder
### 9. Hill Climber Linear Model



## Nomalize

## Feature Engineering
### Categorical Encoding
1) One Hot Encoding
2) Label Encoding
3) Ordinal Encoding
4) Helmert Encoding
5) Binary Encoding
6) Frequency Encoding
7) Mean Encoding
8) Weight of Evidence Encoding
9) Probability Ratio Encoding
10) Hashing Encoding
11) Backward Difference Encoding
12) Leave One Out Encoding
13) James-Stein Encoding
14) M-estimator Encoding
... *I'll fill it soon*
