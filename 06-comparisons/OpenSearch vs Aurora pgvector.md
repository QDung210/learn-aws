# OpenSearch vs Aurora pgvector (Vector Search)

## 1️⃣ Câu hỏi cốt lõi

> **Khi nào chọn cái nào cho RAG/Vector Search?**
> - **[[Amazon OpenSearch Service|OpenSearch]]**: Full-text search + vectors, log analytics, large-scale RAG
> - **[[Amazon RDS  Aurora|Aurora pgvector]]**: Đã dùng PostgreSQL, cần ACID + vectors, complex JOINs

---

## Tổng quan nhanh

| Tiêu chí | OpenSearch | Aurora pgvector |
|----------|------------|-----------------|
| **Loại** | Search engine (Elasticsearch fork) | Relational DB + extension |
| **Primary use** | Full-text search, logs, analytics | Transactional data + vectors |
| **Vector maturity** | Native k-NN plugin | Extension (mature) |
| **Hybrid search** | ✅ BM25 + k-NN | ✅ GIN + HNSW |
| **Scaling** | Horizontal (shards) | Vertical + Read Replicas |
| **Ecosystem** | ELK, Dashboards | PostgreSQL ecosystem |

---

## Khi nào dùng OpenSearch?

✅ **Full-text search** là primary use case  
✅ **Log analytics** - ELK/OpenSearch stack  
✅ **Hybrid search** cần BM25 + vector  
✅ **Large-scale RAG** - billions of vectors  
✅ **Real-time indexing** - high ingest rate  
✅ **Dashboards** - visualization built-in  

### Đặc điểm
- Native vector search (k-NN plugin)
- HNSW và IVF algorithms
- Horizontal scaling with shards
- Near real-time search

---

## Khi nào dùng Aurora pgvector?

✅ **Đã dùng PostgreSQL** - add vectors to existing  
✅ **Transactions + vectors** - ACID với embeddings  
✅ **Complex JOINs** - vectors với relational data  
✅ **Small-medium scale** - millions of vectors  
✅ **PostgreSQL ecosystem** - tools, extensions  
✅ **Cost-sensitive** - simpler pricing  

### Đặc điểm
- PostgreSQL extension
- HNSW và IVFFlat indexes
- Full SQL capabilities
- Familiar for SQL developers

---

## So sánh chi tiết

### Vector Capabilities

| Feature | OpenSearch | Aurora pgvector |
|---------|------------|-----------------|
| Max dimensions | 16,000 | 2,000 |
| Index types | HNSW, IVF, Faiss | HNSW, IVFFlat |
| Distance metrics | L2, cosine, inner product | L2, cosine, inner product |
| Approximate NN | ✅ | ✅ |
| Exact NN | ✅ | ✅ |
| Filtering | Pre/Post filter | WHERE clause |

### Hybrid Search

**OpenSearch:**
```json
{
  "query": {
    "hybrid": {
      "queries": [
        { "match": { "content": "machine learning" } },
        { "knn": { "embedding": { "vector": [...], "k": 10 } } }
      ]
    }
  }
}
```

**Aurora pgvector:**
```sql
SELECT *, 
  ts_rank(to_tsvector(content), query) as text_score,
  1 - (embedding <=> query_embedding) as vector_score
FROM documents, to_tsquery('machine & learning') query
WHERE to_tsvector(content) @@ query
ORDER BY (text_score * 0.3 + vector_score * 0.7) DESC
LIMIT 10;
```

### Scaling

| Aspect | OpenSearch | Aurora pgvector |
|--------|------------|-----------------|
| Write scale | Horizontal (shards) | Single writer |
| Read scale | Replicas per shard | 15 Read Replicas |
| Max size | Petabytes | 128 TB |
| Rebalancing | Automatic | N/A |

### Operations

| Aspect | OpenSearch | Aurora pgvector |
|--------|------------|-----------------|
| Index maintenance | Refresh, merge | VACUUM, REINDEX |
| Monitoring | Dashboards built-in | CloudWatch, pg stats |
| Backup | Snapshots | Automated backups, PITR |
| Learning curve | OpenSearch DSL | SQL (familiar) |

---

## Performance Characteristics

### OpenSearch
```
✅ Excellent for:
  - High QPS search
  - Near real-time indexing
  - Complex aggregations
  - Billions of vectors

⚠️ Watch for:
  - Memory usage (heap)
  - Shard management
  - Index refresh overhead
```

### Aurora pgvector
```
✅ Excellent for:
  - Transactional workloads
  - JOINs with vectors
  - Moderate vector counts
  - SQL familiarity

⚠️ Watch for:
  - Memory for large indexes
  - VACUUM for bloat
  - Dimension limit (2000)
```

---

## Cost Comparison

| Factor | OpenSearch | Aurora |
|--------|------------|--------|
| Instance types | Specialized | General purpose |
| Storage | EBS, UltraWarm | Aurora storage |
| Data transfer | May be higher | Lower (single region) |
| Serverless | ✅ Available | ✅ Serverless v2 |
| Reserved | ✅ | ✅ |

### Rough comparison (same data size)
```
OpenSearch: Usually higher due to:
  - More instances for HA
  - EBS storage costs
  - Data transfer between nodes

Aurora: Usually lower for:
  - Simpler architecture
  - Aurora storage efficiency
  - Less operational overhead
```

---

## Architecture Patterns

### OpenSearch-centric RAG
```
┌─────────────────────────────────────────────────────────────┐
│  Documents → Embeddings → OpenSearch                        │
│                              │                               │
│  Query → Hybrid Search ──────┘                              │
│              │                                               │
│              ▼                                               │
│  Top-k Results → LLM → Response                             │
│                                                              │
│  Best for: Large scale, full-text + semantic                │
└─────────────────────────────────────────────────────────────┘
```

### Aurora pgvector RAG
```
┌─────────────────────────────────────────────────────────────┐
│  Documents → Embeddings → Aurora                            │
│                              │                               │
│  Query → Vector Search ──────┤                              │
│              │               │                               │
│              ▼               ▼                               │
│  Vectors + Metadata (JOIN) → LLM → Response                 │
│                                                              │
│  Best for: Existing Postgres, transactional, JOINs needed   │
└─────────────────────────────────────────────────────────────┘
```

---

## Decision Matrix

| Scenario | Choice |
|----------|--------|
| Greenfield RAG, large scale | OpenSearch |
| Existing PostgreSQL app | Aurora pgvector |
| Full-text + vector hybrid | OpenSearch (better) |
| Need JOINs with business data | Aurora pgvector |
| Log analytics + embeddings | OpenSearch |
| ACID transactions needed | Aurora pgvector |
| Team knows SQL, not DSL | Aurora pgvector |
| Billions of vectors | OpenSearch |
| Millions of vectors | Either works |

---

## Xem thêm
- [[Amazon OpenSearch Service]]
- [[Amazon RDS  Aurora]]
- [[RAG Nâng Cao]]
- [[RAG, Cơ sở tri thức, Vector Store]]
