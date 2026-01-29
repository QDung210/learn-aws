# RAG Nâng Cao

## 1️⃣ Giải quyết vấn đề gì? (WHY)

**Vấn đề kinh điển**: LLM không biết dữ liệu riêng của bạn → **RAG = "thi mở" cho LLM**.

**RAG sinh ra để**:
- Cập nhật kiến thức nhanh (không cần retrain)
- Giảm hallucination với grounding
- Tận dụng semantic search
- Trích dẫn nguồn

---

## 2️⃣ Thuộc nhóm nào trong kiến trúc?

| Phân loại | Vị trí |
|-----------|--------|
| **Category** | AI/ML Pattern |
| **Pipeline** | **Retrieval + Generation** |
| **Components** | Vector Store + LLM |

---

## 3️⃣ DATA FLOW

```
Query → Embed → Vector Search → Top-K Chunks → Inject into Prompt → LLM → Response
                     ↓
              Vector Store (OpenSearch, Aurora, etc.)
```

---

## Chunking & Tiền xử lý

### Chiến lược Chunking
| Strategy | Mô tả | Use case |
|----------|-------|----------|
| **Fixed-size** | Cắt theo character/token count | Simple docs |
| **Hierarchical** | Theo heading (H1, H2...) | Structured docs |
| **Sliding window** | Overlap giữa chunks | Preserve context |
| **Semantic** | Cắt theo ý nghĩa | Complex content |

### Best Practices
- Giữ **overlap nhỏ** để tránh rời rạc
- Chèn **divider strings** (section markers) trước khi chunk
- Chuẩn hóa: bỏ HTML noise, giữ tables/lists
- Tokenizer phù hợp ngôn ngữ

---

## Embedding & Lưu trữ

### Embedding
- Chọn model đa ngôn ngữ nếu cần
- Normalize vector (L2) khi dùng cosine
- **Batch embedding** - không xử lý tuần tự

### Vector Stores

| Store | Best for |
|-------|----------|
| [[Amazon OpenSearch Service]] | Large scale, hybrid search |
| [[Amazon RDS  Aurora]] (pgvector) | ACID + vector, small-medium |
| [[Amazon DynamoDB]] | Key-value + vector (limited) |

---

## Truy vấn & Re-rank

### Hybrid Retrieval
```
Query → BM25 (keyword) + Vector (semantic) → Combine scores → Re-rank → Top-K
```

### Query Preprocessing
- **Style normalization**: Match query style với doc style
- **Query decomposition**: Chia queries phức tạp
- **Keyword extraction**: Cho hybrid search
- **Ambiguity resolution**: Xử lý tham chiếu mơ hồ

### Re-ranking
- Cross-encoder hoặc LLM re-ranker
- Custom hybrid scoring function
- Score cutoff filtering

---

## Context Optimization

**Phần lớn tokens đến từ context, không phải prompt!**

### Tối ưu
- Giới hạn chunks: Top 2-5 thay vì top 10
- Filter bằng metadata trước khi inject
- Loại bỏ chunks trùng lặp
- Cân bằng chunk size vs relevance

---

## Grounding & Kiểm soát

### Grounding
- Yêu cầu trích dẫn nguồn
- Giới hạn response dựa trên retrieved docs
- [[Amazon Bedrock Guardrails]] contextual grounding

### PII Protection
- Token-level redaction trước log/response
- [[Amazon Comprehend]] PII detection

### Factuality Check
- Self-check prompting
- LLM-as-judge với tiêu chí rõ

---

## Đánh giá RAG

### Metrics
| Metric | Đo gì |
|--------|-------|
| **Faithfulness** | Response có based on context? |
| **Answer relevance** | Trả lời đúng câu hỏi? |
| **Context relevance** | Retrieved chunks có liên quan? |
| **Harmfulness** | Có nội dung độc hại? |
| **Latency** | Thời gian response |

### Testing
- Synthetic QA pairs từ dữ liệu thật
- Red team testing
- Drift monitoring

---

## Liên kết

- Vector stores: [[Amazon OpenSearch Service]], [[Amazon RDS  Aurora]], [[Amazon DynamoDB]]
- Bedrock KB: [[RAG, Cơ sở tri thức, Vector Store]]
- Prompt design: [[Prompt Engineering Overview]]
- Guardrails: [[Amazon Bedrock Guardrails]]
- Monitoring: [[CloudWatch Metrics & quan sát]]
- RAG vs Fine-tune: [[RAG vs Finetune]]
