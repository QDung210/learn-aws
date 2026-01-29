# AWS Glue vs SageMaker Data Wrangler

## 1️⃣ Câu hỏi cốt lõi

> **Khi nào chọn cái nào?**
> - **Glue**: Production ETL pipelines, batch processing lớn
> - **Data Wrangler**: Khám phá dữ liệu, prototype transforms, xuất code

---

## Tổng quan nhanh

| Tiêu chí | AWS Glue | Data Wrangler |
|----------|----------|---------------|
| **Mục đích** | ETL production, pipeline lâu dài | Khám phá, chuẩn bị dữ liệu ML |
| **Người dùng** | Data Engineer | Data Scientist, ML Engineer |
| **Engine** | Apache Spark (managed) | SageMaker Studio (interactive) |
| **Chạy dài hạn** | ✅ Có | ❌ Không |
| **Output** | Dữ liệu → S3/Catalog | Code Python/Notebook |
| **Scaling** | Tự động scale Spark | Chạy trên Studio instance |

---

## Khi nào dùng AWS Glue?

✅ **Pipeline ETL production** chạy hàng ngày/giờ  
✅ **Xử lý dữ liệu lớn** (TB+) với Spark  
✅ **Cần Data Catalog** để Athena/Redshift query  
✅ **Tự động hóa** với schedule/event trigger  
✅ **Serverless** - không quản lý cluster  

### Workflow điển hình
```
S3 Raw Data → Crawler → Data Catalog → Glue ETL Job → S3 Processed
                                            ↓
                                    Athena/Redshift Query
```

---

## Khi nào dùng Data Wrangler?

✅ **Khám phá dữ liệu** mới, chưa biết cấu trúc  
✅ **Prototype** các phép biến đổi trước khi code  
✅ **Visual** - kéo thả, không cần code  
✅ **Quick Model** - test feature importance  
✅ **Xuất code** để dùng trong training pipeline  

### Workflow điển hình
```
S3/Database → Data Wrangler (explore) → Export Notebook → SageMaker Training
```

---

## So sánh chi tiết

### Nguồn dữ liệu

| Nguồn | Glue | Data Wrangler |
|-------|------|---------------|
| S3 | ✅ | ✅ |
| Athena | ✅ | ✅ |
| Redshift | ✅ | ✅ |
| RDS/JDBC | ✅ | ✅ |
| Kinesis/Kafka | ✅ | ❌ |
| Feature Store | ❌ | ✅ |
| Databricks | ❌ | ✅ |

### Khả năng xử lý

| Tính năng | Glue | Data Wrangler |
|-----------|------|---------------|
| Biến đổi cơ bản | ✅ | ✅ 300+ |
| Join phức tạp | ✅ Spark SQL | ✅ |
| ML Feature Engineering | Giới hạn | ✅ Tối ưu |
| Visualization | ❌ | ✅ |
| Anomaly Detection | ❌ | ✅ |
| Quick Model | ❌ | ✅ |

### Chi phí

| Glue | Data Wrangler |
|------|---------------|
| Tính theo DPU-hours | Tính theo Studio instance |
| Chạy lúc nào tính lúc đó | Chạy khi mở Studio |
| Phù hợp batch lớn | Phù hợp interactive |

---

## Kết hợp cả hai

```
┌─────────────────────────────────────────────────────────────┐
│                   BEST PRACTICE WORKFLOW                     │
│                                                              │
│  1. Data Wrangler: Khám phá, prototype                      │
│        ↓                                                     │
│  2. Export code Python                                       │
│        ↓                                                     │
│  3. Đưa vào Glue Job cho production                         │
│        ↓                                                     │
│  4. Schedule chạy tự động                                    │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## Tóm tắt quyết định

```
Cần ETL production, chạy lâu dài?  → Glue
Cần khám phá dữ liệu, prototype?   → Data Wrangler
Cần cả hai?                        → Wrangler → Export → Glue
```

---

## Xem thêm
- [[AWS Glue & Data Catalog]]
- [[SageMaker Data Wrangler & Canvas]]
- [[Amazon SageMaker AI]]
