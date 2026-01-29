# Tinh chỉnh & LoRA trên Bedrock

## 1️⃣ Câu hỏi cốt lõi

> **Khi nào cần tinh chỉnh model?**
> - **Phản hồi nhất quán** theo phong cách/thương hiệu
> - **Giảm prompt dài dòng**, tiết kiệm token suy luận
> - Dữ liệu miền hẹp ổn định, không đổi liên tục

So sánh: [[RAG vs Finetune]]. Xem thêm: [[Tổng quan Amazon Bedrock]]

---

## Khái niệm
- Tinh chỉnh (custom model): tiếp tục huấn luyện mô hình nền với dữ liệu gắn nhãn (prompt–completion) hoặc hình ảnh + mô tả.
- Continued pre-training: nạp thêm dữ liệu chưa gắn nhãn (raw text) để mở rộng kiến thức nền.
- LoRA (Low-Rank Adaptation): thêm ma trận hạng thấp vào các lớp attention → giảm tham số cần cập nhật, rẻ & nhanh hơn so với huấn luyện full.

## Lợi ích
- Giảm prompt engineering dài dòng, tiết kiệm token lúc suy luận.
- Ghi nhớ phong cách/thương hiệu, dữ liệu độc quyền, hoặc cập nhật kiến thức mới hơn cutoff.
- Có thể tinh chỉnh lặp lại (incremental) khi có dữ liệu mới.

## Quy trình
1) Chuẩn bị dữ liệu: JSON các cặp `{"prompt":..., "completion":...}` (text) hoặc liên kết S3 tới ảnh + mô tả (image models); mẹo chuẩn hóa/chunking xem [[Xử lý dữ liệu có cấu trúc & phi cấu trúc (GenAI)]].
2) Upload lên S3, tạo job tinh chỉnh hoặc continued pre-training trong Bedrock.
3) Chờ train (tốn thời gian/chi phí), kết quả là model ID mới; dùng API giống hệt mô hình gốc.
4) Có thể tiếp tục tinh chỉnh trên model đã tinh chỉnh.

## Bảo mật & vận hành
- Dữ liệu nhạy cảm: khuyến nghị chạy trong VPC với PrivateLink khi train/tinh chỉnh.
- Theo dõi chi phí: tinh chỉnh mô hình lớn rất tốn; cân nhắc khối lượng dữ liệu vs lợi ích giảm token suy luận.
- Không phải mọi mô hình đều cho phép tinh chỉnh; kiểm tra thẻ thông tin mô hình trong console.

## Khi nào dùng tinh chỉnh vs RAG
- Dùng tinh chỉnh khi: cần phản hồi nhất quán theo phong cách thương hiệu, dữ liệu miền hẹp lặp lại, muốn giảm độ dài prompt lâu dài.
- Dùng RAG khi: kiến thức thay đổi liên tục, muốn cập nhật nhanh, hoặc dữ liệu lớn khó train lại; chi tiết pipeline xem [[RAG, Cơ sở tri thức, Vector Store]].
- Có thể kết hợp: tinh chỉnh cho phong cách, RAG cho dữ liệu động; so sánh thêm tại [[RAG vs Finetune]].