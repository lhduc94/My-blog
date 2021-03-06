---
title: Sử dụng Machine Learning để xếp hạng kết quả Search của Airbnb Experiences
category: Data Science
permalink: 2019/06/11/Su_dung_Machine_Learning_de_xep_hang_ket_qua_search_cua_Airbnb_Experiences
---
![img](https://raw.githubusercontent.com/lhduc94/My-blog/master/_posts/Using_Machine_Learning_to_Rank_Airbnb_Search_Result/img2.PNG)
## Giới thiệu:
Khi số lượng trải nghiệm tăng lên, Tìm kiếm và Khám phá cũng như Cá nhân hóa trở thành nhân tố quan trọng cho sự phát triển và thành công của thị trường. Dưới đây là 3 bước của Search Ranking Machine Learning model mà Airbnb áp dụng cho Experiences của họ (Ứng dụng của Airbnb gồm 3 mục chính: Homes, Experiences, Restaurants. Experiences theo như mình hiểu là những loại hình dịch vụ mang tính trải nghiệm như Lướt ván, Lặn, Tham quan di tích nên trong bài dịch mình giữ nguyên từ này):


__Bước 1 - Offline Machine Learning Model:__
* Kích thước dữ liệu: Nhỏ
* Loại dữ liệu: 
  * Thuộc tính của Experiences
* Tính điểm: Offline

__Bước 2 - Offline Personalized Machine Learning Model:__
* Kích thước dữ liệu: Trung bình
* Loại dữ liệu: 
  * Thuộc tính của Experiences
  * Thuộc tính người dùng
* Tính điểm:  Offline

__Bước 3 - Online Personalized Machine Learning Model:__
* Kích thước dữ liệu : Lớn
* Loại dữ liệu: 
  * Thuộc tính của Experiences
  * Thuộc tính người dùng
  * Thuộc tính của câu truy vấn
* Tính điểm:  Online

## Bước 1: Xây dựng Baseline

Khi Airbnb Experiences ra mắt, khối lượng Experiences cần để xếp hạng còn ít và cần thu thập dữ liệu dựa trên tương tác của người dùng như impressions, clicks và bookings. Trong thời gian này, lựa chọn tốt nhất là tái xếp hạng kết quả hằng ngày bằng phương pháp chọn ngẫu nhiên cho đến khi đủ dữ liệu để phát triển cho giai đoạn 1.

__Thu thập dữ liệu huấn luyện:__ Để huấn luyện ML model cho việc xếp hạng, Airbnb thu thập search logs của các khách hàng đã đặt chỗ.
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

Khi sử dụng GBDT chúng ta không cần phải quan tâm đến việc scaling features hay missing values. Tuy nhiên, không giống như mô hình tuyến tính, sử dụng các giá trị đếm thô làm các features cho các model dạng cây sẽ gặp vấn đề khi các biến đếm có xu hướng tăng trưởng nhanh chóng trong thời gian ngắn. Vì vậy, tốt hơn chúng ta nên sử dụng tỉ lệ làm features. Ví dụ, thay vì sử dụng số lượng đặt chỗ trong 7 ngày (ví dụ 10 lượt), chúng ta nên sử dụng tỉ lệ đặt chỗ so với số người xem (ví dụ 12 lượt trên 1000 người xem).

__Kiểm tra Ranking model:__ Để điều chỉnh các hyper-parameter hay so sánh với random Ranking model, Airbnb sử dụng tập hold-out data (là dữ liệu không sử dụng trong tập huấn luyện), và sử dụng các metrics chuẩn cho ranking như AUC và NDCG. Họ xếp hạng lại các Experiences dựa trên score tính từ model (xác suất Đặt chỗ) và kiểm tra xếp hạng của những Expreiences đã được Đặt chỗ trong toàn bộ những Experiences mà người dùng đã click vào (càng cao càng tốt).

Thêm vào đó, để dễ hình dung những gì mà model đã học, họ tiến hành vẽ biểu đồ thể hiện sự phụ thuộc của kết quả đối với các thuộc tính quan trọng của Experiences. Các biểu đồ này sẽ cho ta biết được điều gì sẽ xảy ra với điểm score cho Experiences khi ta thay đổi giá trị của các features ngoại trừ feature cần kiểm tra(Hiểu đơn giản là họ vẽ biểu đồ mối quan hệ giữa score và từng features quan trọng). Kết quả thu được như sau:
* Experiences có nhiều lượt đặt chỗ trên 1k người xem sẽ được xếp hạng cao hơn
* Experiences với trung bình các đánh giá cao hơn sẽ được xếp hạng cao hơn
* Expreieces có giá thấp hơn sẽ được xếp hạng cao hơn
![img3](https://raw.githubusercontent.com/lhduc94/My-blog/master/_posts/images_2019-06-11-Su-dung-Machine-learning-Xep-hang-ket-qua-Search-cua-Aribnb/img3.png)

Vì việc kiểm tra offline có quá nhiều giả định, dữ liệu bị giới hạn bởi những gì mà người dùng đã clicks và những dữ liệu này chỉ là một phần trong toàn bộ kho dữ liệu, vì vậy họ tiến hành A/B test. Họ so sánh ML model này với việc xếp hạng dựa trên quy tắc về số lượng đặt chỗ. Kết quả cho thấy Mô hình tăng thêm 13% số lượng đặt chỗ.

__Chi tiết thực hiện:__ Ở bước này, ML model chỉ sử dụng các thuộc tính của Experience nên kết quả xếp hạng sẽ giống nhau đối với toàn bộ người dùng, và các tham số truyền vào khi truy vấn (số lượng đặt, ngày đặt, địa điểm) chỉ được dùng để lọc các kết quả trả về, điểm ranking không phụ thuộc vào các tham số này.

## Bước 2 - Personalize
Bước tiếp theo trong việc phát triển Search Ranking là thêm Cá nhân nhóa vào Machine Learning model. Cá nhân hóa đóng vai trò lớn trong việc ranking Experiences bởi tính đa dạng của kho dữ liệu và sở thích người dùng, việc áp dụng này giúp tiết kiệm được thời gian chọn lựa của người dùng cũng như tăng khả năng "thành công" của Experiences được Đặt chỗ.

Không giống như dịch vụ Homes, hai _Private Room_ cùng ở chung một thành phố với cùng mức giá thì rất giống nhau, trong khi đó hai Experiences được chọn ngẫu nhiên có thể rất khác nhau (ví dụ _Lớp học nấu ăn_ và _Lớp học lướt sóng_). Đồng thời, du khách có những sở thích và ý muốn trong chuyến du lịch của họ. Mục tiêu của Airbnb là làm sao nắm bắt được sở thích của họ một cách nhanh chóng và trả ra nội dung phù hợp.

Airbnb giới thiệu 2 cách khác nhau cho Cá nhân hóa khác nhau dựa trên dữ liệu thu thập từ người dùng.
### 1. Cá nhân hóa dựa trên Đặt chỗ trên Airbnb Homes
Phần lớn các lượt Đặt chỗ trên Experiences đến từ người dùng đã đặt chỗ trên Airbnb Home. Do đó có khá nhiều thông tin để có thể xây dựng các features cho Cá nhân hóa:
* Địa điểm đặt chỗ trên Homes
* Ngày đi
* Đi trong bao lâu
* Số lượng khách
* Giá chuyến đi
* Đi kiểu Gia đình / Business
* Địa điểm khứ hồi
* Đi trong nước / Quốc tế
* (First trip or returning to location)
* Khoảng thời gian đặt phòng đến lúc nhận phòng (Lead days)
Để chứng minh các features trên có thể dùng để tính ranking, Airbnb đưa ra 2 dẫn chứng quan trọng:
* _Khoảng cách(Distance) giữa Home đã được Đặt trước và Experience._. Biết được địa điểm Đặt chỗ Home(vĩ độ và kinh độ) cũng như địa điểm của Experience, chúng ta có thể tính được khoảng cách giữa 2 địa điểm. Dữ liệu cho thấy du khách thích sự tiện lợi, phần lớn các Experiences được đặt gần với Home.
* _Các Experience có thể có trong Chuyến đi_ : Dựa vào ngày nhận phòng và trả phòng, chúng ta có thể liệt kê và đánh dấu các Experiences có trong khoảng thời gian này.
