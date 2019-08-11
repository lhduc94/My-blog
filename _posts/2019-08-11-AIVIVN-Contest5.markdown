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
