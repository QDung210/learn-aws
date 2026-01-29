# RAG, Cơ sở tri thức, Vector Store

## 1️⃣ Giải quyết vấn đề gì? (WHY)

**Vấn đề kinh điển**: LLM cần **kiến thức cập nhật** mà không có trong training data.

**Bedrock Knowledge Base sinh ra để**:
- **Managed RAG pipeline**: Ingest → Chunk → Embed → Store → Retrieve
- Không cần tự build infrastructure
- Tích hợp sẵn với Agents

---

## 2️⃣ Thuộc nhóm nào?

| Phân loại | Vị trí |
|-----------|--------|
| **Category** | AI/ML (RAG) |
| **Pipeline** | **Retrieval layer** |
| **Pattern** | Knowledge augmentation |

---

## 3️⃣ DATA FLOW

```
Documents (S3) → Ingest → Chunking → Embedding → Vector Store
                                                      ↓
User Query → Embed Query → Semantic Search → Retrieved Chunks → LLM → Response
```

---

## RAG Concept

### Ưu điểm RAG
- Cập nhật kiến thức nhanh (không retrain)
- Giảm hallucination với grounding
- Trích dẫn nguồn

### Nhược điểm
- Prompt dài tốn token
- Phụ thuộc chất lượng retrieval
- Cần vận hành vector store

---

## Bedrock Knowledge Base

### Managed Pipeline
1. **Ingest**: Documents từ S3
2. **Chunk**: Auto hoặc custom strategy
3. **Embed**: Titan embedding (hoặc khác)
4. **Store**: OpenSearch Serverless, Aurora, etc.
5. **Retrieve**: Semantic search

### Lambda Pre-processor
- Chuyển HTML → divider strings
- Chuẩn hóa trước khi chunk
- Custom logic

### Tích hợp Agents
- Agent tự động query KB khi cần
- **Agentic RAG** = Agent + Knowledge Base
- Xem: [[LLM Agents & Agentic AI]]

---

## Chunking Tips

### Strategies
| Strategy | Use case |
|----------|----------|
| **Fixed-size** | Simple, uniform docs |
| **Hierarchical** | Structured (H1/H2) |
| **Semantic** | Complex content |

### Best Practices
- Dùng cấu trúc tài liệu (heading) làm divider
- Tránh cắt ngang ý
- Kiểm soát k và score cutoff
- Cân bằng recall vs token cost

---

## RAG vs Fine-tuning

| Aspect | RAG | Fine-tuning |
|--------|-----|-------------|
| **Knowledge update** | Real-time | Requires retrain |
| **Cost** | Token + vector store | Training cost |
| **Best for** | Dynamic data, large corpus | Style, format, domain |
| **Combine?** | ✅ Fine-tune style + RAG data |

Chi tiết: [[RAG vs Finetune]]

---

## Liên kết
- Pre-processing: [[Xử lý dữ liệu có cấu trúc & phi cấu trúc (GenAI)]]
- Data extraction: [[Bedrock Data Automation (BDA)]]
- Agents: [[Runtime API, Agent, Knowledge Base]]
- Fine-tuning: [[Tinh chỉnh & LoRA trên Bedrock]]
