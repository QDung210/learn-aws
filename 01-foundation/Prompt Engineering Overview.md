# Prompt Engineering Overview

## 1️⃣ Giải quyết vấn đề gì? (WHY)

**Vấn đề kinh điển**: LLM output phụ thuộc **cách hỏi** - prompt tốt = output tốt.

**Prompt Engineering sinh ra để**:
- Kiểm soát format, style, accuracy của output
- Giảm hallucination
- Guide model behavior
- Tối ưu token usage

---

## 2️⃣ Thuộc nhóm nào?

| Phân loại | Vị trí |
|-----------|--------|
| **Category** | AI/ML Technique |
| **Pipeline** | **Input layer** |
| **Pattern** | Pre-processing, Instructions |

---

## Nguyên tắc cốt lõi

### Clear & Specific
- Rõ ràng, ràng buộc bối cảnh
- Cung cấp **vai trò / mục tiêu / định dạng**
- Đặt thông tin quan trọng lên đầu

### Examples (Few-shot)
- Dùng ví dụ khi cần style/format cụ thể
- Tránh overfit với quá nhiều examples

### Structured I/O
- Yêu cầu JSON/XML để output nhất quán
- Dễ parse programmatically

### Length Control
- `max_tokens` để giới hạn
- Stop sequences để dừng sớm
- Hướng dẫn bullet/table format

---

## Kỹ thuật nâng cao

### Chain-of-Thought (CoT)
```
"Hãy suy luận từng bước trước khi trả lời"
```
→ Cải thiện reasoning

### ReAct (Reasoning + Acting)
```
Thought: Tôi cần tìm...
Action: search("...")
Observation: ...
Thought: Bây giờ tôi biết...
Answer: ...
```

### Self-Checking
```
"Sau khi trả lời, hãy tự đánh giá xem câu trả lời có đúng không"
```

### Guardrails trong Prompt
```
"Chỉ trả lời dựa trên thông tin được cung cấp.
Nếu không biết, hãy nói 'Tôi không có thông tin về điều này.'"
```

---

## Inference Parameters

### Temperature
| Value | Effect |
|-------|--------|
| `0` | Deterministic, consistent |
| `0.5` | Balanced |
| `1.0` | Creative, diverse |

### Top P (Nucleus Sampling)
- Ngưỡng xác suất cho token candidates
- Thường set **Temperature HOẶC Top P**

### Top K
- Số lượng token candidates
- VD: Top K = 50 → chỉ xem 50 tokens cao nhất

### Max Tokens
- Giới hạn độ dài response
- Ít tokens = response nhanh hơn

```
Mối quan hệ:
Top P & Top K → Xác định POOL candidates
Temperature → Chọn từ pool đó
```

---

## Mẫu Prompt

### Basic Structure
```
[Role] Bạn là chuyên gia về X.
[Context] Người dùng đang hỏi về Y.
[Task] Hãy giải thích Z.
[Format] Trả lời dưới dạng bullet points.
[Constraints] Tối đa 5 điểm.
```

### RAG Prompt
```
Dựa trên thông tin sau đây, trả lời câu hỏi.
Nếu thông tin không đủ, nói "Tôi không có đủ thông tin."
Trích dẫn nguồn khi có thể.

Context: {retrieved_chunks}

Question: {user_question}
```

---

## Best Practices

### Token Efficiency
- Giữ prompts ngắn gọn
- Thông tin quan trọng lên đầu (phòng truncation)
- Tránh lặp lại

### Testing
- Bộ regression prompts
- A/B testing prompt variants
- Measure: accuracy, latency, cost

### Version Control
- Version hóa prompts
- Track performance per version
- [[Prompt Flows & Management]]

---

## Liên kết

- Flows & Management: [[Prompt Flows & Management]]
- Áp dụng RAG: [[RAG Nâng Cao]]
- Guardrails: [[Amazon Bedrock Guardrails]]
- Inference params: [[Bedrock Playground & cấu hình suy luận]]
