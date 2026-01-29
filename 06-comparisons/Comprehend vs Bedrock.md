# Amazon Comprehend vs Amazon Bedrock

## 1️⃣ Câu hỏi cốt lõi

> **Khi nào chọn cái nào cho NLP?**
> - **[[Amazon Comprehend]]**: NER, sentiment, PII - structured output, rẻ, nhanh
> - **[[Tổng quan Amazon Bedrock|Bedrock]]**: Text generation, Q&A, summarization - creative, linh hoạt

---

## Tổng quan nhanh

| Tiêu chí | Comprehend | Bedrock |
|----------|------------|---------|
| **Loại** | NLP truyền thống (ML) | GenAI (Foundation Models) |
| **Approach** | Task-specific models | General-purpose LLMs |
| **Training** | Custom classifier có thể | Fine-tuning, LoRA |
| **Output** | Structured (entities, scores) | Free-form text |
| **Chi phí** | Thấp hơn | Cao hơn (token-based) |
| **Latency** | Nhanh hơn | Chậm hơn |

---

## Khi nào dùng Comprehend?

✅ **NER (Named Entity Recognition)** - trích xuất entities  
✅ **Sentiment Analysis** - positive/negative/neutral  
✅ **PII Detection** - phát hiện thông tin nhạy cảm  
✅ **Key Phrase Extraction** - từ khóa quan trọng  
✅ **Language Detection** - nhận dạng ngôn ngữ  
✅ **Topic Modeling** - phân loại chủ đề  
✅ **Medical NER** - ICD-10, RxNorm  

### Đặc điểm
- Output **có cấu trúc** (JSON với scores)
- **Chi phí thấp** cho high-volume
- **Latency thấp** - batch và real-time
- **Không cần prompt engineering**

---

## Khi nào dùng Bedrock?

✅ **Text Generation** - tạo nội dung mới  
✅ **Summarization** - tóm tắt tài liệu  
✅ **Q&A** - trả lời câu hỏi  
✅ **Complex Reasoning** - suy luận phức tạp  
✅ **Code Generation** - viết code  
✅ **Conversational AI** - chatbot thông minh  
✅ **Multi-modal** - image, video  

### Đặc điểm
- Output **tự nhiên**, giống người
- **Linh hoạt** - một model nhiều tasks
- Cần **prompt engineering**
- **RAG integration** cho knowledge

---

## So sánh Use Cases

### Entity Extraction

**Comprehend:**
```
Input: "John works at Amazon in Seattle"
Output: {
  "Entities": [
    {"Text": "John", "Type": "PERSON", "Score": 0.99},
    {"Text": "Amazon", "Type": "ORGANIZATION", "Score": 0.98},
    {"Text": "Seattle", "Type": "LOCATION", "Score": 0.97}
  ]
}
```

**Bedrock:**
```
Input: "Extract entities from: John works at Amazon in Seattle"
Output: "The text mentions John (a person), Amazon (a company), 
        and Seattle (a city in Washington state)..."
```

👉 **Comprehend thắng**: Structured output, rẻ hơn, nhanh hơn

---

### Content Summarization

**Comprehend:** ❌ Không hỗ trợ

**Bedrock:**
```
Input: [Long document] + "Summarize this in 3 bullet points"
Output: "• Key point 1...
        • Key point 2...
        • Key point 3..."
```

👉 **Bedrock thắng**: Generative capability

---

### Sentiment Analysis

**Comprehend:**
```
Output: {"Sentiment": "POSITIVE", "Score": 0.95}
```

**Bedrock:**
```
Output: "The text expresses a positive sentiment, with the author 
        showing enthusiasm about the product features..."
```

👉 **Tùy use case**: 
- Cần score → Comprehend
- Cần giải thích → Bedrock

---

## Cost Comparison

| Scenario | Comprehend | Bedrock |
|----------|------------|---------|
| 1M sentiment analyses | ~$100 | ~$1000+ |
| 1M entity extractions | ~$100 | ~$1000+ |
| 1K summaries | N/A | ~$50 |
| 1K Q&A responses | N/A | ~$50 |

---

## Hybrid Pattern

```
┌─────────────────────────────────────────────────────────────┐
│                   BEST PRACTICE: COMBINE                     │
│                                                              │
│  Input Document                                              │
│        │                                                     │
│        ▼                                                     │
│  ┌─────────────┐     ┌─────────────┐                        │
│  │ Comprehend  │     │   Bedrock   │                        │
│  │ ─────────── │     │ ─────────── │                        │
│  │ • PII detect│     │ • Summarize │                        │
│  │ • Entities  │     │ • Q&A       │                        │
│  │ • Sentiment │     │ • Reasoning │                        │
│  └──────┬──────┘     └──────┬──────┘                        │
│         │                   │                                │
│         ▼                   ▼                                │
│  Structured Data      Natural Response                       │
│  (for filtering)      (for user)                             │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Example: Document Processing Pipeline
1. **Comprehend**: Detect PII → redact
2. **Comprehend**: Extract entities → metadata
3. **Bedrock**: Summarize clean document
4. **Bedrock**: Answer questions with RAG

---

## Decision Matrix

| Need | Choice |
|------|--------|
| Cheap, high-volume NLP | Comprehend |
| Structured output (scores, types) | Comprehend |
| PII detection & redaction | Comprehend |
| Medical NER (ICD-10, RxNorm) | Comprehend Medical |
| Text generation | Bedrock |
| Summarization | Bedrock |
| Complex reasoning | Bedrock |
| Conversational AI | Bedrock |
| Multi-modal (image/video) | Bedrock |
| Both structured + generative | Combine both |

---

## Xem thêm
- [[Amazon Comprehend]]
- [[Tổng quan Amazon Bedrock]]
- [[Amazon Bedrock Guardrails]]
