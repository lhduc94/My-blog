---
title: Một số package cho Data Science
permalink: /2019/07/19/Some_package_for_Data_Scienc
---

## [datatable](https://datatable.readthedocs.io/en/latest/install.html)
package dành cho việc load file `.csv` lớn một cách nhanh chóng cũng như rút ngắn thời gian `join` 2 bảng với nhau.

Ví dụ
```python
import datatable as dt
import time
t1 = time.time()
# parse all files
train_identity = dt.fread(f'train_identity.csv')
test_identity = dt.fread(f'test_identity.csv')
train_transaction = dt.fread(f'train_transaction.csv')
test_transaction = dt.fread(f'test_transaction.csv')
t2 = time.time()
print("Time to parse: %f" % (t2 - t1))

# join frames
train_identity.key = 'TransactionID'
test_identity.key = 'TransactionID'
train = train_transaction[:, :, dt.join(train_identity)]
test = test_transaction[:, :, dt.join(test_identity)]
t3 = time.time()
print("Time to join: %f" % (t3 - t2))

# turn datatable into Pandas frames
train_df = train.to_pandas()
test_df = test.to_pandas()
t4 = time.time()
print("Time to convert: %f" % (t4 - t3))

print("Total time: %f" % (t4 - t1))
print(train_df.shape)
print(test_df.shape)
```
kết quả
```python
Time to parse: 2.803587                                                         
Time to join: 0.134542
Time to convert: 8.445656
Total time: 11.383785
(590540, 434)
(506691, 433)
```
