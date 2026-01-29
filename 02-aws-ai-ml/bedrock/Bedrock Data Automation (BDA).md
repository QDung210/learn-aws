# Bedrock Data Automation (BDA)

## 1️⃣ Câu hỏi cốt lõi

> **BDA giải quyết vấn đề gì?**
> - **Trích xuất dữ liệu cấu trúc tự động** từ document/image/video/audio
> - **Chuẩn bị dữ liệu** cho vector store/Knowledge Base
> - Intelligent Document Processing

Xem thêm: [[RAG, Cơ sở tri thức, Vector Store]], [[Xử lý dữ liệu có cấu trúc & phi cấu trúc (GenAI)]]

---

## Khái niệm
- Dịch vụ trích xuất dữ liệu có cấu trúc tự động, đa phương thức (document, image, video, audio).
- Ứng dụng chính: Intelligent Document Processing, chuẩn bị dữ liệu cho vector store/KB.

## Đầu ra
- Standard output: tự động chọn định dạng hợp lý (doc → JSON, audio → transcript...).
- Custom output: định nghĩa blueprint cho fields cần trích; có sẵn blueprint mẫu (ví dụ bằng lái xe), có thể mở rộng.
- Tùy chọn trả bounding boxes, summary sinh sẵn, xuất Markdown/HTML/CSV/JSON + files phụ.

## Blueprint & Project
- Blueprint chứa schema trường (basic, table, group, custom type). Có thể sinh từ prompt hoặc chỉnh tay.
- Project lưu nhiều blueprint cho nhiều loại tài liệu; dùng API `invoke data automation async` để xử lý hàng loạt.

## File hỗ trợ
- Doc: PDF/TIFF/JPEG/PNG/DOCX → JSON hoặc JSON+files (CSV bảng, markdown, HTML/CSV export).
- Image: caption, IAB taxonomy, logo/text detection, moderation.
- Video: summary, chapter summary, transcript, text/logo detection, moderation (MP4/MOV/AVI/MKV/WebM).
- Audio: summary, transcript kèm speaker/channel label, topic segmentation, moderation (AMR/FLAC/M4A/MP3/OGG/WAV).

## Lưu ý
- Cần bật cross-region support trong console demo.
- Có thể chỉ định bucket/KMS riêng, tag tài nguyên.

Chuẩn hóa/chunking trước embed: xem [[Xử lý dữ liệu có cấu trúc & phi cấu trúc (GenAI)]]; kết quả thường dùng cho RAG/KB tại [[RAG, Cơ sở tri thức, Vector Store]].
