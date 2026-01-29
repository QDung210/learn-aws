# Amazon Bedrock Guardrails

## 1️⃣ Giải quyết vấn đề gì? (WHY)

**Vấn đề kinh điển**: **Kiểm soát input/output của LLM** - chặn nội dung độc hại, prompt injection, PII leakage.

**Nếu không có Guardrails**:
- Tự build content filters
- Regex/rules phức tạp, dễ bypass
- Khó maintain across models

**Guardrails sinh ra để**:
- Block harmful content (bạo lực, hate speech...)
- PII detection & redaction
- Topic control (deny/allow)
- Contextual grounding cho RAG

---

## 2️⃣ Thuộc nhóm nào trong kiến trúc AWS?

| Phân loại | Vị trí |
|-----------|--------|
| **Category** | AI/ML (Safety) |
| **Pipeline** | **Pre-processing + Post-processing** |
| **Pattern** | Input/Output filtering |

---

## 3️⃣ Input – Output (DATA FLOW)

```
User Input → [Guardrail Check] → LLM → [Guardrail Check] → Response
                  ↓                           ↓
              Block/Redact              Block/Redact
```

| Input | Output |
|-------|--------|
| User prompts | Filtered prompts |
| Model responses | Safe responses / blocked |
| RAG context | Grounded responses |

---

## 4️⃣ Cách hoạt động (HOW – Conceptual)

- **Sync**: Check trước và sau mỗi inference
- **Policy-based**: Định nghĩa rules, blocklists, regex
- **ML-powered**: Content classification, PII detection
- **Stateless** per request (nhưng có session policy)

---

## 5️⃣ Khi nào dùng – Khi nào KHÔNG dùng?

### ✅ Use Cases chuẩn
- Customer-facing chatbots
- Regulated industries (healthcare, finance)
- PII protection
- Brand safety
- Prompt injection defense

### ❌ Anti-patterns
| Sai | Đúng |
|-----|------|
| Logic business phức tạp | → Lambda, Step Functions |
| Rate limiting | → API Gateway throttling |
| Authentication | → IAM, Cognito |

---

## 6️⃣ Capabilities

### Content Filters
| Category | Description |
|----------|-------------|
| **Violence** | Bạo lực, gây hại |
| **Hate** | Thù ghét, phân biệt |
| **Sexual** | Nội dung tình dục |
| **Insults** | Xúc phạm |
| **Misconduct** | Hành vi sai trái |
| **Prompt Attack** | Jailbreak, injection |

### Denied Topics
- Định nghĩa topics không được thảo luận
- VD: "Không trả lời về đối thủ cạnh tranh"

### Word Filters
- Blocklist: Từ cấm
- Regex patterns: Patterns phức tạp
- URL allow/deny list

### PII Handling
- Detect: SSN, credit cards, emails, phones...
- Actions: Block hoặc Redact (mask)
- Token-level redaction

### Contextual Grounding
- Đảm bảo response dựa trên context (RAG)
- Giảm hallucination
- Source attribution

---

## 7️⃣ Cost – Scale – Limitations

### Pricing
| Type | Giá |
|------|-----|
| **Text** | $0.75/1000 text units (content filters) |
| **PII** | $0.10/1000 text units |
| **Grounding** | $0.10/1000 text units |

### Limits
| Limit | Value |
|-------|-------|
| Policies per account | 100 |
| Topics per policy | 30 |
| Blocklist words | 10,000 |

---

## Best Practices

### Policy Design
- Tách guardrail theo use case (customer support vs internal)
- Start restrictive, loosen based on false positives
- Version control policies

### Testing
- Bộ "red prompts" để test effectiveness
- Measure block rate vs false positive rate
- Automated regression testing

### RAG Integration
- Pre-check: Filter query trước retrieval
- Post-check: Validate response + sources
- Grounding check: Response phải based on retrieved docs

### Logging
- Minimal logging cho sensitive content
- Encrypt logs
- Audit trail cho blocks

---

## Liên kết

- Áp dụng cho RAG: [[RAG, Cơ sở tri thức, Vector Store]], [[RAG Nâng Cao]]
- Vector store: [[Amazon OpenSearch Service]]
- Tổng quan Bedrock: [[Tổng quan Amazon Bedrock]]
- Monitoring: [[CloudWatch Metrics & quan sát]]
