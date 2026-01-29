# Amazon RDS / Aurora

## 1️⃣ Giải quyết vấn đề gì? (WHY)

**Vấn đề kinh điển**: **Relational database managed** với ACID, SQL, complex joins.

**Nếu không có RDS/Aurora**:
- Tự cài đặt PostgreSQL/MySQL trên EC2
- Lo patching, backup, replication, failover
- Scaling phức tạp

**RDS/Aurora sinh ra để**:
- OLTP workloads với transactions
- Applications cần schema, foreign keys, joins
- Aurora: 5x faster than MySQL, 3x faster than PostgreSQL

---

## 2️⃣ Thuộc nhóm nào trong kiến trúc AWS?

| Phân loại | Vị trí |
|-----------|--------|
| **Category** | Database (Relational) |
| **Pipeline** | **Storage + Serving** layer |
| **Pattern** | OLTP, ACID transactions |

---

## 3️⃣ Input – Output (DATA FLOW)

| Input | Output |
|-------|--------|
| Application writes (SQL) | Query results |
| [[AWS Glue & Data Catalog]] ETL | Application reads |
| DMS migration | Analytics export to S3 |

---

## 4️⃣ Cách hoạt động (HOW – Conceptual)

- **Relational**: Tables, rows, SQL queries
- **Stateful**: Data persisted, replicated
- **Synchronous writes**: Primary → replicas
- **Aurora**: Storage tách compute, auto-repair

---

## 5️⃣ Khi nào dùng – Khi nào KHÔNG dùng?

### ✅ Use Cases chuẩn
- Transactional applications (e-commerce, banking)
- Legacy apps cần MySQL/PostgreSQL
- Complex queries với joins
- **pgvector**: Vector store tích hợp (Aurora PostgreSQL)

### ❌ Anti-patterns
| Sai | Đúng |
|-----|------|
| Key-value với ultra-low latency | → DynamoDB |
| Full-text search | → [[Amazon OpenSearch Service]] |
| Petabyte analytics | → Redshift |
| Schemaless, flexible documents | → DocumentDB, DynamoDB |

---

## 6️⃣ So sánh RDS vs Aurora

| Feature | RDS | Aurora |
|---------|-----|--------|
| **Storage** | EBS attached | Distributed, auto-grow |
| **Replication** | Async replicas | 6-way replication |
| **Failover** | ~60-120s | ~30s |
| **Performance** | Standard | 5x MySQL, 3x PostgreSQL |
| **Cost** | Lower baseline | Higher but more efficient |
| **Serverless** | ❌ | ✅ Serverless v2 |

Chi tiết: [[DynamoDB vs Aurora]]

---

## 7️⃣ Cost – Scale – Limitations

### Pricing
| Component | Giá |
|-----------|-----|
| **RDS** | $0.017–$10/hour (varies) |
| **Aurora** | $0.029/hour + I/O |
| **Aurora Serverless** | $0.12/ACU-hour |
| **Storage** | $0.10–0.20/GB/month |

### Scaling
- **Vertical**: Change instance size (downtime)
- **Read replicas**: Up to 15 (Aurora)
- **Aurora Serverless v2**: Auto-scale ACUs

### Limits
| Limit | Value |
|-------|-------|
| Storage | 64TB (Aurora), 16TB (RDS MySQL) |
| Read replicas | 15 (Aurora), 5 (RDS) |
| Connections | Instance-dependent |

---

## pgvector cho RAG

### Setup
```sql
CREATE EXTENSION IF NOT EXISTS vector;

CREATE TABLE embeddings (
  id SERIAL PRIMARY KEY,
  content TEXT,
  embedding vector(1536),
  metadata JSONB
);

CREATE INDEX ON embeddings 
USING hnsw (embedding vector_cosine_ops);
```

### Best Practices
- **Dimension**: Khớp với embedding model (1536 cho ada-002)
- **Index**: HNSW cho speed, IVFFlat cho memory
- **Connection pooling**: RDS Proxy / pgbouncer
- **Hybrid search**: GIN/B-Tree cho metadata + HNSW cho vector

---

## Liên kết

- Vector search: [[RAG Nâng Cao]], [[OpenSearch vs Aurora pgvector]]
- So sánh NoSQL: [[Amazon DynamoDB]], [[DynamoDB vs Aurora]]
- ETL: [[AWS Glue & Data Catalog]]
