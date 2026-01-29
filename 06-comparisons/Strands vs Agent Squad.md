# Strands Agents SDK vs Agent Squad

## 1️⃣ Câu hỏi cốt lõi

> **Khi nào chọn cái nào cho Multi-Agent?**
> - **[[Strands Agents SDK]]**: Build agents từ đầu với full features
> - **[[Agent Squad]]**: Routing/orchestration nhiều agents có sẵn

Xem thêm: [[Multi-Agent Workflows]], [[LLM Agents & Agentic AI]]

---

## Tổng quan nhanh

| Tiêu chí | Strands | Agent Squad |
|----------|---------|-------------|
| **Focus** | Agent building (full-featured) | Agent routing/orchestration |
| **Pattern** | Agent Loop (tool execution) | Router Pattern (intent routing) |
| **Use Case** | Xây dựng agents từ đầu | Điều phối nhiều agents |
| **Complexity** | Nhiều features | Đơn giản, tập trung |
| **Status** | Active development | ⚠️ Có thể không maintained |
| **Language** | Python | Python + TypeScript |

---

## Strands Agents SDK

### Mục đích
SDK toàn diện để **xây dựng AI agents** với đầy đủ tính năng.

### Kiến trúc: Agent Loop
```
Input → Tool Selection → Tool Execution → Model Check → Output
              ↑                               │
              └───────── (chưa xong) ─────────┘
```

### Đặc điểm chính
- **Single & Multi-agent** systems
- **300+ tools** tích hợp sẵn
- **MCP support** (Model Context Protocol)
- **Multi-modal**: text, voice, image, video
- **Memory**: short-term & long-term
- **AWS integration**: boto3, Bedrock KB, Lambda

### Khi nào dùng?
✅ Xây dựng agent phức tạp với nhiều tools  
✅ Cần memory, context management  
✅ Multi-modal processing  
✅ Tích hợp sâu với AWS  

---

## Agent Squad

### Mục đích
SDK tập trung vào **routing/orchestration** - điều phối nhiều agents.

### Kiến trúc: Router Pattern
```
Input → Classifier → Agent A/B/C → Output
              ↑           │
              └─ History ─┘
```

### Đặc điểm chính
- **Intent Classification**: Phân loại ý định
- **Agent Pool**: Nhiều agents chuyên biệt
- **Shared Context**: Chia sẻ history giữa agents
- **Bedrock Integration**: Dùng Bedrock Agents

### Khi nào dùng?
✅ Có sẵn nhiều agents, cần router  
✅ Pattern đơn giản: classify → route  
✅ Cần TypeScript support  

---

## So sánh chi tiết

### Building vs Routing

| Aspect | Strands | Agent Squad |
|--------|---------|-------------|
| Tạo agent mới | ✅ Full support | ❌ Dùng agent có sẵn |
| Tool execution | ✅ Built-in loop | ❌ Delegate to agent |
| Intent routing | ✅ Có thể làm | ✅ Focus chính |
| Memory | ✅ mem0, AgentCore | ❌ Chỉ conversation |

### Tool Ecosystem

**Strands:**
```python
# 300+ built-in tools
from strands import Agent
from strands.tools import boto3_tool, http_tool, shell_tool

agent = Agent(
    model="bedrock/claude-3",
    tools=[boto3_tool, http_tool, shell_tool]
)
```

**Agent Squad:**
```python
# Sử dụng agents có sẵn
from agent_squad import BedrockAgent, Classifier

classifier = Classifier(model="claude-3")
agents = [
    BedrockAgent(agent_id="SALES_AGENT"),
    BedrockAgent(agent_id="SUPPORT_AGENT")
]
```

### Multi-Agent Patterns

**Strands - Build complex agents:**
```
┌─────────────────────────────────────────────────────────────┐
│  Strands: Build each agent with full capabilities           │
│                                                              │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐              │
│  │ Agent 1  │    │ Agent 2  │    │ Agent 3  │              │
│  │ ──────── │    │ ──────── │    │ ──────── │              │
│  │ Tools    │    │ Tools    │    │ Tools    │              │
│  │ Memory   │    │ Memory   │    │ Memory   │              │
│  │ MCP      │    │ MCP      │    │ MCP      │              │
│  └──────────┘    └──────────┘    └──────────┘              │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

**Agent Squad - Route between existing agents:**
```
┌─────────────────────────────────────────────────────────────┐
│  Agent Squad: Route to appropriate agent                     │
│                                                              │
│              ┌─────────────┐                                │
│  Input ────► │  Classifier │                                │
│              └──────┬──────┘                                │
│                     │                                        │
│         ┌──────────┼──────────┐                             │
│         ▼          ▼          ▼                             │
│    Bedrock A   Bedrock B   Custom C                         │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## Decision Tree

```
Cần xây agent từ đầu với tools?     → Strands
Cần memory, MCP, multi-modal?       → Strands
Cần TypeScript?                     → Agent Squad (hoặc Strands TS)

Có sẵn Bedrock Agents, cần router?  → Agent Squad
Pattern đơn giản classify-route?    → Agent Squad

Dự án production, cần maintained?   → Strands (active)
```

---

## Recommendation

### 2024+ Best Practice

```
┌─────────────────────────────────────────────────────────────┐
│              RECOMMENDED APPROACH                            │
│                                                              │
│  1. Use Strands to BUILD individual agents                  │
│  2. Use Strands multi-agent features for orchestration      │
│  3. Or use Amazon Bedrock Flows for visual orchestration    │
│                                                              │
│  ⚠️ Agent Squad: Consider alternatives as it may not be     │
│     actively maintained. Strands can do what it does.       │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## Xem thêm
- [[Strands Agents SDK]]
- [[Agent Squad]]
- [[Multi-Agent Workflows]]
- [[LLM Agents & Agentic AI]]
