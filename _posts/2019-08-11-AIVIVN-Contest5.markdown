---
title: AIVIVN Contest 5 - Bandwidth Prediction 2
permalink: /2019/03/07/bandwidth_prediction_2/
category: Data Science
---
## Giới thiệu:
Một công ty cung cấp nền tảng giải trí cho phép user sử dụng các dịch vụ music, video, live stream, chat, … 
Hệ thống công ty chia thành các zone theo khu vực địa lý. Để đáp ứng số lượng user ngày càng tăng, 
công ty muốn dự đoán được tổng bandwidth của mỗi server và số lượng tối đa user truy cập đồng thời vào server 
trong vòng một tháng tiếp theo để lên kế hoạch hoạt động.

## Dữ liệu:
Dữ liệu được mô tả trong tập `train.csv`
```csv
UPDATE_TIME,ZONE_CODE,HOUR_ID,BANDWIDTH_TOTAL,MAX_USER
2017-10-01,ZONE01,0,16096.71031272728,212415.0
2017-10-01,ZONE01,1,9374.20790727273,166362.0
2017-10-01,ZONE01,2,5606.225750000003,146370.0
2017-10-01,ZONE01,3,4155.654660909094,141270.0
2017-10-01,ZONE01,4,3253.9785936363623,139689.0
```

- `UPDATE_TIME`: ngày thực hiện lấy dữ liệu
- `HOUR_ID`: giờ thực hiện lấy dữ liệu
- `ZONE_CODE`: mã khu vực
- `BANDWIDTH_TOTAL`: tổng băng thông truy cập tương ứng trong vòng 1 giờ
- `MAX_USER`: số user truy cập đồng thời tối đa trong vòng 1 giờ (là một số tự nhiên)

Trong đó `UPDATE_TIME, HOUR_ID, ZONE_CODE` được định nghĩa như trên, id là mã số tương ứng cho file nộp bài. Các đội chơi cần dự đoán `BANDWIDTH_TOTAL`, và `MAX_USER` cho mỗi dòng. 

## Metrics đánh giá
$$SMAPE = \frac{100\%}{N}\sum_{t=1}^{N}\frac{|F_t - A_t|}{|F_t| +|A_t|}$$

Với $$A_t$$ là giá trị thực còn $$F_t$$ là giá trị dự  đoán.  Mình có code  lại metric trong bài của mình: 
```python
import numpy as np
def mean_absolute_percentage_error(a, b): 
    a = np.array(a)
    b = np.array(b)
    mask = a != 0
    return (np.abs(a - b)/a)[mask].mean()*100

def smape(a, b): 
    a = np.array(a)
    b = np.array(b)
    mask = a != 0
    return (np.abs(a - b)/(np.abs(a)+np.abs(b)))[mask].mean()*100
```

## Xử lý dữ liệu:
Load dữ liệu:
```python
train_df = pd.read_csv('train.csv')
test_df = pd.read_csv('test_id.csv')
sub = pd.read_csv('sample_submission.csv')
```
Ở Contest trước có sự trùng lặp dữ liệu nên mình tiến hành loại bỏ những dữ liệu trùng lặp.
```python
#Drop duplicates and set time index
train_df.drop_duplicates(inplace=True)
train_df['DATE_TIME'] = train_df['UPDATE_TIME'] + ' ' + (train_df['HOUR_ID'].astype(str)) + ':00:00'
train_df['DATE_TIME'] = pd.to_datetime(train_df['DATE_TIME'])
train_df.set_index('DATE_TIME',inplace=True)
```
Ngoài ra, để tránh missing dữ liệu, mình tiến hành fill missing value. Bước đầu tiên mình viết 1 hàm tạo `time` đầy đủ với `start` là ngày đầu tiên và `end` là ngày cuối cùng
```python
def generate_date_time_series(d):
    start = d[0]
    end = d[1]
    step = datetime.timedelta(hours=1)

    result = []

    while start < end:
        result.append(start.strftime('%Y-%m-%d %H:%M:%S'))
        start += step
    result.append(d[1])
    return result
```
Với mỗi ZoneName mình tiến hành fill missing value, mỗi vị trí bị thiếu sẽ được điền bằng trung bình giá trị tại 4 ngày trước. Ở đây mình có set `Nlog=True` để chuẩn hóa dữ liệu về dạng `log(N)` 
```python
t1 = generate_date_time_series(train_df[train_df['ZONE_CODE'] == name].index[[0,-1]])
SERVER = train_df[train_df['ZONE_CODE'] == name]
x = pd.DataFrame({'DATE_TIME':t1,'BANDWIDTH_TOTAL':np.nan, 'MAX_USER':np.nan})
x['DATE_TIME'] = pd.to_datetime(x['DATE_TIME'])
x.set_index('DATE_TIME',inplace=True)

df = pd.concat([SERVER[['BANDWIDTH_TOTAL','MAX_USER']],x])
df1 = df[~df.index.duplicated(keep='first')]
df1.sort_index(inplace=True)
if Nlog:
    df1.replace(0,0.000003,inplace=True)

TIMESERIAL = df1['BANDWIDTH_TOTAL'].values.copy()
xx = np.argwhere(np.isnan(TIMESERIAL)).flatten()
for i in xx:
    TIMESERIAL[i] = np.nanmean([ TIMESERIAL[i-24*j] for j in range(1,4)])
if Nlog:
    TIMESERIAL = np.log(TIMESERIAL)
```
