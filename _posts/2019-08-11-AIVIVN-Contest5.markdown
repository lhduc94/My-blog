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
Dữ liệu được mô tả trong tập `train.csv` hoặc tại [đây](https://www.aivivn.com/contests/5)
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
Với mỗi ZoneName mình tiến hành fill missing value cho `BANDWIDTH`, về `MAX_USER` mình sẽ xử lý cách khác. Mỗi vị trí bị thiếu sẽ được điền bằng trung bình giá trị tại 4 ngày trước. Ở đây mình có set `Nlog=True` để chuẩn hóa dữ liệu về dạng `log(N)`. Để tránh bị lỗi `log(0)` mình replace những giá trị bằng `0` bằng `0.000003` (đây là giá trị nhỏ hơn giá trị min của `BANDWIDTH` của bài trước). Các bạn có thể thay thế hàm `np.log` bằng `np.log1p` mà không cần quan tâm tới giá trị 0. 

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
## Xây dựng dữ liệu training cho BANDWIDTH:
Có 2 cách để xây dựng dữ liệu training:
* Cách 1: Cho chuỗi thời gian $$seq$$, với mỗi giá trị $$y = seq_t$$ mình chọn $$x$$ là $$seq_{t-1 - n\_steps}$$ đến $$seq_{t-1}$$
* Cách 2: Cho chuỗi thời gian $$seq$$, với mỗi giá trị $$y = seq_t$$ mình chọn $$x$$ bao gồm các giá trị của giờ đó những ngày hôm trước cùng với mean,min,max của các giá trị đó. Ngoài ra mình còn thêm vào 24 giá trị gần nhất.

Code
```python
def split_1(sequence, n_steps):
    X, y = list(), list()
    for i in range(len(sequence)):
        end_ix = i + n_steps
        if end_ix > len(sequence)-1:
            break
        seq_x, seq_y = sequence[i:end_ix], sequence[end_ix]
        X.append(seq_x)
        y.append(seq_y)
    
    return np.array(X), np.array(y)

def split_2(sequence, n_steps):
    a = list(sequence)
    X,y = list(), list()
    for i in reversed(range(len(a))):
        if i == n_steps-1:
            break
        seq_y = a[i]
        seq_x = a[i-n_steps:i:24] + a[i-24:i] \
                +[np.mean(a[i-n_steps:i:24]),
                  np.min(a[i-n_steps:i:24]),
                  np.max(a[i-n_steps:i:24])]
        X.append(seq_x)
        y.append(seq_y)
    return np.array(X), np.array(y)
```

## Mô hình dự đoán BANDWIDTH
Cũng như 2 bạn Rank 1,2. Mình cũng chọn `Linear Regression`, và cụ thể hơn là `Ridge`
```python
from sklearn.linear_model import Ridge
Nlog=True
def model_1(TIMESERIAL):
    
    train,test = TIMESERIAL[:-24*31],TIMESERIAL[-24*31:]
    n_steps = 24*31
    X, y = split_1(TIMESERIAL,n_steps)
    X_train = X
    y_train = y
    
    model = Ridge()
    model.fit(X_train, y_train)
    pred = model.predict(X_train)
    if Nlog:
        y_train = np.exp(y_train)
        pred = np.exp(pred)
    print(smape(y_train,pred))
    
    bw_pred = []
    for i in range(24*31):
        seq = TIMESERIAL[-n_steps:]
        yhat = model.predict(np.array([seq]))
        TIMESERIAL = np.append(TIMESERIAL,yhat[0])
        bw_pred.append(yhat[0])
    if Nlog:
        bw_pred = np.exp(bw_pred)
        
    return bw_pred

def model_2(TIMESERIAL):
    
    train,test = TIMESERIAL[:-24*31],TIMESERIAL[-24*31:]
    n_steps = 24*31
    X, y = split_2(TIMESERIAL,n_steps)
    X_train = X
    y_train = y
    
    model = Ridge()
    model.fit(X_train, y_train)
    pred = model.predict(X_train)
    if Nlog:
        y_train = np.exp(y_train)
        pred = np.exp(pred)
    print(smape(y_train,pred))
    
    bw_pred = []
    for j in range(24*31):
        i = len(TIMESERIAL)
        seq = np.append(TIMESERIAL[i-n_steps:i:24],TIMESERIAL[i-24:i])
        seq = np.append(seq,np.array([np.mean(TIMESERIAL[i-n_steps:i:24]),
                  np.min(TIMESERIAL[i-n_steps:i:24]),
                  np.max(TIMESERIAL[i-n_steps:i:24])]
            ))
        yhat = model.predict(np.array([seq]))
        TIMESERIAL = np.append(TIMESERIAL,yhat[0])
        bw_pred.append(yhat[0])
    if Nlog:
        bw_pred = np.exp(bw_pred)
        
    return bw_pred
 ```
Mình train và predict cho từng ZONENAME và kết hợp 2 model lại với nhau theo tham số `[0.67, 0.33]`
```python
bw_pred_1 = model_1(TIMESERIAL)
bw_pred_2 = model_2(TIMESERIAL)
bw_pred = bw_pred_1*0.67 + bw_pred_2*0.33
```

## Dự đoán MAX_USER
Về MAX_USER mình không dự đoán mà chỉ lấy giá trị của ngày cuối cùng (đầy đủ 24 giá trị) làm kết quả.
```python
list_df_2 = []
for idx,name in enumerate(test_df['ZONE_CODE'].unique()):
    print(idx,name)
    t = pd.DataFrame(train_df[train_df['ZONE_CODE']==name].groupby(['UPDATE_TIME']).size(),columns = ['num_hours'])
    t2 = train_df[(train_df['ZONE_CODE']==name)&(train_df['UPDATE_TIME']==t[t['num_hours']==24].index[-1])]
    list_df_2.append(t2)
mu_df = pd.concat(list_df_2)[['ZONE_CODE','HOUR_ID','MAX_USER']] 
mu_df.head()
```

## Kết luận
Mình chỉ xử lý dữ liệu cơ bản, chưa xử lý nhiễu cũng như **feature engineering** nên kết quả không được cao. Các bạn có thể dùng các kỹ thuật xử lý mà bạn top 1,2 trình bày để tối ưu kết quả.

Source code đầy đủ của mình tại [đây](https://github.com/lhduc94/Aivivn-5-bandwidth-prediction-2) 
