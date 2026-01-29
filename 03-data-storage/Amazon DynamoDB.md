# Amazon DynamoDB

## 1️⃣ Giải quyết vấn đề gì? (WHY)

**Vấn đề kinh điển**: Database **NoSQL key-value** với latency < 10ms ở mọi scale.

**Nếu không có DynamoDB**:
- Tự quản lý Cassandra, MongoDB clusters
- Lo sharding, replication, failover
- Khó đạt single-digit millisecond latency

**DynamoDB sinh ra để**:
- Session store, user profiles, shopping carts
- Gaming leaderboards, IoT telemetry
- Feature store cho ML
- Serverless, fully managed

---

## 2️⃣ Thuộc nhóm nào trong kiến trúc AWS?

| Phân loại | Vị trí |
|-----------|--------|
| **Category** | Database (NoSQL) |
| **Pipeline** | **Storage / Serving** layer |
| **Pattern** | Key-value, Document store |

---

## 3️⃣ Input – Output (DATA FLOW)

| Input | Output |
|-------|--------|
| SDK (PutItem, BatchWrite) | GetItem, Query, Scan |
| [[Amazon Kinesis]] → Lambda | DynamoDB Streams → Lambda |
| API Gateway + Lambda | Export to S3 |

---

## 4️⃣ Cách hoạt động (HOW – Conceptual)

- **Key-value + Document**: Partition key + optional Sort key
- **Stateful**: Data persisted, replicated 3 AZs
- **Eventually/Strongly consistent** reads (chọn được)
- **Auto-scaling** hoặc On-demand capacity

---

## 5️⃣ Khi nào dùng – Khi nào KHÔNG dùng?

### ✅ Use Cases chuẩn
- High-traffic web/mobile apps
- Session management, user preferences
- IoT data, gaming scores
- Feature store cho ML

### ❌ Anti-patterns
| Sai | Đúng |
|-----|------|
| Complex joins, ACID transactions | → Aurora, RDS |
| Full-text search | → [[Amazon OpenSearch Service]] |
| Analytics/OLAP | → Redshift, Athena |
| Relational data with foreign keys | → RDS/Aurora |

---

## 6️⃣ So sánh với services khác

| Feature | DynamoDB | Aurora | OpenSearch |
|---------|----------|--------|------------|
| **Type** | NoSQL key-value | Relational | Search engine |
| **Query** | PK/SK, GSI | SQL joins | Full-text, vector |
| **Latency** | < 10ms | ~ms | ~10-100ms |
| **Scale** | Unlimited | Limited replicas | Cluster-based |
| **Use case** | Key lookup | ACID, complex queries | Search, analytics |

Chi tiết: [[DynamoDB vs Aurora]]

---

## 7️⃣ Cost – Scale – Limitations

### Pricing Models
| Mode | Pricing |
|------|---------|
| **On-demand** | $1.25/1M writes, $0.25/1M reads |
| **Provisioned** | $0.00065/WCU-hour, $0.00013/RCU-hour |
| **Storage** | $0.25/GB/month |

### Scaling
- **On-demand**: Tự động, không cần config
- **Provisioned**: Auto Scaling hoặc manual WCU/RCU

### Limits
| Limit | Value |
|-------|-------|
| Item size | Max 400KB |
| Partition throughput | 3,000 RCU + 1,000 WCU per partition |
| GSI count | 20 per table |
| LSI count | 5 per table |

---

## Best Practices

### Table Design
- Chọn **partition key phân tán** (user_id, device_id)
- Tránh hot partitions
- Dùng sort key cho range queries
- GSI/LSI cho access patterns bổ sung

### Performance
- **DAX** cache cho đọc nhanh (microseconds)
- BatchWrite/BatchGet giảm request units
- TTL cho data tạm (tự động xóa)

### Streaming
- **DynamoDB Streams** → Lambda cho event-driven
- Hoặc export to Kinesis Firehose

---

## Liên kết

- So sánh với Aurora: [[DynamoDB vs Aurora]]
- Vector search options: [[RAG Nâng Cao]], [[Amazon OpenSearch Service]]
- Feature store: [[Amazon SageMaker AI]]
- Streaming: [[Amazon Kinesis]]
