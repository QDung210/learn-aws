# LLM Agents & Agentic AI

## 1️⃣ Giải quyết vấn đề gì? (WHY)

**Vấn đề kinh điển**: LLM chỉ generate text → cần **hành động trong thế giới thực**.

**Agents sinh ra để**:
- Gọi APIs, query databases
- Thực hiện multi-step workflows
- Tự quyết định công cụ nào cần dùng
- Kết hợp nhiều khả năng (reasoning + actions)

---

## 2️⃣ Thuộc nhóm nào?

| Phân loại | Vị trí |
|-----------|--------|
| **Category** | AI/ML Pattern |
| **Pipeline** | **Orchestration layer** |
| **Components** | LLM + Tools + Memory |

---

## 3️⃣ DATA FLOW

```
User Input → Agent (LLM) → Tool Selection → Tool Execution → LLM Analysis → Response
                 ↑                                    |
                 └────────────── Loop if needed ──────┘
```

---

## Kiến trúc Agent

```
┌─────────────────────────────────────────┐
│           AGENT CORE                     │
│  ┌─────────────────────────────────┐    │
│  │    Foundation Model (LLM)       │    │
│  └─────────────────────────────────┘    │
│                 ↓                        │
│  ┌─────────────────────────────────┐    │
│  │    Planning Module               │    │
│  │  - Chia nhỏ yêu cầu              │    │
│  │  - Chọn công cụ phù hợp          │    │
│  └─────────────────────────────────┘    │
│                 ↓                        │
│  ┌─────────────────────────────────┐    │
│  │    Memory (Bộ nhớ)               │    │
│  │  - Short-term (session)          │    │
│  │  - Long-term (cross-session)     │    │
│  └─────────────────────────────────┘    │
│                 ↓                        │
│  ┌─────────────────────────────────┐    │
│  │    Tools (Công cụ)               │    │
│  │  - APIs, Lambda, KB              │    │
│  └─────────────────────────────────┘    │
└─────────────────────────────────────────┘
```

---

## Agents trong Amazon Bedrock

### Thành phần chính

#### 1. Foundation Model
- Claude, Titan, Llama...
- Phân tích, quyết định, tổng hợp

#### 2. Action Groups (Tools)
- **Lambda functions** được mô tả bằng ngôn ngữ tự nhiên
- Agent tự biết khi nào gọi
- Định nghĩa qua JSON schema hoặc Console

#### 3. Knowledge Bases
- Tích hợp RAG
- **Agentic RAG** = Agent + KB
- [[RAG, Cơ sở tri thức, Vector Store]]

#### 4. Code Interpreter
- Agent viết và chạy Python
- Tính toán động

### Tính năng quan trọng

| Feature | Description |
|---------|-------------|
| **User Input Elicitation** | Agent hỏi lại khi thiếu thông tin |
| **Session Management** | Lịch sử conversation |
| **Guardrails** | [[Amazon Bedrock Guardrails]] |

---

## Magic của Agents

> Agent tự động hiểu cách sử dụng tools chỉ từ **mô tả bằng tiếng Anh**

```
Tool Description: "Lấy thời tiết hiện tại cho một thành phố"
→ Agent tự biết khi user hỏi "Thời tiết Hà Nội?" thì gọi tool này
```

Không cần hard-code rules!

---

## Liên kết

- Bedrock KB: [[RAG, Cơ sở tri thức, Vector Store]]
- Memory: [[Agent Memory]]
- Multi-agent: [[Multi-Agent Workflows]]
- Tools: [[OpenAPI & Tool Usage]]
- MCP: [[Model Context Protocol]]
- Strands SDK: [[Strands Agents SDK]]
- AgentCore: [[Amazon AgentCore]]
- Hands-on: [[Hands On Bedrock Agents]]
    └── units (string, optional): "Đơn vị nhiệt độ: C hoặc F"
```

### Luồng xử lý
1. User: "Thời tiết hôm nay ở Hà Nội thế nào?"
2. Agent phân tích → xác định cần gọi GetWeather
3. Trích xuất tham số: city="Hà Nội"
4. Gọi Lambda function với tham số
5. Nhận kết quả → tổng hợp phản hồi cho user

## So sánh với Traditional Development

| Aspect | Traditional | Agentic AI |
|--------|-------------|------------|
| API Calls | Hard-coded rules | LLM tự quyết định |
| Interface | Strict API specs | Natural language descriptions |
| Routing | If-else logic | Semantic understanding |
| Extensibility | Code changes | Thêm mô tả công cụ |

## Best Practices

1. **Mô tả rõ ràng**: Viết mô tả công cụ/tham số chi tiết và rõ ràng
2. **Phân chia hợp lý**: Không gộp quá nhiều chức năng vào một Action Group
3. **Error handling**: Lambda function cần trả về thông báo lỗi rõ ràng
4. **Timeout**: Cấu hình timeout phù hợp với độ phức tạp
5. **Guardrails**: Kết hợp với [[Amazon Bedrock Guardrails]] để kiểm soát đầu vào/đầu ra

## Liên kết

- Xây dựng Agent thực hành: [[Hands On Bedrock Agents]]
- Hệ thống đa tác nhân: [[Multi-Agent Workflows]]
- Bộ nhớ Agent: [[Agent Memory]]
- SDK phát triển: [[Strands Agents SDK]]
- Triển khai quy mô: [[Amazon AgentCore]]
- Foundation model cơ bản: [[Tổng quan Amazon Bedrock]]
