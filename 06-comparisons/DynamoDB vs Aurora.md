# DynamoDB vs Aurora (RDS)

## 1️⃣ Câu hỏi cốt lõi

> **Khi nào chọn cái nào?**
> - **DynamoDB**: Key-value lookups, serverless, ultra-low latency
> - **Aurora**: Complex queries, JOINs, ACID transactions

---

## Tổng quan nhanh

| Tiêu chí | DynamoDB | Aurora/RDS |
|----------|----------|------------|
| **Loại** | NoSQL (Key-Value/Document) | SQL (Relational) |
| **Schema** | Flexible, schemaless | Rigid, defined schema |
| **Scale** | Tự động, vô hạn | Vertical + Read Replicas |
| **Latency** | Single-digit ms | Vài ms đến tens of ms |
| **Transactions** | Limited (25 items) | Full ACID |
| **Vector Search** | Hỗ trợ mới | pgvector (mature) |

---

## Khi nào dùng DynamoDB?

✅ **Key-value lookups** - truy vấn theo ID  
✅ **Session store, cache** - dữ liệu tạm  
✅ **Metadata, config** - schema linh hoạt  
✅ **Feature store** - ML features  
✅ **Tải cao, latency thấp** - gaming, IoT  
✅ **Serverless** - không quản lý server  

### Access patterns
```
✅ GetItem(pk)           - Lấy 1 item
✅ Query(pk, sk_range)   - Lấy nhiều items cùng pk
✅ Scan (tránh)          - Quét toàn bộ (tốn kém)
```

---

## Khi nào dùng Aurora/RDS?

✅ **Quan hệ phức tạp** - JOINs, foreign keys  
✅ **Transactions** - ACID đầy đủ  
✅ **Ad-hoc queries** - SQL linh hoạt  
✅ **Reporting, analytics** - aggregations  
✅ **Vector search** - pgvector mature  
✅ **Legacy migration** - từ on-prem  

### Access patterns
```
✅ SELECT với JOINs phức tạp
✅ Aggregations (SUM, AVG, GROUP BY)
✅ Full-text search
✅ Vector similarity (pgvector)
```

---

## So sánh chi tiết

### Scaling

| Tiêu chí | DynamoDB | Aurora |
|----------|----------|--------|
| Write Scale | Tự động (partitions) | Vertical only |
| Read Scale | Tự động | Read Replicas (15) |
| Storage | Tự động, vô hạn | Tự động lên 128TB |
| Global | Global Tables | Aurora Global DB |

### Pricing Model

| DynamoDB | Aurora |
|----------|--------|
| Pay-per-request (on-demand) | Pay-per-instance |
| Provisioned (RCU/WCU) | Storage + I/O |
| Khó dự đoán nếu tải biến động | Dễ dự đoán hơn |

### Vector Search cho AI/ML

| Tiêu chí | DynamoDB | Aurora (pgvector) |
|----------|----------|-------------------|
| Maturity | Mới | Mature |
| Index Types | - | HNSW, IVFFlat |
| Hybrid Search | - | ✅ GIN + Vector |
| Dimension Limit | - | 2000 |
| Integration | - | PostgreSQL ecosystem |

---

## Design Patterns

### DynamoDB - Single Table Design
```
PK              SK              Attributes
────────────────────────────────────────────
USER#123        PROFILE         {name, email}
USER#123        ORDER#001       {total, date}
USER#123        ORDER#002       {total, date}
PRODUCT#456     METADATA        {name, price}
```

### Aurora - Normalized Design
```sql
users (id, name, email)
orders (id, user_id, total, date)
products (id, name, price)
order_items (order_id, product_id, qty)
```

---

## Hybrid Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    COMMON PATTERN                            │
│                                                              │
│  DynamoDB                          Aurora                    │
│  ─────────                         ──────                    │
│  • Sessions                        • User profiles           │
│  • Real-time counters              • Orders (transactions)   │
│  • Feature flags                   • Reporting data          │
│  • Cache layer                     • Vector embeddings       │
│                                                              │
│            ↓                              ↓                  │
│  ┌─────────────────┐          ┌─────────────────┐           │
│  │  Fast Lookups   │          │  Complex Queries │           │
│  │  < 10ms         │          │  JOINs, Reports  │           │
│  └─────────────────┘          └─────────────────┘           │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## Decision Tree

```
Cần JOINs phức tạp?           → Aurora
Cần ACID transactions?        → Aurora
Cần vector search mature?     → Aurora (pgvector)

Cần key-value lookups?        → DynamoDB
Cần auto-scale serverless?    → DynamoDB
Cần single-digit ms latency?  → DynamoDB
Cần global replication easy?  → DynamoDB Global Tables

Cần cả hai đặc điểm?          → Hybrid Architecture
```

---

## Xem thêm
- [[Amazon DynamoDB]]
- [[Amazon RDS  Aurora]]
- [[RAG Nâng Cao]] - Vector store options
