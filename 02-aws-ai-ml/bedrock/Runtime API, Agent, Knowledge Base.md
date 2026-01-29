# Runtime API, Agent, Knowledge Base

## 1️⃣ Câu hỏi cốt lõi

> **Các API chính của Bedrock?**
> - **Runtime API**: `invoke_model`, `converse` - suy luận trực tiếp
> - **Agent Runtime**: Orchestration nhiều bước, gọi tools
> - **Knowledge Base API**: RAG pipeline - ingest, chunk, embed, query

Xem thêm: [[Tổng quan Amazon Bedrock]], [[RAG, Cơ sở tri thức, Vector Store]]

---

## Runtime & Converse
- API chính để suy luận: `invoke model`, `converse` (chat lịch sử), streaming phản hồi.
- Đầu vào chuẩn JSON (Bedrock & SageMaker đều yêu cầu tự format JSON: messages, role, content, system prompt, max_tokens...).
- Chat history: mảng `messages[{role, content}]`; role: `user|assistant`.

## Agent & Agent Runtime
- Agent = orkestrator có thể chạy nhiều mô hình song song, gọi công cụ ngoài, truy cập KB.
- Agent Runtime: endpoint để thực thi agent với prompt và công cụ/KBase gắn kèm.
- Dùng khi cần workflow nhiều bước, gọi APIs nội bộ, hoặc phân tách nhiệm vụ.

## Knowledge Base (KB)
- Quản lý pipeline RAG: ingest → chunk → embed → lưu vector → truy xuất theo ngữ nghĩa.
- Hỗ trợ Lambda pre-processor để chèn divider strings, chuẩn hóa HTML trước khi chunking.
- Lưu ý token cost: dữ liệu KB được chèn vào prompt tăng context length.

Hướng dẫn RAG/KB đầy đủ: [[RAG, Cơ sở tri thức, Vector Store]]; mẹo ingest/chunking: [[Xử lý dữ liệu có cấu trúc & phi cấu trúc (GenAI)]].

## Quyền & best-practice
- IAM: cấp quyền Bedrock phù hợp (full vs read-only). Không dùng root.
- Model access: hiện dùng là bị tính phí ngay, không cần request thủ công.

Thử cấu hình inference trên playground: [[Bedrock Playground & cấu hình suy luận]].
