# Amazon Transcribe

## 1️⃣ Giải quyết vấn đề gì? (WHY)

**Vấn đề kinh điển**: **Speech-to-Text** - chuyển audio/video thành văn bản.

**Nếu không có Transcribe**:
- Tự host Whisper, DeepSpeech
- Quản lý GPU, scaling
- Khó support multi-language

**Transcribe sinh ra để**:
- Transcribe calls, meetings, podcasts
- Subtitles cho videos
- Voice input cho applications
- Serverless, pay-per-use

---

## 2️⃣ Thuộc nhóm nào trong kiến trúc AWS?

| Phân loại | Vị trí |
|-----------|--------|
| **Category** | AI/ML (Speech) |
| **Pipeline** | **Ingestion / Processing** |
| **Pattern** | Audio → Text conversion |

---

## 3️⃣ Input – Output (DATA FLOW)

| Input | Output |
|-------|--------|
| Audio files (S3) | Transcript JSON/text |
| Real-time audio stream | Streaming transcript |
| Video files | Transcript + timestamps |

**Formats**: AMR, FLAC, M4A, MP3, MP4, OGG, WAV, WebM

---

## 4️⃣ Cách hoạt động (HOW – Conceptual)

- **Batch**: Upload audio → async job → get transcript
- **Streaming**: Real-time audio → live transcript
- **Stateless**: Mỗi job độc lập

---

## 5️⃣ Khi nào dùng – Khi nào KHÔNG dùng?

### ✅ Use Cases chuẩn
- Call center transcripts
- Meeting notes automation
- Video subtitles/captions
- Voice commands → text
- Podcast transcription

### ❌ Anti-patterns
| Sai | Đúng |
|-----|------|
| Text analysis (sentiment, entities) | → [[Amazon Comprehend]] |
| Text-to-speech | → Amazon Polly |
| OCR từ images | → Textract |
| Translation | → Amazon Translate |

Chi tiết: [[Transcribe vs Comprehend]]

---

## 6️⃣ So sánh với services khác

| Feature | Transcribe | Comprehend | Bedrock |
|---------|------------|------------|---------|
| **Input** | Audio | Text | Text/Images |
| **Output** | Text transcript | Entities, sentiment | Generated text |
| **Purpose** | Speech → Text | Text analysis | Gen AI |

---

## 7️⃣ Cost – Scale – Limitations

### Pricing
| Type | Giá |
|------|-----|
| **Batch** | $0.024/phút |
| **Streaming** | $0.024/phút |
| **Medical** | $0.075/phút |
| **Call Analytics** | $0.030/phút |

### Limits
| Limit | Value |
|-------|-------|
| Audio length | 4 hours (batch) |
| Concurrent jobs | 100 |
| Languages | 100+ |

---

## Features

### Speaker Diarization
- Phân biệt người nói (Speaker 1, Speaker 2...)
- Channel separation cho stereo calls

### Vocabulary
- **Custom vocabulary**: Thêm từ chuyên ngành
- **Vocabulary filtering**: Mask profanity

### Content Moderation
- Phát hiện từ nhạy cảm
- Redact PII trong transcript

### Transcribe Medical
- Thuật ngữ y khoa
- HIPAA eligible

### Call Analytics
- Sentiment per speaker
- Call categorization
- Interruption detection

---

## Luồng xử lý RAG

```
Audio → Transcribe → Text → Comprehend (entities) 
                        ↓
                    Chunking → Embedding → Vector Store
                        ↓
                    RAG Pipeline
```

---

## Liên kết

- Phân tích sau transcribe: [[Amazon Comprehend]]
- So sánh: [[Transcribe vs Comprehend]]
- Đưa vào RAG: [[RAG Nâng Cao]], [[Xử lý dữ liệu có cấu trúc & phi cấu trúc (GenAI)]]
