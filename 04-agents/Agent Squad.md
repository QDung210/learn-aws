# Agent Squad

## 1️⃣ Câu hỏi cốt lõi

> **Agent Squad giải quyết vấn đề gì?**
> - **Router pattern**: Định tuyến thông minh đến agent phù hợp
> - **Multi-agent orchestration**: Phối hợp nhiều agents có sẵn

⚠️ Có thể dùng [[Strands Agents SDK]] thay thế. Xem: [[Strands vs Agent Squad]]

---

## Tổng quan

### Agent Squad là gì?
- **SDK mã nguồn mở** từ Amazon cho orchestration
- Tập trung vào **Router pattern** - định tuyến thông minh
- Hỗ trợ cả **Python và TypeScript**
- Tích hợp với Bedrock Agents và Flows

### Trạng thái hiện tại
> ⚠️ **Lưu ý**: Agent Squad có thể không được bảo trì tích cực. Những gì nó làm có thể được thực hiện bằng Strands hoặc Amazon Flows.

## Kiến trúc

```
                    ┌─────────────────────┐
      Input ──────► │    Classifier       │
                    │  (Intent Detection) │
                    └──────────┬──────────┘
                               │
                    ┌──────────┼──────────┐
                    │          │          │
                    ▼          ▼          ▼
             ┌──────────┐ ┌──────────┐ ┌──────────┐
             │ Agent A  │ │ Agent B  │ │ Agent C  │
             └────┬─────┘ └────┬─────┘ └────┬─────┘
                  │            │            │
                  └────────────┼────────────┘
                               │
                    ┌──────────▼──────────┐
                    │ Conversation History │
                    └──────────┬──────────┘
                               │
                           Output
```

### Thành phần chính

1. **Classifier (Bộ phân loại)**
   - Phân tích intent của input
   - Quyết định agent nào xử lý
   - Có thể sử dụng LLM hoặc rule-based

2. **Agent Pool**
   - Tập hợp các agents chuyên biệt
   - Mỗi agent có mô tả rõ ràng
   - Có thể là Bedrock Agent, Strands agent, hoặc custom

3. **Conversation History**
   - Chia sẻ context giữa các agents
   - Lưu trữ lịch sử hội thoại
   - Được đưa vào classifier cho phân loại tiếp

## Tính năng

### 1. Intent Classification
- Phân loại ý định người dùng
- Dựa trên mô tả của từng agent
- Chọn agent phù hợp nhất

### 2. Shared Context
- Lịch sử cuộc trò chuyện được chia sẻ
- Classifier sử dụng context để quyết định
- Agents nhận được context từ classifier

### 3. Built-in Agents & Classifiers
- Pre-built components để bắt đầu nhanh
- Có thể customize theo nhu cầu

### 4. Bedrock Integration
```python
from agent_squad import BedrockAgent

# Sử dụng Bedrock Agent với Agent Squad
bedrock_agent = BedrockAgent(
    agent_id="AGENT_ID",
    agent_alias_id="ALIAS_ID"
)
```

### 5. Bedrock Flows Extension
- Thêm **conversation memory** vào Flows
- Thêm **orchestration** capabilities
- Mở rộng khả năng của Flows

### 6. Supervisor Agent
- Phối hợp nhiều agents phức tạp hơn
- Gần với Strands hơn về khả năng

## Cài đặt

### Python
```bash
pip install agent-squad
```

### TypeScript
```bash
npm install agent-squad
```

## Ví dụ code

### Basic Router

```python
from agent_squad import Squad, Agent, Classifier

# Định nghĩa agents
billing_agent = Agent(
    name="billing",
    description="Xử lý các câu hỏi về hóa đơn, thanh toán, số dư",
    handler=billing_handler
)

support_agent = Agent(
    name="support", 
    description="Hỗ trợ kỹ thuật, giải quyết vấn đề",
    handler=support_handler
)

sales_agent = Agent(
    name="sales",
    description="Tư vấn sản phẩm, báo giá, upsell",
    handler=sales_handler
)

# Tạo squad với classifier
squad = Squad(
    agents=[billing_agent, support_agent, sales_agent],
    classifier=Classifier(model="bedrock/claude-3-sonnet")
)

# Xử lý request
response = squad.process("Tôi muốn kiểm tra số dư tài khoản")
# → Classifier chọn billing_agent
```

### Với Bedrock Agents

```python
from agent_squad import Squad, BedrockAgent

# Wrap existing Bedrock Agent
weather_agent = BedrockAgent(
    agent_id="WEATHER_AGENT_ID",
    agent_alias_id="PROD",
    description="Trả lời câu hỏi về thời tiết"
)

faq_agent = BedrockAgent(
    agent_id="FAQ_AGENT_ID", 
    agent_alias_id="PROD",
    description="Trả lời câu hỏi thường gặp"
)

squad = Squad(
    agents=[weather_agent, faq_agent]
)
```

### TypeScript Example

```typescript
import { Squad, Agent, Classifier } from 'agent-squad';

const billingAgent = new Agent({
    name: 'billing',
    description: 'Handle billing inquiries',
    handler: async (input) => {
        // Logic xử lý
        return 'Your balance is $100';
    }
});

const squad = new Squad({
    agents: [billingAgent],
    classifier: new Classifier({
        model: 'bedrock/claude-3-sonnet'
    })
});

const response = await squad.process('What is my balance?');
```

### Với Strands Agent

```python
from agent_squad import Squad, StrandsAgentWrapper
from strands import Agent as StrandsAgent

# Tạo Strands agent
strands_agent = StrandsAgent(
    model="bedrock/claude-3-sonnet",
    tools=["calculator", "current_time"]
)

# Wrap cho Agent Squad
# Lưu ý: Có thể cần custom wrapper do compatibility issues
wrapped_agent = StrandsAgentWrapper(
    strands_agent=strands_agent,
    description="Tính toán và cho biết thời gian"
)

squad = Squad(agents=[wrapped_agent])
```

## So sánh với Strands

| Aspect | Agent Squad | Strands |
|--------|-------------|---------|
| Focus | Router/Classifier | Tool-based Agent Loop |
| Multi-agent | ⭐⭐⭐ (core feature) | ✅ (supported) |
| Tool calling | Via wrapped agents | ⭐⭐⭐ (core feature) |
| TypeScript | ✅ | ❌ (Python only) |
| Maintenance | ⚠️ Uncertain | ✅ Active |
| Use case | Routing problems | General agent tasks |

## Khi nào dùng Agent Squad?

### ✅ Nên dùng
- Cần **TypeScript** thay vì Python
- Có **nhiều agents chuyên biệt** cần routing
- Muốn **extend Bedrock Flows** với memory/orchestration
- Đã có **Bedrock Agents** sẵn, cần orchestrate

### ❌ Không nên dùng
- Có thể làm được với Strands
- Cần tính năng tool-calling phức tạp
- Lo ngại về long-term maintenance

## Troubleshooting

### Compatibility Issues
```
Import Error: strands.agents không tìm thấy
```
- Agent Squad có thể không tương thích với phiên bản Strands mới nhất
- Kiểm tra GitHub issues để fix

### Model Deprecation
- Các models mặc định có thể bị deprecated
- Cập nhật model configuration thường xuyên

## Liên kết

- Strands SDK: [[Strands Agents SDK]]
- Multi-Agent: [[Multi-Agent Workflows]]
- AgentCore: [[Amazon AgentCore]]
- Bedrock Agents: [[LLM Agents & Agentic AI]]
- Flows: [[Prompt Flows & Management]]

