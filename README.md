# FUNiX's DSP305x_01

# Vũ Văn Dũng - dungvvfx10285@funix.edu.vn

## TỔNG QUAN ĐỒ ÁN
- Xây dựng model phát hiện gian lận thẻ giao dịch khống tại các merchant
- Dạng bài toán: classification (2 nhãn là merchant giao dịch khống/merchant hoạt động bình thường)

## 1. Business Understanding (Hiểu biết về nghiệp vụ)
- Các merchant giao dịch khống là một hình thức gian lận trong nghiệp vụ thẻ tại các ngân hàng, cần theo dõi, phát hiện và xử lý đối với các merchant gian lận
## 2. Data Understanding (Hiểu biết về dữ liệu)
### 2.1. Mô tả dữ liệu:
- Tập dữ liệu: dữ liệu tập kết trong vòng 6 tháng bao gồm các thông tin liên quan đến các merchant. Đối với Merchant gian lận sẽ lấy dữ liệu tại ngày mà merchant đó được xác định là gian lận.
- Dữ liệu bao gồm ~57K rows dữ liệu với 382 trường dữ liệu số và category 
### 2.2. Exploratory Data Analysis - EDA (Phân tích dữ liệu khám phá):
- Thực hiện visualize dữ liệu đưa ra nhận xét: **VisualizeData06Resample.ipynb**
  + Dữ liệu imbalance rất lớn: **752 Fraud/ 56710 bình thường**
  + Số thuộc tính có mức độ tương quan cao nhiều
  + Số thuộc tính có giá trị null ít
  + Các thuộc tính thường không tuân theo phân bố chuẩn  
## 3. Data Preparation (Chuẩn bị dữ liệu)
- Thực hiện làm sạch dữ liệu: loại bỏ 183 trường dữ liệu
  + Loại bỏ những trường dữ liệu nào có > 50% giá trị null
  + Loại bỏ các trường có mức độ tương quan tuyến tính cao
  + Xây dựng pipepline transfrom dữ liệu  
- Xây dựng tập dữ liệu train, val, test: thực hiện resampling với tập dữ liệu để xử lý việc dữ liệu bị imbalance
## 4. Modeling (Lập mô hình, mô hình hóa)
- Xây dựng mô hình: **Funix_RandomForestModelData06Resample.ipynb**
- Dùng phương pháp SMOTE để xử lý imbalance của dữ liệu
- Xây dựng model bằng thuật toán randomforest
- Dùng RandomizedSearchCV để xác định các tham số tối ưu nhất cho mô hình
  {'classifier__bootstrap': False,
 'classifier__max_depth': 50,
 'classifier__max_features': 'auto',
 'classifier__min_samples_leaf': 1,
 'classifier__min_samples_split': 10,
 'classifier__n_estimators': 1000}
## 5.  Model Evaluation (Đánh giá mô hình)
- Đánh giá mô hình trên tập dữ liệu test: dựa trên metric là recall

![image](https://user-images.githubusercontent.com/21106518/163090307-a2f8c1f6-a16a-4b4d-b4a3-64f17935cd09.png)


- Kết quả mô hình đặt tỉ lệ recal là 98%
