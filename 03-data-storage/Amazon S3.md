# Amazon S3

## 1️⃣ Giải quyết vấn đề gì? (WHY)

**Vấn đề kinh điển**: Lưu trữ **object/file với scale không giới hạn**, độ bền cao, chi phí thấp.

**Nếu không có S3**:
- Tự quản lý NAS/SAN, lo replication, backup
- Giới hạn capacity, khó scale
- Chi phí vận hành cao

**S3 sinh ra để**:
- Lưu trữ mọi loại file: logs, media, backups, data lake
- 99.999999999% (11 nines) durability
- Tích hợp sâu với hầu hết AWS services

---

## 2️⃣ Thuộc nhóm nào trong kiến trúc AWS?

| Phân loại | Vị trí |
|-----------|--------|
| **Category** | Storage |
| **Pipeline** | **Storage layer** (raw, processed, outputs) |
| **Pattern** | Data Lake foundation |

---

## 3️⃣ Input – Output (DATA FLOW)

| Input | Output |
|-------|--------|
| Upload từ SDK, CLI, Console | GET object qua SDK/CLI |
| [[AWS Glue & Data Catalog]], [[Amazon Kinesis]] | Athena queries, [[Amazon SageMaker AI]] training |
| [[Bedrock Data Automation (BDA)]] | [[RAG, Cơ sở tri thức, Vector Store]] |
| Application logs, backups | Analytics, ML pipelines |

---

## 4️⃣ Cách hoạt động (HOW – Conceptual)

- **Object storage**: Key-value (key = path, value = file)
- **Stateless**: Mỗi request độc lập
- **Eventually consistent** (read-after-write cho PUTs mới)
- **Storage Classes**: Standard → IA → Glacier (cost vs access time)

---

## 5️⃣ Khi nào dùng – Khi nào KHÔNG dùng?

### ✅ Use Cases chuẩn
- Data lake, raw/processed data
- Static website hosting
- Backup & archive
- ML training data, model artifacts

### ❌ Anti-patterns
| Sai | Đúng |
|-----|------|
| Query real-time trên S3 | → DynamoDB, Aurora |
| File system với POSIX | → EFS, FSx |
| Low-latency caching | → ElastiCache |

---

## 6️⃣ So sánh với services khác

| Feature | S3 | EBS | EFS |
|---------|-----|-----|-----|
| **Type** | Object | Block | File (NFS) |
| **Access** | HTTP/SDK | EC2 only | Multi-EC2 |
| **Scale** | Unlimited | Limited | Auto-scale |
| **Use case** | Data lake, static | EC2 root/data | Shared file system |

---

## 7️⃣ Cost – Scale – Limitations

### Pricing
| Component | Giá (us-east-1) |
|-----------|-----------------|
| **Standard** | $0.023/GB/month |
| **IA** | $0.0125/GB/month |
| **Glacier** | $0.004/GB/month |
| **Requests** | $0.005/1000 PUT, $0.0004/1000 GET |

### Limits
| Limit | Value |
|-------|-------|
| Object size | Max 5TB (multipart) |
| Bucket count | 100 per account (soft) |
| Prefix TPS | 3,500 PUT + 5,500 GET/prefix |

---

## Best Practices

### Storage Tiers & Lifecycle
- Standard → IA → Glacier theo lifecycle rules
- S3 Intelligent-Tiering nếu access pattern không rõ

### Security
- Block Public Access mặc định
- Bucket policy + IAM roles (không dùng access keys)
- SSE-S3, SSE-KMS, hoặc client-side encryption
- Access logs + CloudTrail

### Performance
- Prefix key design: `tenant/date/doc/` cho partition
- Multipart upload cho files lớn
- S3 Transfer Acceleration cho cross-region
- S3 Select để đọc một phần object

### Data Protection
- Versioning + Object Lock (WORM) cho compliance
- Cross-Region Replication (CRR) cho DR

---

## Liên kết

- Pipeline RAG/KB: [[RAG Nâng Cao]], [[RAG, Cơ sở tri thức, Vector Store]]
- ETL/Catalog: [[AWS Glue & Data Catalog]]
- Streaming vào S3: [[Amazon Kinesis]]
- Monitoring: [[CloudWatch Metrics & quan sát]]
