# Strands Agents SDK

## 1️⃣ Câu hỏi cốt lõi

> **Strands giải quyết vấn đề gì?**
> - **SDK mã nguồn mở** từ Amazon để xây dựng AI Agents
> - **Agent Loop pattern**: Tool Selection → Tool Execution → Model Check
> - Tích hợp 300+ tools, hỗ trợ [[Model Context Protocol|MCP]]

Xem thêm: [[LLM Agents & Agentic AI]], [[Amazon AgentCore]], [[Bedrock Agents vs Strands]]

---

## Tổng quan

### Strands là gì?
- **SDK mã nguồn mở** từ Amazon cho việc xây dựng AI Agents
- Một trong nhiều SDK: OpenAI Agents SDK, CrewAI, LangGraph, Google ADK
- **Tích hợp AWS chặt chẽ** nhưng không bắt buộc

### Đặc điểm
- Hỗ trợ **single và multi-agent** systems
- Xử lý **đa phương tiện**: text, voice, image, video
- Hỗ trợ **MCP** (Model Context Protocol)
- **Bộ công cụ tích hợp** phong phú
- Có thể dùng với **OpenAI, Anthropic** hoặc các model khác

## Cài đặt

```bash
pip install strands-agents
pip install boto3  # Cho AWS integration
```

## Kiến trúc: Agent Loop

```
┌─────────────────────────────────────────────────────────┐
│                     STRANDS AGENT LOOP                   │
│                                                          │
│   Input + Context                                        │
│        │                                                 │
│        ▼                                                 │
│   ┌──────────────┐                                      │
│   │ Tool         │ ◄──── "Tôi cần gọi công cụ nào?"     │
│   │ Selection    │                                      │
│   └──────┬───────┘                                      │
│          │                                              │
│          ▼                                              │
│   ┌──────────────┐                                      │
│   │ Tool         │ ◄──── Thực thi công cụ đã chọn       │
│   │ Execution    │                                      │
│   └──────┬───────┘                                      │
│          │                                              │
│          ▼                                              │
│   ┌──────────────┐                                      │
│   │ Foundation   │ ◄──── "Tôi đã xong chưa?"            │
│   │ Model Check  │                                      │
│   └──────┬───────┘                                      │
│          │                                              │
│     ┌────┴────┐                                         │
│     │         │                                         │
│   Chưa      Xong                                        │
│     │         │                                         │
│     │         ▼                                         │
│     │   Final Response                                  │
│     │                                                   │
│     └──────► Quay lại Tool Selection                    │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

## Công cụ tích hợp (Built-in Tools)

### AWS Integration
| Tool | Mô tả |
|------|-------|
| `boto3` | Full AWS SDK access |
| `bedrock_kb` | Bedrock Knowledge Bases |
| `lambda_invoke` | Gọi Lambda functions |
| `step_functions` | AWS Step Functions |

### Memory & Personalization
| Tool | Mô tả |
|------|-------|
| `mem0` | Long/short-term memory |
| `agentcore_memory` | AgentCore memory integration |

### Code Execution
| Tool | Mô tả |
|------|-------|
| `python_exec` | Chạy Python code |
| `calculator` | Tính toán |
| `shell` | Chạy shell commands |

### External
| Tool | Mô tả |
|------|-------|
| `http` | HTTP requests |
| `file` | File manipulation |
| `current_time` | Lấy thời gian hiện tại |

### Multi-modal
| Tool | Mô tả |
|------|-------|
| `polly` | Text-to-speech (Amazon Polly) |
| `image_gen` | Image generation |

## Ví dụ code

### Basic Agent

```python
from strands import Agent, tool

# Định nghĩa custom tool
@tool
def count_letters(word: str, letter: str) -> int:
    """Đếm số lần xuất hiện của một chữ cái trong từ."""
    return word.lower().count(letter.lower())

# Tạo agent với tools
agent = Agent(
    model="bedrock/claude-3-sonnet",
    tools=[
        count_letters,      # Custom tool
        "calculator",       # Built-in
        "current_time",     # Built-in
        "python_exec"       # Built-in
    ]
)

# Invoke agent
response = agent("Có bao nhiêu chữ 'r' trong từ 'strawberry'?")
print(response)
# Output: "Có 3 chữ 'r' trong từ 'strawberry'"
```

### Multi-step Task

```python
from strands import Agent

agent = Agent(
    model="bedrock/claude-3-sonnet",
    tools=["calculator", "current_time", "python_exec"]
)

# Agent sẽ tự động lặp và gọi nhiều tools
response = agent("""
1. Bây giờ là mấy giờ?
2. Tính 15 * 24
3. Viết code Python in "Hello World"
""")

# Agent loop sẽ:
# 1. Gọi current_time → "14:30"
# 2. Gọi calculator → "360"  
# 3. Gọi python_exec → "Hello World"
# 4. Tổng hợp kết quả
```

### Agent với Knowledge Base

```python
from strands import Agent
from strands.tools.aws import bedrock_kb

agent = Agent(
    model="bedrock/claude-3-sonnet",
    tools=[
        bedrock_kb(knowledge_base_id="KB_ID_HERE")
    ],
    system_prompt="Trả lời câu hỏi dựa trên Knowledge Base."
)

response = agent("Làm sao để bắt đầu kinh doanh?")
```

### Agent với Memory

```python
from strands import Agent
from strands.tools.memory import mem0

agent = Agent(
    model="bedrock/claude-3-sonnet",
    tools=[mem0()],
    memory_enabled=True
)

# Session 1
agent("Tôi là developer Python, thích async programming")

# Session 2 (nhớ context từ session 1)
agent("Gợi ý project nào phù hợp với tôi?")
# → Agent nhớ user là Python dev, thích async
```

### MCP Integration

```python
from strands import Agent
from strands.mcp import MCPClient

# Kết nối MCP server
mcp_client = MCPClient("http://mcp-server:8080")

agent = Agent(
    model="bedrock/claude-3-sonnet",
    mcp_clients=[mcp_client]  # Tools từ MCP server
)

response = agent("Lấy thông tin từ GitHub repo")
```

## Agent Orchestration

### Supervisor Agent

```python
from strands import Agent, Supervisor

# Các worker agents
code_reviewer = Agent(
    name="reviewer",
    model="bedrock/claude-3-sonnet",
    tools=["python_exec"],
    description="Review code quality"
)

optimizer = Agent(
    name="optimizer", 
    model="bedrock/claude-3-sonnet",
    tools=["python_exec"],
    description="Optimize code performance"
)

# Supervisor điều phối
supervisor = Supervisor(
    agents=[code_reviewer, optimizer],
    model="bedrock/claude-3-sonnet"
)

result = supervisor("Review và optimize code này: ...")
```

## So sánh với các SDK khác

| Feature | Strands | OpenAI SDK | CrewAI | LangGraph |
|---------|---------|------------|--------|-----------|
| AWS Integration | ⭐⭐⭐ | ⭐ | ⭐ | ⭐⭐ |
| Multi-model | ✅ | ❌ (OpenAI only) | ✅ | ✅ |
| MCP Support | ✅ | ✅ | ❌ | ✅ |
| Memory | ✅ | ✅ | ✅ | ✅ |
| Multi-agent | ✅ | ✅ | ⭐⭐⭐ | ⭐⭐⭐ |
| AgentCore | ⭐⭐⭐ | ❌ | ❌ | ❌ |

## Best Practices

### 1. Tool Descriptions
```python
@tool
def get_weather(city: str, units: str = "C") -> str:
    """
    Lấy thông tin thời tiết hiện tại cho một thành phố.
    
    Args:
        city: Tên thành phố (ví dụ: "Hanoi", "Ho Chi Minh")
        units: Đơn vị nhiệt độ, "C" cho Celsius, "F" cho Fahrenheit
    
    Returns:
        Chuỗi mô tả thời tiết hiện tại
    """
    # Implementation
    pass
```

### 2. Error Handling
```python
@tool
def risky_operation(data: str) -> str:
    """Thao tác có thể fail."""
    try:
        result = do_something(data)
        return result
    except Exception as e:
        return f"Lỗi: {str(e)}. Vui lòng thử cách khác."
```

### 3. Streaming Response
```python
agent = Agent(
    model="bedrock/claude-3-sonnet",
    streaming=True
)

for chunk in agent.stream("Giải thích quantum computing"):
    print(chunk, end="", flush=True)
```

### 4. Triển khai với AgentCore
```bash
# Cấu hình
agentcore configure

# Triển khai
agentcore deploy
```

Xem chi tiết: [[Amazon AgentCore]]

## Liên kết

- Agent cơ bản: [[LLM Agents & Agentic AI]]
- Multi-Agent: [[Multi-Agent Workflows]]
- Memory: [[Agent Memory]]
- AgentCore: [[Amazon AgentCore]]
- Agent Squad: [[Agent Squad]]
- MCP: [[Model Context Protocol]]
- Bedrock: [[Tổng quan Amazon Bedrock]]




