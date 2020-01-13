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
 ```
 **classification**
```python
params = {
    'boosting_type': 'gbdt',
    'max_depth': 8,
    'num_leaves': 31,
    'learning_rate': 0.25,
    'feature_fraction': 0.9,
    'bagging_fraction': 0.8,
    'bagging_freq': 5,
    'verbose': -1
 }
model = lgb.LGBMClassifier(**params, n_estimators=1000, n_jobs = -1)
#train model
model.fit(X_train, y_train,
          eval_set=[(X_train, y_train), (X_valid, y_valid)],
          eval_metric='logloss',
          verbose=100,
          early_stopping_rounds=100)
## Phan lop
y_pred = model.predict(X_valid)
## Du doan Xac suat
y_pred_proba = model.predict_proba(X_valid)
```
### 2. XGBoost
**import**
```python
import xgboost as xgb
```
**classification**
```python
params = {
    "max_depth": 8,
    "booster": "gbtree",
    "subsample": 0.8,
    "colsample_bytree": 0.7,
    "gamma":0.1,
    "learning_rate":0.1
}
model = xgb.XGBClassifier(**params,
                          n_estimators=1000,
                          n_jobs=-1,
                          gpu_id=0)
#train model 
model.fit(X_train, y_train)
## Phan lop
y_pred = model.predict(X_valid)
## Du doan Xac suat
y_pred_proba = model.predict_proba(X_valid)
```
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
