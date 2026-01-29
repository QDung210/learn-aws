# Amazon OpenSearch Service

## 1️⃣ Giải quyết vấn đề gì? (WHY)

**Vấn đề kinh điển**: **Full-text search** + **Vector search** + **Log analytics** trong một engine.

**Nếu không có OpenSearch**:
- Tự quản lý Elasticsearch clusters
- Riêng biệt hóa search engine vs log system
- Khó kết hợp semantic + keyword search

**OpenSearch sinh ra để**:
- Search trên documents, logs, metrics
- Vector search cho RAG/embedding
- Real-time dashboards (Kibana-style)

---

## 2️⃣ Thuộc nhóm nào trong kiến trúc AWS?

| Phân loại | Vị trí |
|-----------|--------|
| **Category** | Analytics / Search |
| **Pipeline** | **Storage + Serving** (indexed data) |
| **Pattern** | Search engine, Vector store |

---

## 3️⃣ Input – Output (DATA FLOW)

| Input | Output |
|-------|--------|
| [[Amazon Kinesis]] Firehose | Search API results |
| Lambda, Application logs | OpenSearch Dashboards |
| Bedrock KB (embedding) | RAG retrieval results |
| CloudWatch Logs | Aggregations, metrics |

---

## 4️⃣ Cách hoạt động (HOW – Conceptual)

- **Inverted index**: Full-text search, faceting, aggregations
- **k-NN plugin**: Vector search (HNSW/IVF)
- **Near real-time**: Index refresh ~1s
- **Cluster-based**: Nodes, shards, replicas

---

## 5️⃣ Khi nào dùng – Khi nào KHÔNG dùng?

### ✅ Use Cases chuẩn
- Full-text search trên documents
- Log analytics (ELK stack replacement)
- Vector store cho RAG
- Real-time dashboards

### ❌ Anti-patterns
| Sai | Đúng |
|-----|------|
| Primary database cho transactions | → DynamoDB, Aurora |
| Simple key-value lookups | → DynamoDB |
| Data warehouse analytics | → Redshift, Athena |
| Small-scale vector (< 1M) | → Aurora pgvector |

---

## 6️⃣ So sánh với services khác

| Feature | OpenSearch | Aurora pgvector | DynamoDB |
|---------|------------|-----------------|----------|
| **Full-text** | ✅ Best | ❌ Basic | ❌ |
| **Vector search** | ✅ Native k-NN | ✅ pgvector | ⚠️ Limited |
| **ACID** | ❌ | ✅ | ⚠️ Limited |
| **Scale** | Cluster | Replicas | Unlimited |
| **Best for** | Search + RAG | Hybrid SQL + vector | Key-value |

Chi tiết: [[OpenSearch vs Aurora pgvector]]

---

## 7️⃣ Cost – Scale – Limitations

### Pricing
| Component | Giá |
|-----------|-----|
| **Instance** | $0.024–$10/hour (varies by size) |
| **Storage** | $0.10/GB EBS, $0.023/GB UltraWarm |
| **Serverless** | OCU-hour pricing |

### Scaling
- **Provisioned**: Add nodes, resize
- **Serverless**: Auto-scale OCUs
- **UltraWarm/Cold**: Tiered storage

### Limits
| Limit | Value |
|-------|-------|
| Shards per node | ~25 per GB heap |
| Index size | Recommended < 50GB/shard |
| k-NN dimensions | Max 16,000 |

---

## Best Practices

### Vector Search
- Engines: k-NN plugin (HNSW/IVF)
- Schema: vector field + metadata
- **Hybrid search**: BM25 + vector với RRF reranking
- Dimension phải khớp embedding model

### Index Management
- ILM/ISM: hot → warm → cold → delete
- Rollover theo size/time
- Tránh oversharding

### Security
- Fine-grained access control (FGAC)
- Field/document-level security
- VPC-only deployment
- Audit logs

### Monitoring
- Cluster health (Green/Yellow/Red)
- CPU, heap, disk watermarks
- Shard allocation status

---

## Liên kết

- Vector store cho RAG: [[RAG Nâng Cao]], [[RAG, Cơ sở tri thức, Vector Store]]
- So sánh: [[OpenSearch vs Aurora pgvector]]
- Guardrails: [[Amazon Bedrock Guardrails]]
- Streaming data: [[Amazon Kinesis]]
