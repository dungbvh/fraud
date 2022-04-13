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
### 4.1. Base model
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
### 4.2. Hyperparameter tuning với XGBoost và RandomForest
- Xây dựng 2 pipepline
 + Pipeline 1: xử lý toàn bộ các cột dữ liệu kiểu số
 + Pipeline 2: xử lý code các cột dữ liệu 'CURR_NUM', 'MP_IND', 'PAY_METHOD', 'HAS_TXN_LESS_15S' theo OrdinalEncoder
- Thực hiện hyper parameter tuning bằng Optuna
- Hyperparameter tuning với RandomForest: **Funix RandomForest hyperparameter tuning.ipynb** 
- Hyperparameter tuning với XGBoost: **Funix XGBoost hyperparameter tuning.ipynb**
### 4.3. Model Blending 
- Thực hiện modele blending:
  + ('svc', SVC(C=1000, gamma= 0.01, kernel='rbf'))
  + ('XGBClassifier', XGBClassifier(alpha=0.004016238677167677, colsample_bytree=0.3, subsample=0.6, learning_rate=0.02, max_depth=9, random_state=48, min_child_weight=2, reg_lambda=4.248186489909531))
  + ('RandomForestClassifier', RandomForestClassifier(max_depth=9,max_leaf_nodes=8,min_samples_split=7, n_estimators=175))
  + ('lr', LogisticRegression())
  + ('bayes', GaussianNB())
## 5.  Model Evaluation (Đánh giá mô hình)
- Đánh giá mô hình trên tập dữ liệu test: dựa trên metric là recall
## 5.1. Pipepline 1
### 5.1.1. RandomForest
- Best params:
    {
    'max_depth': 10,
     'max_leaf_nodes': 16,
     'min_samples_split': 4,
     'n_estimators': 466
    }
 
- Kết quả thực hiện

![image](https://user-images.githubusercontent.com/21106518/163116137-7158271c-6f25-4af6-ad97-c70e00f68825.png)

### 5.1.2. XGBoost
- Best param: 
 {'alpha': 0.7881845480038765,
 'colsample_bytree': 1.0,
 'lambda': 0.0038818469818502105,
 'learning_rate': 0.02,
 'max_depth': 15,
 'min_child_weight': 1,
 'random_state': 2020,
 'subsample': 0.6}
- Kết quả 

![image](https://user-images.githubusercontent.com/21106518/163117334-90609858-e4a7-4247-bcb1-a83a6869d06d.png)


### 5.1.3. Model blending
- Kết quả thực hiện

![image](https://user-images.githubusercontent.com/21106518/163116591-f0908eb5-f33e-434a-8934-406097f1a211.png)


## 5.2. Pipepline 2
### 5.2.1. RandomForest
- Best params:
    {'max_depth': 10,
     'max_leaf_nodes': 29,
     'min_samples_split': 2,
     'n_estimators': 402}
- Kết quả

![image](https://user-images.githubusercontent.com/21106518/163116917-5c24beaa-e4bd-481d-a94f-622ad1119f1b.png)

### 5.2.2. XGBoost
- Best params:
{'alpha': 0.027965096689938844,
 'colsample_bytree': 0.3,
 'lambda': 0.14078870210197214,
 'learning_rate': 0.018,
 'max_depth': 11,
 'min_child_weight': 1,
 'random_state': 48,
 'subsample': 0.6}

- Kết quả: 

![image](https://user-images.githubusercontent.com/21106518/163117196-3286d507-67dc-44ef-9196-a626bbbc1976.png)

### 5.2.3. Model Blending

- Kết quả

![image](https://user-images.githubusercontent.com/21106518/163118580-dbabdb7d-c196-403b-aef8-e2bfc17e34ad.png)


**Đánh giá**
Cả pipeline 1 và 2, model blending đều cho kết quả tốt hơn so với model đơn lẻ là RandomForest hoặc XGBoost

