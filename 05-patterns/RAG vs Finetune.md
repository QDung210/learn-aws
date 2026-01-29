# RAG vs Finetune: chọn chiến lược

## 1️⃣ Câu hỏi cốt lõi

> **Khi nào chọn cái nào?**
> - **[[RAG Nâng Cao|RAG]]**: Dữ liệu thay đổi liên tục, cập nhật nhanh
> - **[[Tinh chỉnh & LoRA trên Bedrock|Finetune]]**: Dữ liệu ổn định, cần phong cách nhất quán
> - **Kết hợp**: Finetune cho phong cách, RAG cho nội dung động

---

## RAG (Retrieval-Augmented Generation)
- Ưu: cập nhật nhanh, không train; phù hợp dữ liệu động/lớn; tận dụng tìm kiếm ngữ nghĩa.
- Nhược: prompt dài tốn token; phụ thuộc chất lượng retrieve; cần vận hành vector store; vẫn có thể ảo giác nếu retrieve sai.
- Dùng khi: tri thức thay đổi liên tục (tin tức, chính sách), nhiều tài liệu, cần truy vấn ad-hoc.

## Finetune / Continued pre-training
- Ưu: phản hồi nhất quán, ít token input; phù hợp phong cách/thương hiệu; giảm engineering ở inference.
- Nhược: chi phí train cao, mất thời gian; phải train lại khi kiến thức đổi; không phải model nào cũng cho phép.
- Dùng khi: miền hẹp, nội dung ổn định; cần giọng điệu riêng; muốn tối ưu cost dài hạn.

## Kết hợp
- Tinh chỉnh cho phong cách & định dạng; RAG cho dữ liệu cập nhật.
- BDA/Glue để trích xuất + chuẩn hóa trước khi embed: [[Bedrock Data Automation (BDA)]]

Nếu cần chi tiết RAG/KB: [[RAG, Cơ sở tri thức, Vector Store]]; xem thêm tinh chỉnh: [[Tinh chỉnh & LoRA trên Bedrock]].
