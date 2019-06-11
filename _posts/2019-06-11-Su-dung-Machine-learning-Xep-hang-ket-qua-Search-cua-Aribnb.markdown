---
title: Sử dụng Machine Learning để xếp hạng kết quả Search của Airbnb Experiences
category: Data Science
permalink: 2019/06/11/Su_dung_Machine_Learning_de_xep_hang_ket_qua_search_cua_Airbnb_Experiences
---
![img](https://raw.githubusercontent.com/lhduc94/My-blog/master/_posts/images_2019-06-11-Su-dung-Machine-learning-Xep-hang-ket-qua-Search-cua-Aribnb/img2.PNG)
## Giới thiệu:

Khi số lượng trải nghiệm tăng lên, Tìm kiếm và Khám phá cũng như Cá nhân hóa trở thành nhân tố quan trọng cho sự phát triển và thành công của thị trường. Dưới đây là 3 bước của Search Ranking Machine Learning model mà Airbnb áp dụng:
* __Bước 1 - Offline Machine Learning Model:__
    * Kích thước dữ liệu: Nhỏ
    * Loại dữ liệu: Thuộc tính của Expreiences
    * Tính điểm: Offline
* __Bước 2 - Offline Personalized Machine Learning Model:__
    * Kích thước dữ liệu : Trung bình
    * Loại dữ liệu : 
        * Thuộc tính của Expreiences
        * Thuộc tính người dùng
    * Tính điểm :  Offline
* __Bước 2 - Online Personalized Machine Learning Model:__
    * Kích thước dữ liệu : Lớn
    * Loại dữ liệu : 
        * Thuộc tính của Expreiences
        * Thuộc tính người dùng
        * Thuộc tính của câu truy vấn
    * Tính điểm :  Online

## Bước 1: Xây dựng Baseline

Khi Airbnb Experiences ra mắt, khối lượng trải nghiệm người dùng cần để xếp hạng còn ít và cần thu thấp dữ liệu dựa trên tương tác của người dùng như impressions, clicks và bookings. Trong thời gian này, lựa chọn tốt nhất là tái xếp hạng kết quả hằng ngày bằng phương pháp chọn ngẫu nhiên cho đến khi đủ dữ liệu để phát triển cho giai đoạn 1. 

__Thu thập dữ liệu huấn luyện:__ Để huấn luyện ML model cho việc xếp hạn, Airbnb thu thập search logs của các khách hàng đã đặt chỗ.

![img1](https://cdn-images-1.medium.com/max/1200/1*6oFrH49leqhJR2fd2wRHpQ.png). 


__Gán nhãn dữ liệu huấn luyện:__ Khi gán nhãn cho dữ liệu huấn luyện, Airbnb chủ yếu tập trung vào 2 nhãn: *Đặt chỗ* (positive labels) và *Click xem nhưng không Đặt chỗ* (negative labels). Theo cách này, họ thu thập được 50,000 mẫu dữ liệu. 

__Xây dựng các thuộc tính của dữ liệu được huấn luyện:__ Trong giai đoạn 1 của ML model, họ quyết định xếp hạng dựa trên *Các Thuộc tính của Expreiences*. Tổng cộng có 25 thuộc tính trong đó có thể liệt kê như:
   * Thời lượng (1h, 2h, 3h,....)
   * Giá và Giá/giờ
   * Thể loại (lớp nấu ăn, âm nhạc, lướt sóng)
   * Đánh giá (ratings, số lượng views)
   * Số lượng Đặt chỗ (7 ngày gần đây, 30 ngày gần đây)
   * Số lượng phòng còn trống, đã đặt trước
   * Số lượng người tối đa (ví dụ tối đa 5 người)
   * Click-through rate.

__Huấn luyện Ranking model:__ Airbnb sử dụng [Gradient Boosted Decision Tree(GBDT)](https://github.com/yarny/gbdt) model để huấn luyện dữ liệu này. Họ giải quyết bài toán này như một bài toán *Binary Classification* với *log-loss loss function*. 

Khi sử dụng GBDT chúng ta không cần phải quan tâm đến việc scaling features hay missing values. Tuy nhiên, không giống như mô hình tuyến tính, sử dụng các giá trị đếm thô làm các features cho các model dạng cây sẽ gặp vấn đề khi các biến đếm có xu hướng tăng trưởng nhanh chóng trong thời gian ngắn. Vì vậy, tốt hơn chúng ta nên sử dụng tỉ lệ làm featurs. Ví dụ, thay vì sử dụng số lượng đặt chỗ trong 7 ngày (ví dụ 10 lượt), chúng ta nên sử dụng tỉ lệ đặt chỗ so với số người xem (ví dụ 12 lượt trên 1000 người xem). 

__Kiểm tra Ranking model:__ Để điều chỉnh các hyper-parameter hay so sánh với random Ranking model, chúng ta sử dụng tập hold-out data (là dữ liệu không sử dụng trong tập huấn luyện), và sử dụng các metrics chuẩn cho ranking như AUC và NDCG. 
