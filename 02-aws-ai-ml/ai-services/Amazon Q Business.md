# Amazon Q Business

## 1️⃣ Giải quyết vấn đề gì? (WHY)

**Vấn đề kinh điển**: Nhân viên cần **trả lời câu hỏi từ dữ liệu nội bộ công ty** mà ChatGPT không biết.

**Nếu không có Q Business**:
- Tự build RAG pipeline
- Tích hợp từng data source riêng
- Xử lý permissions, security phức tạp

**Q Business sinh ra để**:
- Fully managed enterprise AI assistant
- 40+ data connectors có sẵn
- Kế thừa ACL/permissions từ source systems
- Trích dẫn nguồn tự động

---

## 2️⃣ Thuộc nhóm nào?

| Phân loại | Vị trí |
|-----------|--------|
| **Category** | AI/ML (Enterprise GenAI) |
| **Pipeline** | **End-to-end RAG** (ingest → serve) |
| **Pattern** | Enterprise Q&A, Knowledge Management |

---

## 3️⃣ DATA FLOW

```
Data Sources (S3, SharePoint, Slack...) → Connectors → Managed RAG → Web UI
                                                            ↓
                                          User Query → Response + Citations
```

---

## 4️⃣ Cách hoạt động

- **Managed RAG**: AWS xử lý ingest, chunk, embed, retrieve
- **ACL-aware**: Chỉ show documents user có quyền
- **Multi-model**: Powered by Bedrock FMs
- **Plugins**: Không chỉ đọc mà còn **hành động** (create Jira, etc.)

---

## 5️⃣ Khi nào dùng – Khi nào KHÔNG dùng?

### ✅ Use Cases chuẩn
- Enterprise knowledge search
- HR policy Q&A
- IT support automation
- Sales intelligence
- Compliance queries

### ❌ Anti-patterns
| Sai | Đúng |
|-----|------|
| Cần custom model training | → SageMaker |
| Public chatbot | → Bedrock + custom UI |
| Developer assistant | → [[Amazon Q Developer]] |
| No-code app building | → [[Amazon Q Apps]] |

Chi tiết: [[Q Business vs Q Developer]]

---

## 6️⃣ So sánh với Q Developer

| Feature | Q Business | Q Developer |
|---------|------------|-------------|
| **Target** | Business users | Developers |
| **Data** | Enterprise docs | AWS + Code |
| **Actions** | Jira, ServiceNow... | CLI, Code gen |
| **UI** | Web app | IDE, Console |

---

## 7️⃣ Cost – Scale – Limitations

### Pricing
| Tier | Giá | Features |
|------|-----|----------|
| **Lite** | Lower | Basic Q&A |
| **Pro** | Higher | Full features, plugins |

Per user/month (bao gồm storage, queries, model calls)

### Limits
- Connectors: 40+
- Document types: Common formats
- Sync: Scheduled or real-time

---

## Data Connectors (40+)

### AWS Services
- [[Amazon S3]], RDS, Aurora, WorkDocs

### Third-Party
- Microsoft 365, SharePoint, Google Drive
- Slack, Salesforce, Confluence, Jira

### Custom
- OpenAPI-based custom connectors

---

## Plugins (Actions)

| Plugin | Actions |
|--------|---------|
| **Jira** | Create/update issues |
| **ServiceNow** | Create tickets |
| **Zendesk** | Manage support tickets |
| **Salesforce** | Update CRM records |
| **Custom** | OpenAPI spec |

---

## Authentication

- **IAM Identity Center** required
- External IdP: Google, Microsoft AD, etc.
- ACL: User chỉ thấy documents họ có quyền

---

## Liên kết

- Q Apps: [[Amazon Q Apps]]
- Q Developer: [[Amazon Q Developer]]
- So sánh: [[Q Business vs Q Developer]]
- Bedrock RAG: [[RAG, Cơ sở tri thức, Vector Store]]
- Storage: [[Amazon S3]]

### AWS Services
| Service | Mô tả |
|---------|-------|
| Amazon S3 | Object storage - files, documents |
| Amazon RDS | Relational database |
| Amazon Aurora | MySQL/PostgreSQL |
| Amazon WorkDocs | Document collaboration |

### Third-Party
| Service | Mô tả |
|---------|-------|
| Microsoft 365 | Office docs, OneDrive |
| SharePoint | Enterprise documents |
| Google Drive | Google Workspace files |
| Gmail | Email content |
| Slack | Messages, channels |
| Salesforce | CRM data |
| Confluence | Wiki, documentation |
| Jira | Issue tracking (read) |

### Custom
- **Custom connectors** sử dụng APIs
- Kết nối bất kỳ data source nào

## Plugins (Actions)

### Mục đích
- Không chỉ **đọc** dữ liệu mà còn **thực hiện hành động**
- Tích hợp với các dịch vụ bên thứ ba

### Plugins tích hợp sẵn
| Plugin | Actions |
|--------|---------|
| Jira | Create/update issues |
| ServiceNow | Create tickets |
| Zendesk | Manage support tickets |
| Salesforce | Update CRM records |

### Custom Plugins
```yaml
# Tạo custom plugin với API
plugin:
  name: my-custom-plugin
  description: "Integrate with internal system"
  api_spec: openapi.yaml
  authentication: oauth2
```

## Authentication với IAM Identity Center

### Workflow

```
User ─────► Login Page ─────► IAM Identity Center
                                      │
                          ┌───────────┴───────────┐
                          │                       │
                    Direct Login         External IdP
                    (Username/Pass)      (Google, MS AD)
                          │                       │
                          └───────────┬───────────┘
                                      │
                                      ▼
                              Authenticated User
                              with Permissions
                                      │
                                      ▼
                              Amazon Q Business
                              (Access only allowed docs)
```

### Access Control
- Người dùng **chỉ thấy tài liệu họ có quyền**
- Kế thừa permissions từ source systems
- Không có rủi ro data leakage giữa users

## Web Experience

### Giao diện
- **Ứng dụng web** được deploy tự động
- Chat interface giống ChatGPT
- Hiển thị sources/citations

### Features
- Conversational Q&A
- Document preview
- Source attribution
- History management

## Ví dụ Use Cases

### 1. HR Assistant
```
Employee: "Chính sách nghỉ phép của công ty là gì?"
Q Business: "Theo HR Policy v2024:
- Nhân viên full-time được 15 ngày phép/năm
- Có thể tích lũy tối đa 5 ngày sang năm sau
- [Nguồn: HR_Policy_2024.pdf, trang 12]"
```

### 2. IT Support
```
Employee: "Làm sao để reset password?"
Q Business: "Để reset password:
1. Truy cập https://reset.company.com
2. Nhập email công ty
3. Check email để xác nhận
[Nguồn: IT_Handbook.docx]"
```

### 3. Sales Intelligence
```
Sales Rep: "Thông tin về khách hàng ABC Corp?"
Q Business: "ABC Corp:
- Ngành: Manufacturing
- Doanh thu: $50M
- Contacts: John (CEO), Jane (CTO)
- Lịch sử: 3 deals closed, 1 pending
[Nguồn: Salesforce CRM]"
```

## Pricing

### Model
- **Per user/month** pricing
- Bao gồm:
  - Data connector usage
  - Storage
  - Queries
  - Foundation model calls

### Tiers
| Tier | Use Case |
|------|----------|
| Lite | Basic Q&A, limited features |
| Pro | Full features, plugins, custom apps |

## Best Practices

### 1. Data Quality
- Đảm bảo documents được organize tốt
- Metadata đầy đủ
- Regular sync schedule

### 2. Security
- Review IAM permissions
- Audit access logs
- PII handling

### 3. User Adoption
- Training cho users
- Clear use case communication
- Feedback collection

### 4. Content Governance
- Định kỳ review data sources
- Remove outdated content
- Update sync configurations

## Liên kết

- Q Apps: [[Amazon Q Apps]]
- Q Developer: [[Amazon Q Developer]]
- Bedrock RAG: [[RAG, Cơ sở tri thức, Vector Store]]
- S3: [[Amazon S3]]

