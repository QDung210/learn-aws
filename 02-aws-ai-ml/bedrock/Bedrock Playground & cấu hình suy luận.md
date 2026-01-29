# Bedrock Playground & cấu hình suy luận

## 1️⃣ Câu hỏi cốt lõi

> **Playground dùng để làm gì?**
> - **Test models** trực tiếp trên console
> - **Tinh chỉnh tham số**: temperature, top-k, top-p, max tokens
> - **Thử multimodal**: chat, image, video generation

Xem thêm: [[Tổng quan Amazon Bedrock]], [[Tinh chỉnh & LoRA trên Bedrock]]

---

## Chế độ
- Chat: giữ lịch sử, phù hợp hội thoại liên tục.
- Prompt đơn: không lưu lịch sử, một lần là xong.
- Image/Video playground: tạo hình ảnh (Nova Canvas) hoặc video (Nova Reels), hỗ trợ biến thể, inpaint, đổi nền.

## Tham số thường gặp
- System prompt: bối cảnh chung áp dụng mọi lượt.
- Model reasoning / chain-of-thought: cho phép mô hình chia bài toán thành bước nhỏ (tăng cost, dùng khi nhiệm vụ phức tạp).
- Max output tokens: giới hạn độ dài trả lời.
- Stop sequences: dừng sinh khi gặp chuỗi nhất định.
- Temperature / Top-K / Top-P: điều chỉnh độ ngẫu nhiên & đa dạng (nhiệt độ=0 cho kết quả ổn định).
- Image params: số ảnh, kích thước, prompt/negative prompt, palette, seed, guidance scale.

## Mẹo kiểm thử
- Dùng system prompt để ép phong cách (vd. “trả lời như cướp biển”).
- Với multimodal, có thể tải tệp (doc, hình) để hỏi đáp bám nguồn.
- Với streaming: hữu ích cho UI hiển thị dần, giảm độ trễ cảm nhận.

Chi tiết API runtime/agent: [[Runtime API, Agent, Knowledge Base]]; RAG/KB: [[RAG, Cơ sở tri thức, Vector Store]]; tinh chỉnh/LoRA: [[Tinh chỉnh & LoRA trên Bedrock]].
