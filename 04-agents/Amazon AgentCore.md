# Amazon AgentCore

## 1️⃣ Câu hỏi cốt lõi

> **AgentCore giải quyết vấn đề gì?**
> - **Deployment**: Triển khai AI Agents ở quy mô lớn (serverless)
> - **Operations**: Auto-scaling, Auth, Memory, Monitoring
> - **Framework agnostic**: Hoạt động với bất kỳ agent framework nào

Tích hợp tốt nhất với [[Strands Agents SDK]]. Xem thêm: [[Agent Memory]]

---

## Tổng quan

### AgentCore là gì?
- Dịch vụ **triển khai và vận hành AI Agents** ở quy mô lớn
- **Serverless** - không cần quản lý infrastructure
- Hoạt động với **bất kỳ agent framework nào** (Strands, OpenAI SDK, LangGraph, CrewAI...)
- Tích hợp tốt nhất với **Strands SDK**

### Vấn đề AgentCore giải quyết
```
Local Development                    Production
┌─────────────────┐                 ┌─────────────────────────────┐
│ strands agent   │ ──────────────► │ Docker? ECS? EKS?           │
│ running locally │   How to        │ Scaling? Auth? Memory?      │
│ works great!    │   deploy?       │ Monitoring? Credentials?    │
└─────────────────┘                 └─────────────────────────────┘
                                                 │
                                    AgentCore giải quyết tất cả!
                                                 │
                                    ┌─────────────────────────────┐
                                    │ agentcore deploy            │
                                    │ → Serverless endpoints      │
                                    │ → Auto-scaling              │
                                    │ → Memory management         │
                                    │ → Auth integration          │
                                    └─────────────────────────────┘
```

## Kiến trúc AgentCore

```
┌─────────────────────────────────────────────────────────────────┐
│                        AGENTCORE                                 │
│                                                                  │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐              │
│  │   Agent     │  │   Memory    │  │   Gateway   │              │
│  │  Runtime    │  │   System    │  │  (Tools)    │              │
│  └─────────────┘  └─────────────┘  └─────────────┘              │
│         │               │                │                       │
│         │    ┌──────────┴───────────────┘                       │
│         │    │                                                   │
│  ┌──────┴────┴──────┐  ┌─────────────┐  ┌─────────────┐        │
│  │    Identity      │  │   Bedrock   │  │ Observability│        │
│  │   Management     │  │   Import    │  │ (CloudWatch) │        │
│  └──────────────────┘  └─────────────┘  └─────────────┘        │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

## Thành phần chính

### 1. Agent Runtime

#### Serverless Endpoints
- Không cần quản lý servers
- Tự động scale theo traffic
- Pay-per-use pricing

#### Deployment Options
```bash
# Sử dụng Starter Toolkit (đơn giản)
agentcore configure
agentcore deploy

# Custom Docker (advanced)
# Build Docker image → Push to ECR → Deploy to ECS
```

#### Hỗ trợ nhiều Framework
| Framework | Support Level |
|-----------|--------------|
| Strands | ⭐⭐⭐ Best |
| OpenAI SDK | ✅ |
| LangGraph | ✅ |
| CrewAI | ✅ |
| Custom | ✅ (provide invoke function) |

### 2. Memory System

#### Short-term Memory
- Lịch sử cuộc trò chuyện trong session
- API focus: **Sessions** và **Events**

```python
from agentcore.memory import Session

session = Session.create(user_id="user123")
session.add_event({"type": "message", "content": "Hello"})
history = session.get_events()
```

#### Long-term Memory
- Persist qua nhiều sessions
- **Memory Profiles** với các strategies:
  - User Preferences
  - Semantic Facts  
  - Session Summaries

```python
from agentcore.memory import MemoryProfile

profile = MemoryProfile(user_id="user123")

# User Preferences strategy
profile.set_preference("language", "vi")

# Semantic Facts strategy
profile.add_fact("User is a Python developer")

# Session Summary strategy
profile.add_summary(session_id="s1", summary="Discussed AI")
```

#### Scalable Storage
- **SQLite không đủ** cho production
- AgentCore sử dụng **managed, distributed storage**
- Không cần lo về infrastructure

### 3. Gateway (Quản lý Tools)

#### Mục đích
- Quản lý kết nối đến **external tools**
- **Centralized credentials** và authorization
- Biến mọi thứ thành **MCP-like interface**

#### Targets hỗ trợ
- OpenAPI specifications
- Smithy models
- Lambda functions
- External APIs

#### Semantic Tool Selection
- Gateway hỗ trợ chọn tool phù hợp
- Không chỉ routing, còn understand intent

### 4. Identity Management

> **Quan trọng**: Đây là identity của **Agent**, không phải User!

#### Agent Identity
```
User Identity (Cognito)     vs    Agent Identity (AgentCore)
         │                                   │
    Người dùng login              Agent kết nối external tools
    vào ứng dụng                  với credentials riêng
```

#### Tính năng
- Centralized identity store cho agents
- Secure credential storage
- OAuth 2.0 support
- Built-in integrations: Google, GitHub, Slack, Salesforce, Atlassian

### 5. Bedrock Import

```bash
# Import Bedrock Agent vào AgentCore
agentcore import agent --agent-id AGENT_ID --output ./my-agent

# Output: Strands SDK code (hoặc LangGraph)
```

#### Workflow
1. Có Bedrock Agent sẵn
2. Import vào AgentCore → Sinh ra Strands code
3. Customize/extend code
4. Deploy với AgentCore

### 6. Observability

- Tích hợp **CloudWatch** tự động
- **Gen-AI specific metrics**
- Dashboard cho monitoring
- Trace agent interactions

Xem: [[CloudWatch Metrics & quan sát]]

## Starter Toolkit

### Commands

```bash
# Cài đặt
pip install bedrock-agentcore-tools

# Cấu hình
agentcore configure
# → Hướng dẫn qua các lựa chọn:
#   - Deployment method
#   - Region
#   - Authentication
#   - etc.

# Triển khai
agentcore deploy
# → Build container
# → Push to ECR
# → Deploy to ECS
# → Setup endpoints

# Phá hủy (quan trọng!)
agentcore destroy
# → Cleanup tất cả resources
```

### Under the hood
- **CodeBuild** để build containers
- **ECR** để lưu images
- **ECS/Fargate** để run containers
- Tự động setup networking, security, etc.

## Built-in Tools

### Code Interpreter
- Chạy code trong **sandboxed container**
- Hỗ trợ: Python, JavaScript, TypeScript
- An toàn và isolated

### Browser Tool
- Điều khiển browser để tương tác với web
- Automation capabilities

## Ví dụ tích hợp Strands + AgentCore

```python
# agent.py
from strands import Agent
from agentcore.memory import Memory
from agentcore.identity import Identity

# Tạo agent với AgentCore memory
agent = Agent(
    model="bedrock/claude-3-sonnet",
    tools=["calculator", "current_time"],
    memory=Memory(provider="agentcore"),
    identity=Identity(provider="agentcore")
)

def invoke(input_text: str, session_id: str):
    """Entry point cho AgentCore"""
    return agent(input_text, session_id=session_id)

if __name__ == "__main__":
    # Local testing
    print(invoke("What time is it?", "test-session"))
```

```bash
# Deploy
agentcore deploy --entry invoke --file agent.py
```

## Best Practices

### 1. Sử dụng Starter Toolkit
- Đơn giản nhất cho hầu hết use cases
- Không cần biết Docker/ECS

### 2. Cleanup Resources
```bash
# LUÔN chạy sau khi xong
agentcore destroy
```

### 3. Region Consistency
- Đảm bảo tất cả resources cùng region
- Bedrock, AgentCore, Lambda, etc.

### 4. Monitoring
- Setup CloudWatch alerts
- Monitor costs và usage

### 5. Development vs Production
```bash
# Development
agentcore configure --env dev

# Production  
agentcore configure --env prod --enable-auth
```

## Liên kết

- Strands SDK: [[Strands Agents SDK]]
- Memory: [[Agent Memory]]
- Multi-Agent: [[Multi-Agent Workflows]]
- MCP: [[Model Context Protocol]]
- Monitoring: [[CloudWatch Metrics & quan sát]]
- Bedrock: [[Tổng quan Amazon Bedrock]]



