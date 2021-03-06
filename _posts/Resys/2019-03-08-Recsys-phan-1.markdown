---
title : Recommender System Part 1
permalink : /2019/03/08/recomemder_system_part_1/
category : Recommender System
---
Mình xin chia sẻ cách giải quyết bài toán __Recommendation__ theo hướng tiếp cận __Collaborative Filtering__ với thuật toán __KNN-itembased__

### Load các thư viện cần thiết
```python
import numpy as np
import pandas as pd
from sklearn.neighbors import NearestNeighbors
```
### Đọc dữ liệu từ file
Ở đây mình sử dụng tập movielen 1M - Các bạn có thể xem tại [đây](https://grouplens.org/datasets/movielens/1m/)
```python
df = pd.read_csv('ml-1m/ratings.dat',sep='::',names=['UserID','ItemID','Rating','Timestamp'])
df.head()
```
### Xử lý dữ liệu 
Ở đây mình chuyển danh sách __cạnh__  : _(user,item,rating)_ thành ma trận __vuông__ :_M(U,I)_, sử dụng `pivot` của `pandas`. Hàm `fillna(0)` để điền vào vị trí (U,I) trống thể hiện _user_ chưa xếp hạng cho _item_
```python
df_pivot = df.pivot(index='ItemID',columns='UserID',values='Rating').fillna(0)
```
Lấy dữ liệu rating
```python
ratings = df_pivot.values
```
### KNN - model
Bạn có thể thay đổi metric bằng `cosine`. Tham khảo [NearestNeighbors](http://scikit-learn.org/stable/modules/generated/sklearn.neighbors.NearestNeighbors.html)
```python
model_knn = NearestNeighbors(metric='euclidean',algorithm='kd_tree')
model_knn.fit(ratings)
```
### Build
giả sử mình chọn toàn bộ rating của _item1_
```python
item1_ratings = df_pivot.iloc[1,:].values.reshape(1,-1)
```
sau đó gọi hàm phía dưới để lấy những item gần nhất với item1
```python
dist,k_item = model_knn.kneighbors(item1_ratings,n_neighbors=10)
```
`dist` ở đây chính là khoảng cách giữa các _item_ với _item1_
```python
dist.flatten()
```
và `k_item` là tên các _item_ đó
```python
k_item.flatten()
``` 
