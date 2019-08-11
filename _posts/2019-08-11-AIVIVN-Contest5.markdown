---
title: AIVIVN Contest 5: BANDWIDTH PREDICTION
category: Data Science
permalink: 2019/08/11/aivivn_contest_5
author: Le Huynh Duc
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
