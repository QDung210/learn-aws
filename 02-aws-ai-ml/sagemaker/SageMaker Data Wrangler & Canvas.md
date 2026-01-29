# SageMaker Data Wrangler & Canvas 

## 1️⃣ Câu hỏi cốt lõi

> **Data Wrangler vs Canvas?**
> - **Data Wrangler**: [[ETL]] kéo-thả cho data scientist, xuất code
> - **Canvas**: No-code ML cho business users, không viết code

So sánh: [[Glue vs Data Wrangler]]. Xem thêm: [[Amazon SageMaker AI]]

> 📌 **Xem thêm**: [[Amazon SageMaker AI]] - Tổng quan về SageMaker ecosystem

## 2️⃣ SageMaker **Data Wrangler** là gì?

👉 **Công cụ làm sạch & chuẩn bị dữ liệu ([[ETL]]) dạng kéo-thả** trong **SageMaker Studio**  
👉 Dành cho **data scientist / ML engineer**, không phải tool chạy pipeline production lâu dài

### Nói ngắn gọn

- Data Wrangler = **Excel + Pandas + Visualization**, nhưng chạy trên AWS
- Làm xong → **xuất ra Notebook code** (Python) để đem đi train tiếp
### 🔹 Data Wrangler dùng để làm gì?
- Import dữ liệu
- Làm sạch, biến đổi, kiểm tra chất lượng dữ liệu
- Xem dữ liệu “có ổn để train ML chưa”
### 🔹 Nguồn dữ liệu hỗ trợ
- **S3** (phổ biến nhất)
- **Athena / Lake Formation**
- **Redshift**
- **Feature Store**
- **Database bên ngoài** (JDBC, SaaS)
- **Databricks**
👉 Hiểu đơn giản: _hầu hết dữ liệu doanh nghiệp đều kéo vào được_

---

### 🔹 Bạn làm được gì trong Data Wrangler?
- Hơn **300 phép biến đổi**:
    - Xử lý missing value
    - Chuẩn hóa, encode
    - Tách cột, đổi kiểu dữ liệu
    - Remove duplicate
- **Visualize dữ liệu**
    - Histogram, phân phối
    - Outlier
- **Quick Model**
    - Train nhanh 1 model thử
    - Xem **feature nào ảnh hưởng mạnh**
    - Không dùng để deploy, chỉ để **hiểu dữ liệu**
### 🔹 Sau khi làm xong thì sao?
Bạn **không chạy pipeline lâu dài trong Data Wrangler**, mà:

- Export ra:
    - **Jupyter Notebook (Python code)**
    - hoặc **CSV đã xử lý**
- Rồi dùng Notebook / SageMaker training / Canvas để train tiếp

### ⚠️ Lưu ý hay gặp

- Phải cấp quyền **AmazonSageMakerFullAccess** cho dữ liệu
- Nếu báo:
    > `instance type not available`  
    > → Vào **Service Quotas**  
    > → tăng quota **Studio Kernel Gateway – ml.c5.4xlarge**
## 2️⃣ SageMaker **Canvas** là gì?

👉 **No-code ML** cho người **không muốn viết code**

### Nói ngắn gọn

- Canvas = **kéo thả để train & deploy model**
- Dành cho **business / analyst / người không rành ML**
### 🔹 Canvas làm được gì?
- Import dữ liệu từ:
    - S3
    - CSV upload
- Xử lý dữ liệu cơ bản
    - **Dùng Data Wrangler tích hợp bên trong**
- Train model chỉ bằng vài click
### 🔹 Canvas hỗ trợ bài toán nào?

- **Regression / Classification**
- **Time series forecasting**
- **Object detection**
    - Dùng model có sẵn từ:
        - Rekognition
        - Textract
        - Comprehend
- **GenAI chatbot** (tab riêng)
### 🔹 Kết quả dùng thế nào?

- Predict:
    - **Single**
    - **Batch**
- Output:
    - Lưu về **S3**
    - Xuất CSV
    - Đẩy sang **QuickSight**
## 3️⃣ Luồng làm việc chuẩn (dễ hình dung)

```text
Dữ liệu thô
   ↓
Data Wrangler
   ↓
Làm sạch + transform + hiểu feature
   ↓
Quick Model (check nhanh)
   ↓
Export Notebook / CSV
   ↓
Canvas hoặc SageMaker Training
```

### Ví dụ thực tế

- Bạn có file CSV bán hàng
- Data Wrangler:
    - Fix số bị `"1,000"` → `1000`
    - Xóa duplicate
    - Encode category
- Quick Model:
    - Thấy `price` ảnh hưởng mạnh nhất
- Export CSV
- Canvas:
    - Train model dự báo doanh thu
    - Xuất kết quả cho business xem

---

## 4️⃣ Tóm tắt 1 dòng cho dễ nhớ

- **Data Wrangler** → _Chuẩn bị & hiểu dữ liệu (ETL + EDA)_
- **Canvas** → _Train & dùng model không cần code_
- 
Nếu dùng kết quả để tinh chỉnh Bedrock: xem [[Tinh chỉnh & LoRA trên Bedrock]]; cần ETL production so sánh Glue: [[AWS Glue & Data Catalog]].
