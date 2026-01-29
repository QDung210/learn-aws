# Multi-Agent Workflows (Quy trình đa tác nhân)

## 1️⃣ Câu hỏi cốt lõi

> **Khi nào cần Multi-Agent thay vì Single Agent?**
> - **Quá nhiều tools**: Single agent không thể chọn đúng
> - **Logic phức tạp**: Workflow đòi hỏi quyết định nhiều bước

Patterns: Orchestrator, Router, Swarm. Xem thêm: [[LLM Agents & Agentic AI]], [[Agent Squad]]

---

## Tổng quan

### Từ Single Agent đến Multi-Agent
- **Single Agent**: Một FM được trang bị một bộ công cụ
- **Multi-Agent**: Nhiều agent chuyên biệt làm việc cùng nhau
- Còn gọi là: **LLM-augmented systems**, **Agentic workflows**

### Khi nào cần Multi-Agent?

#### 1. Quá nhiều công cụ
- Single agent với bộ công cụ khổng lồ → khó chọn đúng công cụ
- **Context space** bị giới hạn → không tập trung được
- **Giải pháp**: Chia thành nhiều agent chuyên biệt

#### 2. Logic phức tạp
- Prompt với nhiều điều kiện if-else phức tạp
- Workflow đòi hỏi quyết định nhiều bước
- **Giải pháp**: Định nghĩa workflow với nhiều agent

### Lợi ích của Multi-Agent
- Mỗi agent **tập trung vào nhiệm vụ riêng**
- Có thể chạy **song song** hoặc **tuần tự**
- Mỗi agent có **bộ nhớ riêng** → không bị ô nhiễm context
- **Mở rộng dễ dàng**: Thêm agent mới không ảnh hưởng agent khác

## Các mẫu Multi-Agent (Patterns)

### 1. Orchestrator Pattern (Mẫu điều phối)

```
                    ┌─────────────────┐
     Input ───────► │  Orchestrator   │
                    │    Agent        │
                    └────────┬────────┘
                             │
           ┌─────────────────┼─────────────────┐
           ▼                 ▼                 ▼
    ┌────────────┐    ┌────────────┐    ┌────────────┐
    │  Worker    │    │  Worker    │    │  Worker    │
    │  Agent A   │    │  Agent B   │    │  Agent C   │
    └─────┬──────┘    └─────┬──────┘    └─────┬──────┘
          │                 │                 │
          └─────────────────┼─────────────────┘
                            ▼
                    ┌─────────────────┐
                    │   Aggregator    │
                    └────────┬────────┘
                             │
                         Output
```

#### Cách hoạt động
1. **Orchestrator** nhận yêu cầu → chia thành các task con
2. Giao task cho các **Worker agents** chuyên biệt
3. Worker có thể chạy **song song**
4. **Aggregator** tổng hợp kết quả

#### Ví dụ: Translation Agent
```
Input: "Dịch văn bản này sang 5 ngôn ngữ"
       ↓
Orchestrator: Chia task
       ↓
┌─────────┬─────────┬─────────┬─────────┬─────────┐
│  EN→VI  │  EN→JP  │  EN→KR  │  EN→FR  │  EN→DE  │
│ Worker  │ Worker  │ Worker  │ Worker  │ Worker  │
└────┬────┴────┬────┴────┬────┴────┬────┴────┬────┘
     └─────────┴─────────┴─────────┴─────────┘
                         ↓
                   Aggregator
                         ↓
              Output: 5 bản dịch
```

#### Use Cases
- **Coding agents**: Review → Optimize → Refactor
- **Dịch thuật đa ngôn ngữ**: Song song nhiều ngôn ngữ
- **Quyết định phức tạp**: Chia nhỏ thành quyết định con

### 2. Router Pattern (Mẫu định tuyến)

```
                    ┌─────────────────┐
     Input ───────► │     Router      │
                    └────────┬────────┘
                             │
           ┌─────────────────┼─────────────────┐
           │                 │                 │
           ▼                 ▼                 ▼
    ┌────────────┐    ┌────────────┐    ┌────────────┐
    │  Agent A   │    │  Agent B   │    │  Agent C   │
    │ (Billing)  │    │ (Support)  │    │ (Sales)    │
    └─────┬──────┘    └─────┬──────┘    └─────┬──────┘
          │                 │                 │
          └─────────────────┴─────────────────┘
                            ▼
                         Output
```

#### Cách hoạt động
1. **Router** phân tích input
2. Xác định agent phù hợp nhất
3. Chuyển tiếp đến agent đó
4. Trả về output trực tiếp

#### Ví dụ: Customer Service
```
Input: "Tôi muốn hủy tài khoản"
       ↓
Router: Phân loại → Account Cancellation
       ↓
Cancellation Agent xử lý
       ↓
Output: Hướng dẫn hủy tài khoản
```

#### Use Cases
- **Customer service**: Routing theo loại vấn đề
- **Chọn model size**: Đơn giản → small model, Phức tạp → large model
- **Intent classification**: Phân loại ý định người dùng

### 3. Parallel Pattern (Mẫu song song)

```
     Input ─────┬──────────────┬──────────────┐
                │              │              │
                ▼              ▼              ▼
         ┌──────────┐   ┌──────────┐   ┌──────────┐
         │ Worker A │   │ Worker B │   │ Worker C │
         │(Guardrail)│   │(Guardrail)│  │(Evaluator)│
         └────┬─────┘   └────┬─────┘   └────┬─────┘
              │              │              │
              └──────────────┼──────────────┘
                             ▼
                      Aggregator
                             ↓
                          Output
```

#### Cách hoạt động
- Không cần Orchestrator riêng
- Input đi thẳng đến tất cả workers
- Workers xử lý **đồng thời**
- Aggregator tổng hợp kết quả

#### Use Cases
- **Multiple guardrails**: Chạy nhiều bộ lọc song song
- **Multiple evaluations**: Đánh giá output theo nhiều tiêu chí
- **Voting**: Nhiều agent làm cùng task, vote kết quả tốt nhất

### 4. Supervisor Pattern

```
                    ┌─────────────────┐
                    │   Supervisor    │
                    │     Agent       │
                    └────────┬────────┘
                             │
                      ┌──────┴──────┐
                      │             │
                      ▼             ▼
               ┌──────────┐  ┌──────────┐
               │ Worker A │  │ Worker B │
               └──────────┘  └──────────┘
```

#### Khác với Orchestrator
- Supervisor **giám sát** và **điều chỉnh** workers
- Có thể ra quyết định khi workers conflict
- Được sử dụng trong **Agent Squad**

## Triển khai trên AWS

### 1. Bedrock Agents
- Native support cho single agent
- Kết hợp với [[Tổng quan Amazon Bedrock]] cho multi-agent

### 2. Strands Agents SDK
- Python SDK cho multi-agent
- Xem chi tiết: [[Strands Agents SDK]]

### 3. Agent Squad
- Chuyên về Router pattern
- Xem chi tiết: [[Agent Squad]]

### 4. Amazon Flows
- Visual workflow builder
- Hỗ trợ orchestration patterns

## Best Practices

### 1. Giữ đơn giản
> **Cảnh báo**: Không dùng multi-agent cho vấn đề đơn giản!

- Single agent đủ cho hầu hết use cases
- Multi-agent = Complexity + Cost cao hơn
- Chỉ dùng khi thực sự cần

### 2. Agent Design
- Mỗi agent có **mục đích rõ ràng**
- **Mô tả chi tiết** để router/orchestrator hiểu
- **Công cụ chuyên biệt** cho từng agent

### 3. Error Handling
- Xử lý khi worker fail
- Timeout cho từng agent
- Fallback strategy

### 4. Observability
- Log luồng giữa các agents
- Monitor latency từng agent
- Track token usage
- Xem: [[CloudWatch Metrics & quan sát]]

## Ví dụ: Hệ thống Customer Service

```
Customer Query
      │
      ▼
┌─────────────┐
│   Router    │ ← Intent classification
└──────┬──────┘
       │
       ├──► Billing Agent ──► "Số dư: $100"
       │
       ├──► Support Agent ──► Escalate to human
       │
       └──► Sales Agent ──► "Gói Premium: $99/tháng"
```

### Agents trong hệ thống
1. **Router**: Phân loại ý định (billing/support/sales)
2. **Billing Agent**: Truy vấn database, trả lời về số dư
3. **Support Agent**: Xử lý vấn đề kỹ thuật, escalate nếu cần
4. **Sales Agent**: Tư vấn sản phẩm, upsell

## Liên kết

- Agent cơ bản: [[LLM Agents & Agentic AI]]
- Bộ nhớ Agent: [[Agent Memory]]
- Strands SDK: [[Strands Agents SDK]]
- Agent Squad: [[Agent Squad]]
- Triển khai: [[Amazon AgentCore]]
- Workflows: [[Prompt Flows & Management]]




