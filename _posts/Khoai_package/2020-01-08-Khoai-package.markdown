---
title: Khoai package
permalink: /my-package/khoai-package
category: Data Science
---
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
## Plot tool

## Metric tool
### dist_with_miss
Là hàm tính khoảng cách giữa 2 array chưa Missing Value
```python
def dist_with_miss(a, b, p=1, l=0.0):
    """ A function compute a distance betwwen 2 array with missing value.
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
