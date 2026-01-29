# Amazon Transcribe vs Amazon Comprehend

## 1️⃣ Câu hỏi cốt lõi

> **Khi nào chọn cái nào?**
> - **[[Amazon Transcribe]]**: Audio/Video → Text (Speech-to-Text)
> - **[[Amazon Comprehend]]**: Text → Structured data (Text Analysis)

Thường dùng cùng nhau: Transcribe → Comprehend

---

## Tổng quan nhanh

| Tiêu chí | Transcribe | Comprehend |
|----------|------------|------------|
| **Input** | Audio/Video | Text |
| **Output** | Text (transcript) | Structured data (entities, sentiment...) |
| **Function** | Speech-to-Text | Text Analysis |
| **Use case** | Chuyển đổi giọng nói | Phân tích văn bản |

---

## Khi nào dùng Transcribe?

✅ **Speech-to-Text** - chuyển audio thành text  
✅ **Call center recordings** - phân tích cuộc gọi  
✅ **Meeting transcription** - ghi chép cuộc họp  
✅ **Video subtitles** - tạo phụ đề  
✅ **Voice commands** - xử lý giọng nói  
✅ **Medical dictation** - Transcribe Medical  

### Đặc điểm
- Real-time và batch
- Multi-speaker diarization
- Custom vocabulary
- Automatic punctuation
- PII redaction trong audio

---

## Khi nào dùng Comprehend?

✅ **Entity extraction** - tìm người, địa điểm, tổ chức  
✅ **Sentiment analysis** - tích cực/tiêu cực  
✅ **Key phrase extraction** - từ khóa quan trọng  
✅ **PII detection** - thông tin nhạy cảm  
✅ **Topic modeling** - phân loại chủ đề  
✅ **Medical NER** - Comprehend Medical  

### Đặc điểm
- Batch và real-time
- Custom classification
- Multi-language
- Structured output

---

## So sánh

| Aspect | Transcribe | Comprehend |
|--------|------------|------------|
| Input format | Audio (WAV, MP3, FLAC...) | Text (UTF-8) |
| Output format | Text transcript | JSON (structured) |
| Real-time | ✅ Streaming | ✅ API |
| Batch | ✅ S3 jobs | ✅ S3 jobs |
| Custom models | Custom vocabulary | Custom classifier |
| PII handling | Redaction | Detection |
| Medical | Transcribe Medical | Comprehend Medical |

---

## Common Pattern: Pipeline

```
┌─────────────────────────────────────────────────────────────┐
│           TRANSCRIBE → COMPREHEND PIPELINE                   │
│                                                              │
│  Audio/Video                                                 │
│       │                                                      │
│       ▼                                                      │
│  ┌──────────────┐                                           │
│  │  Transcribe  │ ──► Text transcript                       │
│  └──────────────┘                                           │
│                          │                                   │
│                          ▼                                   │
│                   ┌──────────────┐                          │
│                   │  Comprehend  │                          │
│                   └──────────────┘                          │
│                          │                                   │
│         ┌────────────────┼────────────────┐                 │
│         ▼                ▼                ▼                 │
│     Entities         Sentiment       Key Phrases            │
│                                                              │
│  Use case: Call center analytics, meeting insights          │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Example: Call Center Analytics
```
1. Transcribe: Audio → Text
2. Transcribe: Speaker diarization (agent vs customer)
3. Comprehend: Sentiment per speaker
4. Comprehend: Key phrases extraction
5. Comprehend: Custom classification (issue type)
6. Dashboard: Aggregate insights
```

---

## Decision Table

| Need | Service |
|------|---------|
| Convert audio to text | Transcribe |
| Analyze text content | Comprehend |
| Find sentiment in call | Transcribe → Comprehend |
| Add subtitles to video | Transcribe |
| Extract entities from document | Comprehend |
| Real-time speech recognition | Transcribe Streaming |
| Classify support tickets | Comprehend Custom |
| Redact PII from audio | Transcribe |
| Detect PII in text | Comprehend |

---

## Xem thêm
- [[Amazon Transcribe]]
- [[Amazon Comprehend]]
- [[Comprehend vs Bedrock]]
