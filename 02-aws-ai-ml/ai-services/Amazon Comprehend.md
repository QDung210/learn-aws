# Amazon Comprehend

## 1️⃣ Giải quyết vấn đề gì? (WHY)

**Vấn đề kinh điển**: **NLP tự động** - trích xuất insights từ text mà không cần train model.

**Nếu không có Comprehend**:
- Tự build NLP models (spaCy, NLTK)
- Tốn thời gian training, tuning
- Khó maintain multi-language

**Comprehend sinh ra để**:
- Entity extraction, sentiment analysis
- PII detection & redaction
- Custom classification
- Serverless, pay-per-use

---

## 2️⃣ Thuộc nhóm nào trong kiến trúc AWS?

| Phân loại | Vị trí |
|-----------|--------|
| **Category** | AI/ML (NLP) |
| **Pipeline** | **Processing** layer |
| **Pattern** | Text analysis, pre-processing |

---

## 3️⃣ Input – Output (DATA FLOW)

| Input | Output |
|-------|--------|
| Text (raw, từ [[Amazon Transcribe]]) | Entities, sentiment, key phrases |
| Documents từ [[Amazon S3]] | PII locations, custom labels |
| Real-time API calls | JSON results với scores |

---

## 4️⃣ Cách hoạt động (HOW – Conceptual)

- **Sync/Async**: Real-time API hoặc batch jobs
- **Stateless**: Mỗi request độc lập
- **Pre-trained + Custom**: Built-in models + custom classifiers

---

## 5️⃣ Khi nào dùng – Khi nào KHÔNG dùng?

### ✅ Use Cases chuẩn
- Sentiment analysis (reviews, feedback)
- Entity extraction (names, places, orgs)
- PII detection/redaction
- Document classification
- Topic modeling

### ❌ Anti-patterns
| Sai | Đúng |
|-----|------|
| OCR từ images/PDF | → Textract |
| Speech-to-text | → [[Amazon Transcribe]] |
| Text generation | → Bedrock |
| Complex reasoning | → Bedrock LLMs |

Chi tiết: [[Transcribe vs Comprehend]], [[Comprehend vs Bedrock]]

---

## 6️⃣ So sánh với services khác

| Feature | Comprehend | Bedrock | Textract |
|---------|------------|---------|----------|
| **Purpose** | NLP analysis | Gen AI | OCR |
| **Input** | Text only | Text, images | Images, PDFs |
| **Output** | Structured (entities, sentiment) | Generated text | Extracted text |
| **Custom** | Custom classifier | Fine-tuning | ❌ |
| **Cost** | Per character | Per token | Per page |

---

## 7️⃣ Cost – Scale – Limitations

### Pricing
| Feature | Giá |
|---------|-----|
| **Entity/Sentiment** | $0.0001/unit (100 chars) |
| **Custom Classification** | $3/hour training + $0.0005/unit |
| **PII** | $0.0001/unit |
| **Medical** | $0.01/unit |

### Limits
| Limit | Value |
|-------|-------|
| Text size | 5KB per request (sync) |
| Batch | 25 docs per batch |
| Languages | 12+ |

---

## Comprehend Medical

Chuyên biệt cho y khoa:
- **Entities**: RxNorm (thuốc), ICD-10 (bệnh)
- **Relations**: Test ↔ condition, medication ↔ dosage
- **PHI detection**: HIPAA compliance

⚠️ Cần redaction + encryption khi lưu PHI

---

## Best Practices

### Text Preparation
- Chuẩn hóa (lowercase, bỏ ký tự thừa) nếu không cần giữ format
- Giới hạn 5KB → cắt đoạn dài
- Giữ offset để map lại nguồn

### Custom Classification
- Tập training cân bằng
- Theo dõi F1 score và drift
- Retrain khi accuracy giảm

### PII Handling
- Bật PII detection trước khi log/lưu
- Token-level redaction
- Kết hợp [[Amazon Bedrock Guardrails]]

---

## Liên kết

- Tiền xử lý cho RAG: [[Xử lý dữ liệu có cấu trúc & phi cấu trúc (GenAI)]]
- Kết hợp Transcribe: [[Amazon Transcribe]], [[Transcribe vs Comprehend]]
- Guardrails/Redaction: [[Amazon Bedrock Guardrails]]
