---
title: Tree based Modeling Part 1
permalink: /2019/03/07/tree_based_modeling_part_1/
category: Data Science
---
*Thuật toán máy học dựa trên mô hình tree-based là một trong những thuật toán được xem là tốt nhất và được nhiều người sử dụng nhất. Nhóm thuật toán này cho kết quả dự đoán có độ chính xác cao và dễ diễn giải.Không giống như mô hình tuyến tính, Tree based Model ánh xạ mối quan hệ phi tuyến rất tốt. Do đó mô hình này thích hợp để giải quyết hầu hết các bài toán phân lớp và hồi quy.Các thuật toán Decision Trees, Random Forest, Gradient Boosting được sử dụng phổ biến trong các bài toán Data Science.*

# Decision Tree là gì? Nó hoạt động như thế nào?
**Decision Tree**(cây quyết định) là một loại thuật toán học máy có giám sát. Thuật toán này có thể dùng cho cả các input và output là dữ liệu thuộc kiểu categorial(có tính phân loại) và các biến liên tục.
**Ví dụ** : Cho dữ liệu mẫu gồm 30 học sinh với 3 biến mô tả : Gender (Boy / Girl),  Class (IX/ X) và Height (5 ~6 ft) trong đó có 15 học sinh chơi Cricket. Chúng ta cần xây dựng mô hình dự đoán học sinh nào sẽ chơi Cricket dựa trên 3 thuộc tính  Gender, Class và Height.

**Decision Tree** sẽ phân chia các học sinh thành các nhóm dựa theo 3 thuộc tính trên sao cho với mỗi nhóm, tỉ lệ số học sinh chơi cricket(hoặc không chơi cricket) là cao nhất *(độ đồng nhất/tinh khiết của dữ liệu là cao nhất). Hình ảnh minh họa dưới đây cho thấy với thuộc tính Gender cho kết quả phân nhóm tốt hơn 2 thuộc tính còn lại.

![Test](https://github.com/lhduc94/Tree_based_modeling/blob/master/image/part1_1.png)

Câu hỏi đặt ra là làm sao Decision Tree chọn được các biến tốt nhất cho việc phân loại. Các phần tiếp theo sẽ trình bày cụ thể các phương pháp chọn các biến đó.

# Các dạng của Decision Tree
Dựa vào đặc điểm của các biến, có thể chia Decision Tree thành 2 dạng :
1. **Categorical Variable Decision Tree** : là dạng Decision Tree mà biến mục tiêu thuộc dạng categorical variable (biến phân loại). Ví dụ ở trên, biến mục tiêu là “học sinh có chơi cricket hay không?” – CÓ hoặc KHÔNG.
2. **Continuous Variable Decision Tree** :   là dạng Decision Tree mà biến mục tiêu thuộc dạng countinous variable (biến liên tục). Ví dụ như bài toán dự đoán giá nhà đất, biến mục tiêu trả về giá trị cụ thể.

**Ví dụ** : Dự đoán khách hàng có tiếp tục gia hạn bảo hiểm của mình hay không?(Yes/No)(Categorical Variable Decision Tree). Có thể nhận thấy rằng thu nhập của một người có ảnh hưởng lớn đến việc khách hàng đó có tiếp tục đóng bảo hiểm hay không, nhưng dữ liệu về thu nhập thể sẽ không đầy đủ đối với tất cả các khách hàng. Do vậy bài toán chuyển thành dự đoán thu nhập của khách hàng tham gia đóng bảo hiểm, từ đó đánh giá xem họ sẽ gia hạn hay không?(Continuous Variable Decision Tree)

# Những thuật ngữ quan trọng trong Decision Tree
Dưới đây là một số thuật ngữ cơ bản :
1. **Root Node**(nút gốc) : Thể hiện toàn bộ dữ liệu mẫu. Dữ liệu này được tiếp tục phân chia thành các nhóm nhỏ hơn.
2. **Splitting**(phân nhóm): Là quá trình chia các nốt thành các nốt nhỏ hơn.
3. **Decision Node**(Nút quyết định): Là nút tiếp tục được phân chia.
4. **Leaf/ Terminal Node** (Nút lá/ nút cuối):  Là những nút không được phân chia.
5. **Prunning**(Tỉa cành) : Loại bỏ một số nút phụ của nút quyết định.
6. **Brand / Sub-Tree** (Nhánh): là một phần của cây
7. **Parent and Child Node** : Nút bị chia thành các nút phụ được gọi là Nút cha, và các nút phụ gọi là Nút con.
![Decision_Tree_2](https://github.com/lhduc94/Tree_based_modeling/blob/master/image/part1_2.png)

# Ưu và nhược điểm của Decision Tree
## Ưu điểm
1. **Dễ hiểu** : Biểu diễn cấu trúc Cây của Decision Tree trực quan, output của Decision Tree dễ hiểu kể cả những người không có nền tảng phân tích thống kê.
2. **Hữu ích cho Data exploration** :  Decisison Tree là một trong những cách nhanh nhất để xác định các biến quan trọng nhất hoặc xác định mối tương quan giữa hai hay nhiều biến. Chúng ta có thể tạo thêm những feature tốt cho việc dự đoán dựa vào Decicion Tree (Tham khảo bài viết
3. **Cần ít dữ liệu** : Decision Tree không cần nhiều dữ liệu như các mô hình khác, nó không bị ảnh hưởng bởi outlier và missing value.
4. **Không phụ thuộc vào loại dữ liệu** : Có thể áp dụng trên cả 2 loại dữ liệu dạng numerical và categorical.
5. **Phi tham số** : Tham số của Decision Tree phụ thuộc vào độ lớn của tập dữ liệu, không cần phải đặt giả định về phân bố của dữ liệu
## Nhược điểm
1. **Over fitting** : Vì không cần nhiều dữ liệu nên dễ bị overfitting.
2. **Không tốt với các biến liên tục** : Quá trình splitting hiệu quả khi các biến mô tả là các biến thuộc dạng categorical.
____________________________________________________________________________________________

Nguồn tham khảo : <https://www.analyticsvidhya.com/blog/2016/04/complete-tutorial-tree-based-modeling-scratch-in-python/>

