# Agent Memory (Bộ nhớ của Tác nhân)

## 1️⃣ Câu hỏi cốt lõi

> **Tại sao AI Agents cần Memory?**
> - **Short-term**: Nhớ ngữ cảnh trong cuộc trò chuyện hiện tại
> - **Long-term**: Nhớ user preferences, insights qua nhiều sessions

Xem thêm: [[LLM Agents & Agentic AI]], [[Amazon AgentCore]], [[Amazon DynamoDB]]

---

## Tổng quan

Giống như con người, AI Agents cũng có **hai dạng bộ nhớ**:
- **Short-term Memory** (Bộ nhớ ngắn hạn)
- **Long-term Memory** (Bộ nhớ dài hạn)

## Short-term Memory (Bộ nhớ ngắn hạn)

### Khái niệm
- Là **lịch sử cuộc trò chuyện hiện tại** (session)
- Cho phép tham chiếu đến những gì đã nói trước đó
- Tồn tại trong suốt phiên làm việc

### Ví dụ
```
User: "Thời tiết Hà Nội thế nào?"
Agent: "Hà Nội hiện tại 28°C, trời nắng."

User: "Còn ở Đà Nẵng?"  ← Tham chiếu ngữ cảnh
Agent: "Đà Nẵng hiện tại 32°C, nhiều mây."

User: "Làm điều đó lần nữa."  ← Cần nhớ "điều đó" là gì
```

### Lưu trữ
- Có thể chỉ trong **bộ nhớ (RAM)**
- Hoặc **distributed cache**:
  - Amazon ElastiCache
  - Amazon MemoryDB
  - Amazon DynamoDB

### Nội dung lưu trữ
- Tin nhắn của user và agent
- Các sự kiện trong phiên (tools được gọi, files upload)
- Session metadata

## Long-term Memory (Bộ nhớ dài hạn)

### Khái niệm
- Thông tin **tồn tại qua nhiều phiên**
- Cho phép quay lại cuộc trò chuyện cũ
- Lưu trữ **insights** và **preferences** của người dùng

### Các loại thông tin lưu trữ

#### 1. Session Summaries
- Tóm tắt các cuộc trò chuyện trước đó
- Không lưu nguyên văn → tiết kiệm token và storage
- Dễ dàng đưa vào context cho phiên mới

#### 2. User Preferences
- Phong cách coding
- Công cụ yêu thích
- Ngôn ngữ ưa thích
- Thói quen làm việc

#### 3. Semantic Facts
- Các sự thật người dùng đã nói
- "Hãy nhớ tôi là developer Python"
- "Tôi thích code ngắn gọn"

#### 4. Explicit Facts
- Thông tin người dùng yêu cầu lưu rõ ràng
- "Remember this: API key là xyz..."

### Ví dụ: ChatGPT Memory
ChatGPT học được về bạn qua các cuộc trò chuyện:
- Phong cách lập trình của bạn
- Công cụ yêu thích
- Sở thích cá nhân
- Ngữ cảnh công việc

→ Cá nhân hóa phản hồi trong tương lai

## Memory Profiles (Hồ sơ bộ nhớ)

### Khái niệm
- Cấu trúc để tổ chức long-term memory
- Có thể có **chiến lược khác nhau** cho từng loại

### Chiến lược lưu trữ
| Loại | Chiến lược | Ví dụ |
|------|-----------|-------|
| User Preferences | Key-value store | `{"coding_style": "minimal"}` |
| Semantic Facts | Vector store + search | Embedding facts |
| Session Summaries | Document store | Tóm tắt từng session |

## Giải pháp lưu trữ trên AWS

### 1. Amazon DynamoDB
- Key-value và document store
- Phù hợp cho cả short-term và long-term
- Serverless, tự động scale
- Xem: [[Amazon DynamoDB]]

### 2. Amazon ElastiCache / MemoryDB
- In-memory caching
- Tốc độ cao cho short-term memory
- Redis-compatible

### 3. SQLite (cho development)
- OpenAI SDK sử dụng mặc định
- Chỉ phù hợp cho demo/prototype
- **Không scale** cho production

### 4. Amazon AgentCore Memory
- **Giải pháp tốt nhất** cho Agents
- Tích hợp sẵn với Strands SDK
- Serverless, scalable
- Hỗ trợ cả short-term và long-term
- Xem: [[Amazon AgentCore]]

### 5. Mem0 (Third-party)
- Tích hợp với Strands SDK
- Memory management cho AI apps
- Hỗ trợ cá nhân hóa

## Knowledge Base vs Memory

| Aspect | Knowledge Base | Memory |
|--------|---------------|--------|
| Nguồn | Dữ liệu bên ngoài (docs, DB) | Tương tác với người dùng |
| Mục đích | Cung cấp context | Cá nhân hóa |
| Cập nhật | Batch/scheduled | Real-time |
| Phạm vi | Chung cho tất cả users | Riêng cho từng user |

> **Lưu ý**: Knowledge Base cũng là một dạng "long-term memory" nhưng từ góc độ thông tin bên ngoài, không phải từ user interactions.

## AgentCore Memory API

### Short-term Memory
```python
from agentcore import Session, Event

# Tạo session mới
session = Session.create(user_id="user123")

# Thêm events
session.add_event(Event(
    type="user_message",
    content="Xin chào!"
))

session.add_event(Event(
    type="agent_response", 
    content="Chào bạn!"
))

# Lấy lịch sử
history = session.get_events()
```

### Long-term Memory
```python
from agentcore import MemoryProfile

# Tạo memory profile
profile = MemoryProfile(user_id="user123")

# Chiến lược: User Preferences
profile.set_preference("coding_style", "minimal")
profile.set_preference("language", "vietnamese")

# Chiến lược: Semantic Facts
profile.add_fact("User is a Python developer")
profile.add_fact("User prefers async programming")

# Chiến lược: Session Summaries
profile.add_summary(
    session_id="session456",
    summary="Discussed Python async patterns"
)

# Truy xuất
preferences = profile.get_preferences()
facts = profile.search_facts("programming")
```

## Best Practices

### 1. Phân biệt rõ short/long-term
- Short-term: Chỉ trong session hiện tại
- Long-term: Persist qua sessions

### 2. Không lưu quá nhiều
- Tóm tắt thay vì lưu nguyên văn
- Xóa thông tin không còn relevant

### 3. Bảo mật
- Encrypt sensitive data
- Respect user privacy
- Cho phép user xóa memory

### 4. Sử dụng AgentCore cho production
- SQLite chỉ cho dev/prototype
- AgentCore scalable và managed

### 5. Integration với Strands
```python
from strands import Agent
from agentcore.memory import Memory

# Tích hợp memory vào agent
agent = Agent(
    name="my_agent",
    memory=Memory(provider="agentcore")
)
```

## Liên kết

- Agent cơ bản: [[LLM Agents & Agentic AI]]
- Multi-Agent: [[Multi-Agent Workflows]]
- AgentCore: [[Amazon AgentCore]]
- Strands SDK: [[Strands Agents SDK]]
- DynamoDB: [[Amazon DynamoDB]]
- RAG/KB: [[RAG, Cơ sở tri thức, Vector Store]]



