# Xử lý dữ liệu có cấu trúc & phi cấu trúc (GenAI)

## 1️⃣ Câu hỏi cốt lõi

> **Cách chuẩn bị dữ liệu cho GenAI/RAG?**
> - **Giữ cấu trúc**: Chuyển PDF/Doc → HTML giữ heading, bảng
> - **Chunking thông minh**: Chèn divider strings trước khi chunk
> - Công cụ: [[AWS Glue & Data Catalog]], [[Bedrock Data Automation (BDA)]]

---

## Định dạng vào/ra
- Bedrock & SageMaker: tự xây JSON input/output; model không tự format.
- Chat JSON mẫu: `{messages: [{role:'user|assistant', content:'...'}], system:'...', maxTokens:...}`.

## Giữ cấu trúc khi ingest
- Với tài liệu OCR/PDF: chuyển thành HTML để giữ heading, bảng → model hiểu ngữ cảnh tốt hơn.
- Công cụ: Textract/Comprehend hoặc Pandoc; pipeline ingest có thể dùng [[AWS Glue & Data Catalog]] hoặc [[Bedrock Data Automation (BDA)]]

## Chunking cho RAG
- Chèn divider strings (section/subsection) trước khi chunk để tránh cắt ngang ý.
- Lambda pre-processor của Knowledge Base có thể chuyển H1/H2 → `<<SECTION>>`, `<<SUBSECTION>>` rồi chunk.

## Định dạng hội thoại (Converse API)
- Mỗi message cần `role` + `content`; giữ lịch sử để mô hình duy trì ngữ cảnh.

Ứng dụng: RAG/KB xem [[RAG, Cơ sở tri thức, Vector Store]]; cân nhắc tinh chỉnh hay RAG tại [[RAG vs Finetune]]; tự động trích xuất với [[Bedrock Data Automation (BDA)]].
