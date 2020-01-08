---
title: Khoai package
permalink: /my-package/khoai-package
category: Data Science
---
Bạn có thể cài đặt thư viện bằng cách sử dụng `pip` theo cú pháp
```bash
pip install khoai
```
## DataFrame tool
### reduce_memory_usage
Là hàm giảm dung lượng lưu trữ cho DataFrame
```python
def reduce_mem_usage(df, verbose=True):
    """ A function reduce memory of DataFrame.
    Parameters:
            df: DataFrame
                A table of data.
            veborse: bool
                Show mem. usage decreased.
    Output:
            DataFrame
    """
```
Import thư viện
```python
from khoai.df_tools import reduce_mem_usage
```
### calibrate
Là hàm điều chỉnh phân phối xác suất dự đoán của lớp thiểu số sau khi undersample data.
```python
def calibrate(data, train_pop, target_pop, sampled_train_pop, sampled_target_pop):
    """ Calibrate data after undersample data.
    Parameters:
                data: float array
                    probability prediction Array.
                train_pop: int
                    Number of data in training data.
                target_pop: int
                    Number of target class (minority class) in the training dataset.
                sampled_train_pop: int
                    Number of data in training dataset after undersampling.
                sampled_target_pop: int
                    Number of the target class(minority class) in the training dataset after undersampling.
    Return:     
                data: float array
                    probability prediction Array after calibrate.
    """
```
Cách sử dụng
```python
from khoai.df_tools import calibrate
import numpy as np
model_results = np.array([0.65] * 10000)
model_results = calibrate(model_results, 50000, 500, 10000, 500)
```
## Plot tool

## Metric tool
### dist_with_miss
Là hàm tính khoảng cách giữa 2 array chưa Missing Value
```python
def dist_with_miss(a, b, p=1, l=0.0):
    """ A function compute a distance betwen 2 array with missing value.
    Parameters:
        a: array
            Input Array.	
        b: array
            Input Array.
        p: int
            The order of the norm of the difference in minkowski distance.
        l: float
            The lambda value of missing value.
    Output: 
        distance: float
            The distance between 2 array. 
```
## Defined Function 
