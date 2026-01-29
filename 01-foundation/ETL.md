# ETL (Extract - Transform - Load)

## 1️⃣ Câu hỏi cốt lõi

> **ETL là gì?**
> - **Lấy dữ liệu → sửa dữ liệu → đưa vào nơi dùng**
> - 70-80% thời gian làm ML là ETL
> - AWS tools: [[AWS Glue & Data Catalog]], [[SageMaker Data Wrangler & Canvas]]

---

ETL là viết tắt của **Extract – Transform – Load**.
## ETL là gì? (bản đời thường)

👉 **Lấy dữ liệu → sửa dữ liệu → đưa vào nơi dùng**
## 1️⃣ Extract (Lấy dữ liệu)

Lấy dữ liệu từ các nguồn khác nhau:

- File CSV / Excel
- Database
- API
- S3, Redshift, MySQL, …

📌 Ví dụ:
- Lấy dữ liệu bán hàng từ S3
- Lấy dữ liệu khách hàng từ database
## 2️⃣ Transform (Xử lý / làm sạch)

Dữ liệu thô **chưa dùng được**, cần xử lý:

- Xóa dữ liệu trùng
- Điền missing value
- Đổi `"1,000"` → `1000`
- Chuẩn hóa ngày tháng
- Encode category (Nam/Nữ → 0/1)
- Tạo feature mới
📌 Đây là bước **quan trọng nhất**
## 3️⃣Load (Đưa vào nơi dùng)

Đưa dữ liệu đã sạch vào:

- Data Warehouse
- Data Lake
- Feature Store
- Nơi để:
    - BI (dashboard)
    - ML training
    - Analytics

📌 Ví dụ:
- Load lại vào S3
- Load vào Redshift
- Load vào Feature Store cho model dùng

## ETL trong ML thì sao?

Trong **Machine Learning**:

- ETL = **data preprocessing**
- Data xấu → model tệ ❌
- Data sạch → model ngon ✅
👉 70–80% thời gian làm ML là ETL
## ETL vs ELT (hay gặp trong AWS)

|ETL|ELT|
|---|---|
|Transform trước|Load trước|
|Xử lý bên ngoài|Xử lý trong warehouse|
|Dùng Spark / Glue|Dùng Redshift / BigQuery|

📌 AWS Glue thường làm **ETL**,  
📌 Athena / Redshift hay dùng **ELT**
## Ví dụ gắn với SageMaker

- **Data Wrangler** → làm **Transform**
- **S3 / Feature Store** → nơi **Load**
- **Model training** → dùng dữ liệu đã ETL