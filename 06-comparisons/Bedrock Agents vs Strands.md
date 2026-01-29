# Bedrock Agents vs Strands Agents SDK

## 1️⃣ Câu hỏi cốt lõi

> **Khi nào chọn cái nào để xây dựng Agents?**
> - **Bedrock Agents**: Managed, nhanh setup, chỉ dùng Bedrock models, ít customize
> - **[[Strands Agents SDK]]**: Open-source, full control, any LLM, 300+ tools

Xem thêm: [[LLM Agents & Agentic AI]], [[Multi-Agent Workflows]]

---

## Tổng quan nhanh

| Tiêu chí | Bedrock Agents | Strands SDK |
|----------|----------------|-------------|
| **Type** | Managed service | Open-source SDK |
| **Infrastructure** | Serverless, AWS-managed | Self-managed |
| **Flexibility** | Limited to Bedrock | Any LLM provider |
| **Tools** | Action Groups (Lambda) | 300+ built-in + custom |
| **Setup** | Console/API config | Code-first |
| **Cost model** | Pay-per-invocation | Compute + LLM costs |

---

## Bedrock Agents

### Mục đích
**Managed agentic service** - AWS quản lý toàn bộ infrastructure.
### Đặc điểm
✅ **Fully managed** - không cần quản lý server  
✅ **Console setup** - configure qua UI  
✅ **Knowledge Base integration** - RAG built-in  
✅ **Action Groups** - connect Lambda functions  
✅ **Guardrails** - content filtering  
✅ **Session management** - conversation history  

### Architecture
```
┌─────────────────────────────────────────────────────────────┐
│                    BEDROCK AGENT                             │
│                                                              │
│  User Input                                                  │
│       │                                                      │
│       ▼                                                      │
│  ┌─────────────┐     ┌─────────────┐     ┌─────────────┐   │
│  │   Agent     │ ──► │ Knowledge   │ ──► │   Action    │   │
│  │ (Orchestrate)│     │    Base     │     │   Groups    │   │
│  └─────────────┘     └─────────────┘     │  (Lambda)   │   │
│                                          └─────────────┘   │
│                              │                              │
│                              ▼                              │
│                      Bedrock FM (Claude, Llama...)          │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Limitations
- Chỉ dùng được Bedrock models
- Action Groups phải qua Lambda
- Customization limited
- Debugging khó hơn

---

## Strands Agents SDK

### Mục đích
**Open-source SDK** - full control để build agents.

### Đặc điểm
✅ **Code-first** - Python SDK  
✅ **Any LLM** - Bedrock, OpenAI, Anthropic, local  
✅ **300+ tools** - AWS, HTTP, shell, code execution  
✅ **MCP support** - Model Context Protocol  
✅ **Memory** - mem0, AgentCore  
✅ **Multi-modal** - text, voice, image, video  

### Architecture
```
┌─────────────────────────────────────────────────────────────┐
│                    STRANDS AGENT                             │
│                                                              │
│  User Input                                                  │
│       │                                                      │
│       ▼                                                      │
│  ┌─────────────┐                                            │
│  │ Agent Loop  │ ◄──── System Prompt + Tools               │
│  └──────┬──────┘                                            │
│         │                                                    │
│    ┌────┴────┐                                              │
│    ▼         ▼                                              │
│  Tools    LLM (any provider)                                │
│  • boto3                                                     │
│  • http                                                      │
│  • shell                                                     │
│  • bedrock_kb                                               │
│  • custom                                                    │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Trade-offs
- Cần self-host (EC2, Lambda, container)
- Cần manage dependencies
- More code to write
- Full debugging control

---

## So sánh chi tiết

### Setup & Development

| Aspect | Bedrock Agents | Strands |
|--------|----------------|---------|
| Setup time | Minutes (console) | Hours (code) |
| Configuration | Console/CloudFormation | Python code |
| Debugging | CloudWatch logs | Full Python debugging |
| Testing | Limited | pytest, unit tests |
| Version control | ❌ Agent config | ✅ Code in git |

### Model Support

| Provider | Bedrock Agents | Strands |
|----------|----------------|---------|
| Claude (Bedrock) | ✅ | ✅ |
| Llama (Bedrock) | ✅ | ✅ |
| Titan | ✅ | ✅ |
| OpenAI | ❌ | ✅ |
| Anthropic direct | ❌ | ✅ |
| Local models | ❌ | ✅ |
| Custom models | ❌ | ✅ |

### Tools & Actions

| Capability | Bedrock Agents | Strands |
|------------|----------------|---------|
| Tool definition | OpenAPI spec | Python decorator |
| Execution | Lambda only | Direct, any code |
| Built-in tools | ❌ | 300+ |
| AWS integration | Lambda → SDK | boto3 direct |
| MCP | ❌ | ✅ |
| HTTP calls | Lambda wrapper | http_tool |
| Shell commands | ❌ | shell_tool |

### Knowledge & Memory

| Feature | Bedrock Agents | Strands |
|---------|----------------|---------|
| RAG | ✅ KB integration | bedrock_kb tool |
| Memory | Session only | mem0, AgentCore |
| Long-term memory | ❌ | ✅ |
| Custom retrieval | ❌ | ✅ |

---

## Cost Comparison

### Bedrock Agents
```
Cost = Agent invocations + Model tokens + Lambda + KB

Example (1000 queries/day):
- Agent: $0.001/invocation = $1
- Claude tokens: ~$5-20
- Lambda: minimal
- KB: storage + queries

Total: ~$10-25/day
```

### Strands
```
Cost = Compute (host) + Model tokens

Example (1000 queries/day):
- EC2/Lambda: $1-5/day
- Model tokens: ~$5-20 (same)

Total: ~$6-25/day

+ Development time cost
```

---

## Decision Matrix

| Scenario | Choice |
|----------|--------|
| Quick prototype | Bedrock Agents |
| No infrastructure management | Bedrock Agents |
| Simple RAG + actions | Bedrock Agents |
| Need OpenAI/other models | Strands |
| Complex tool chains | Strands |
| Need MCP | Strands |
| Full debugging control | Strands |
| Long-term memory | Strands |
| Multi-modal processing | Strands |
| Production, minimal code | Bedrock Agents |
| Production, full control | Strands |

---

## Hybrid Approach

```
┌─────────────────────────────────────────────────────────────┐
│              USE BOTH                                        │
│                                                              │
│  Bedrock Agents:                                             │
│  • Simple customer-facing agents                             │
│  • Standard RAG + action patterns                            │
│  • When speed-to-deploy matters                              │
│                                                              │
│  Strands:                                                    │
│  • Complex internal tools                                    │
│  • Multi-model orchestration                                 │
│  • Custom memory/context needs                               │
│  • Integration testing & CI/CD                               │
│                                                              │
│  Strands can USE Bedrock:                                    │
│  - strands.tools.bedrock_kb (call KB)                       │
│  - strands with model="bedrock/claude-3"                    │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## Xem thêm
- [[Strands Agents SDK]]
- [[LLM Agents & Agentic AI]]
- [[Runtime API, Agent, Knowledge Base]]
- [[Hands On Bedrock Agents]]
