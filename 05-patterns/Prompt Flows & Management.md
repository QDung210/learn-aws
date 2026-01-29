# Prompt Flows & Management

## 1️⃣ Câu hỏi cốt lõi

> **Quản lý prompts như thế nào?**
> - **Phiên bản hóa**: Lưu metadata, người sửa, ngày
> - **Prompt flow**: Orchestrate retrieve → plan → act → verify
> - **Kiểm thử**: Regression prompts, LLM-as-judge

Xem thêm: [[Prompt Engineering Overview]], [[RAG Nâng Cao]]

---

## Quản lý prompt
- Phiên bản hóa prompt; lưu metadata (mục tiêu, mô hình, ngày, người sửa).
- Tách tham số động (context, user input) ra khỏi prompt cố định.
- A/B test prompt và theo dõi chất lượng (accept rate, CSAT, factuality).

## Prompt flow
- Orchestrate nhiều bước: retrieve → plan → act → verify → answer.
- Có thể dùng Bedrock Agents hoặc workflow engine.
- Cho phép fallback: mô hình nhỏ trước, lớn sau; hoặc template khác khi lỗi.

## Kiểm thử
- Bộ regression prompt: câu hỏi bẫy, biên, nhiễu.
- Tự động chấm điểm bằng LLM-as-judge hoặc rule-based (regex, policy).

Áp dụng trong RAG pipeline: [[RAG Overview]]; kết hợp guardrails/policy: [[Amazon Bedrock Guardrails]].

