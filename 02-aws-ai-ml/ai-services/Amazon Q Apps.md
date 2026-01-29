# Amazon Q Apps

## 1️⃣ Câu hỏi cốt lõi

> **Q Apps giải quyết vấn đề gì?**
> - Tạo **ứng dụng Gen-AI không cần code**
> - Dùng **ngôn ngữ tự nhiên** để mô tả app
> - Dành cho **business users**, không cần developer

Phần của [[Amazon Q Business]]. Xem thêm: [[Q Business vs Q Developer]]

---

## Tổng quan

### Q Apps là gì?
- **Phần của Amazon Q Business**
- Tạo **ứng dụng Gen-AI** mà **không cần lập trình**
- Sử dụng **ngôn ngữ tự nhiên** để mô tả app
- Dựa trên **dữ liệu nội bộ công ty** + **plugins**

### Đối tượng sử dụng
- **Business users** (không phải developers)
- Ai cũng có thể tạo app
- Không cần kỹ năng coding

## Cách hoạt động

```
┌─────────────────────────────────────────────────────────┐
│              AMAZON Q APPS CREATOR                       │
│                                                          │
│  User Prompt:                                            │
│  "Tạo app để tóm tắt meeting notes từ file upload"      │
│                                                          │
│                         │                                │
│                         ▼                                │
│  ┌──────────────────────────────────────────────┐       │
│  │          Q Apps Generator                     │       │
│  │                                               │       │
│  │  1. Phân tích requirements                    │       │
│  │  2. Thiết kế UI components                    │       │
│  │  3. Kết nối data sources                      │       │
│  │  4. Setup prompts                             │       │
│  └──────────────────────────────────────────────┘       │
│                         │                                │
│                         ▼                                │
│  ┌──────────────────────────────────────────────┐       │
│  │          Generated Web App                    │       │
│  │                                               │       │
│  │  ┌────────────────────────────────┐          │       │
│  │  │  📤 Upload Meeting Notes       │          │       │
│  │  │  [Choose File]                 │          │       │
│  │  ├────────────────────────────────┤          │       │
│  │  │  💬 Additional Instructions    │          │       │
│  │  │  [Text input]                  │          │       │
│  │  ├────────────────────────────────┤          │       │
│  │  │  [Generate Summary]            │          │       │
│  │  ├────────────────────────────────┤          │       │
│  │  │  📝 Summary Output             │          │       │
│  │  │  [Generated content here]      │          │       │
│  │  └────────────────────────────────┘          │       │
│  └──────────────────────────────────────────────┘       │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

## Tính năng

### 1. Natural Language App Creation
```
Prompt: "Tạo app để:
- Upload CV của ứng viên
- So sánh với job description
- Đánh giá độ phù hợp
- Đề xuất câu hỏi phỏng vấn"

→ Q Apps tự động tạo app với các components cần thiết
```

### 2. Data Source Integration
- Sử dụng **data connectors** từ Q Business
- Access enterprise knowledge
- Real-time data

### 3. Plugin Integration
- Tích hợp **actions** từ Q Business plugins
- Ví dụ: Tạo JIRA ticket từ app

### 4. Sharing
- Chia sẻ apps với team
- Permission management
- App library

## Ví dụ Use Cases

### 1. Meeting Summary App
```
Input:
- Upload: meeting_notes.txt

Output:
- Key decisions
- Action items
- Attendees summary
```

### 2. Job Posting Generator
```
Input:
- Job title: "Senior Developer"
- Department: "Engineering"
- Requirements: "5 years experience, Python"

Output:
- Formatted job posting
- Following company tone/style
```

### 3. Proposal Reviewer
```
Input:
- Upload: vendor_proposal.pdf

Output:
- Key terms summary
- Risk analysis
- Comparison with company policies
```

### 4. Customer Email Generator
```
Input:
- Customer name
- Issue type
- Resolution

Output:
- Professional email response
- Following brand guidelines
```

## So sánh

| Feature | Q Business | Q Apps |
|---------|------------|--------|
| Interface | Chat | Custom app UI |
| Use | Ad-hoc questions | Repeatable workflows |
| Creation | N/A | No-code builder |
| Sharing | N/A | Sharable apps |
| Audience | All employees | Specific team needs |

## Workflow

### 1. Tạo App
```
Q Apps Creator
      │
      ▼
Describe app in natural language
      │
      ▼
Q generates app structure
      │
      ▼
Review & customize
      │
      ▼
Publish
```

### 2. Sử dụng App
```
User
  │
  ▼
Select app from library
  │
  ▼
Provide inputs (files, text)
  │
  ▼
App processes with Q Business backend
  │
  ▼
Receive output
```

## Limitations

- **Phụ thuộc Q Business** - cần setup Q Business trước
- **Complexity limits** - complex apps may need custom dev
- **Data access** - limited to Q Business connectors

## Best Practices

### 1. Clear Descriptions
```
❌ "Tạo app xử lý documents"
✅ "Tạo app upload PDF hợp đồng, trích xuất các điều khoản quan trọng, 
    highlight các rủi ro pháp lý"
```

### 2. Iterative Refinement
- Tạo version đơn giản trước
- Test với users
- Thêm features dần

### 3. Naming Convention
- Tên app rõ ràng
- Description đầy đủ
- Tags cho categorization

## Liên kết

- Q Business: [[Amazon Q Business]]
- Q Developer: [[Amazon Q Developer]]
- RAG: [[RAG, Cơ sở tri thức, Vector Store]]

