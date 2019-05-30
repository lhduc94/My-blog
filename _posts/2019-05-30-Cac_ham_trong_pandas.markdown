---
title: Các hàm trong Pandas
category : Data Science
permalink: /2019/05/30/Cac_ham_trong_pandas
---

### pandas.cut
Sử dụng để Phân đoạn dữ liệu trong khoảng từ (a,b) thành các bins khác nhau và gán nhãn cho từng đoạn.

Giả sử ta có một `np.array([1, 2, 3, 4, 5, 6, 7, 8, 9])` cần phân thành 3 đoạn khác nhau. Ta có thể sử dụng hàm `pandas.cut` như sau
```python
pd.cut(np.array([1, 2, 3, 4, 5, 6, 7, 8 , 9]), bins = 3)
```
Ta có thể gán nhãn cho các phân đoạn này bằng cách thêm vào trường `labels`
```python
pd.cut(np.array([1, 2, 3, 4, 5, 6, 7, 8 , 9]), bins = 3, labels = ['Low','Medium','High'])
```
Ngoài ra bins có thể là 1 danh sách khoảng thay vì số. Ví dụ ta có 3 khoảng `(-inf, 3], (3, 6], (6, inf)` 
Ví dụ
```python
pd.cut(np.array([1, 2, 3, 4, 5, 6, 7, 8 , 9]), bins = [-np.inf, 3, 6, np.inf], labels = ['Low','Medium','High'])
```
