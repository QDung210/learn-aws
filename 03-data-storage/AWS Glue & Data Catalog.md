# AWS Glue & Data Catalog

## 1️⃣ Giải quyết vấn đề gì? (WHY)

**Vấn đề kinh điển**: **ETL serverless** + **Metadata catalog** cho data lake.

**Nếu không có Glue**:
- Tự quản lý Spark clusters (EMR)
- Tự build metadata store
- Lo scheduling, scaling

**Glue sinh ra để**:
- Serverless ETL với Apache Spark
- Auto-discover schema (Crawlers)
- Central metadata cho Athena, Redshift, EMR

---

## 2️⃣ Thuộc nhóm nào trong kiến trúc AWS?

| Phân loại | Vị trí |
|-----------|--------|
| **Category** | Analytics / ETL |
| **Pipeline** | **Processing** layer |
| **Pattern** | Batch ETL, Data Catalog |

---

## 3️⃣ Input – Output (DATA FLOW)

| Input | Output |
|-------|--------|
| [[Amazon S3]] (raw data) | [[Amazon S3]] (processed) |
| JDBC sources (RDS, Redshift) | Data Catalog tables |
| [[Amazon Kinesis]] streams | Athena-queryable data |

---

## 4️⃣ Cách hoạt động (HOW – Conceptual)

- **Batch**: ETL jobs chạy theo schedule hoặc trigger
- **Serverless**: Không quản lý clusters
- **Stateless jobs**: Mỗi job run độc lập
- **Crawlers**: Auto-discover schema → populate catalog

---

## 5️⃣ Khi nào dùng – Khi nào KHÔNG dùng?

### ✅ Use Cases chuẩn
- Data lake ETL (S3 → transform → S3)
- Schema discovery cho raw data
- Prepare data cho ML training
- Batch processing lớn

### ❌ Anti-patterns
| Sai | Đúng |
|-----|------|
| Real-time streaming ETL | → [[Amazon Kinesis]] + Lambda |
| Interactive data exploration | → [[SageMaker Data Wrangler & Canvas]] |
| Simple file conversions | → Lambda |
| Sub-second latency | → Lambda, Kinesis |

Chi tiết: [[Glue vs Data Wrangler]]

---

## 6️⃣ So sánh với Data Wrangler

| Feature | Glue | Data Wrangler |
|---------|------|---------------|
| **Purpose** | Production ETL | Data exploration |
| **Interface** | Code / Studio | Visual drag-drop |
| **Scale** | Unlimited (Spark) | Limited (exploration) |
| **Output** | Data files | Notebook code |
| **Use case** | Batch pipelines | Feature engineering |

---

## 7️⃣ Cost – Scale – Limitations

### Pricing
| Component | Giá |
|-----------|-----|
| **ETL Jobs** | $0.44/DPU-hour |
| **Crawlers** | $0.44/DPU-hour |
| **Data Catalog** | $1/100K objects/month |
| **Glue Studio** | Free (UI) |

### Scaling
- **DPU allocation**: 2–100 DPUs
- **Auto-scaling**: Glue 3.0+
- **Job bookmarks**: Incremental processing

### Limits
| Limit | Value |
|-------|-------|
| Max DPUs | 100 per job |
| Concurrent jobs | 50 (default) |
| Timeout | 48 hours max |

---

## Components

### Crawlers
- Scan data sources (S3, JDBC)
- Infer schema automatically
- Update Data Catalog

### Data Catalog
- Hive-compatible metastore
- Tables, databases, partitions
- Used by Athena, Redshift Spectrum, EMR

### ETL Jobs
- Python/Scala Spark
- Visual ETL (Glue Studio)
- Transforms: map, filter, join, aggregate

### Glue Studio
- Visual DAG builder
- Drag-drop transforms
- Monitoring dashboard

---

## Partition Strategy

### Time-based (phổ biến)
```
s3://bucket/data/year=2024/month=01/day=15/
```
→ Query theo thời gian nhanh

### Entity-based
```
s3://bucket/data/device=ABC/year=2024/month=01/
```
→ Query theo entity trước

### Best Practice
- Design prefix theo query patterns
- Crawler tự sinh partitions từ folder structure
- Đặt đúng ngay từ đầu (khó migrate)

---

## Liên kết

- So sánh: [[Glue vs Data Wrangler]]
- Data prep cho ML: [[SageMaker Data Wrangler & Canvas]]
- Streaming ETL: [[Amazon Kinesis]]
- Storage: [[Amazon S3]]
- Ingest cho RAG: [[Xử lý dữ liệu có cấu trúc & phi cấu trúc (GenAI)]]
