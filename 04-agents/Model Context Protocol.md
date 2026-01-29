# Model Context Protocol (MCP)

## 1️⃣ Câu hỏi cốt lõi

> **MCP giải quyết vấn đề gì?**
> - **Giao thức chuẩn** cho AI Agents tương tác với Tools/Services
> - Ví như **"USB-C cho AI Apps"** - một interface chuẩn
> - Do **Anthropic** phát triển

Xem thêm: [[Strands Agents SDK]], [[OpenAPI & Tool Usage]]

---

## Tổng quan

### MCP là gì?
- **Giao thức chuẩn** cho tương tác giữa AI Agents và Tools/Services
- Do **Anthropic** phát triển
- Được ví như **"USB-C cho AI Apps"** - một interface chuẩn để kết nối mọi thứ

### Mục đích
```
┌─────────────────┐          ┌─────────────────┐
│    AI Agent     │   MCP    │  External Tool  │
│  (any SDK)      │ ◄──────► │  (any service)  │
└─────────────────┘          └─────────────────┘

Standardized interface → Interoperability
```

## Kiến trúc

```
┌─────────────────────────────────────────────────────────┐
│                      AI Application                      │
└────────────────────────────┬────────────────────────────┘
                             │
                             ▼
┌─────────────────────────────────────────────────────────┐
│                       MCP Layer                          │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐     │
│  │   Tools     │  │  Resources  │  │   Prompts   │     │
│  └─────────────┘  └─────────────┘  └─────────────┘     │
└─────────────────────────────────────────────────────────┘
                             │
           ┌─────────────────┼─────────────────┐
           │                 │                 │
           ▼                 ▼                 ▼
    ┌────────────┐    ┌────────────┐    ┌────────────┐
    │  Local     │    │  External  │    │  Dev       │
    │  Data      │    │  APIs      │    │  Tools     │
    │  (Files,   │    │  (GitHub,  │    │  (IDE,     │
    │  Database) │    │  Slack)    │    │  Terminal) │
    └────────────┘    └────────────┘    └────────────┘
```

### MCP Server Capabilities

1. **Tools**: Các functions agent có thể gọi
2. **Resources**: Dữ liệu/context bổ sung
3. **Prompts**: Hướng dẫn sử dụng MCP server

## Technical Details

### Transport Layer
| Type | Protocol | Use Case |
|------|----------|----------|
| Local | stdio (Standard I/O) | Local tools, files |
| Remote | HTTP Streaming | External APIs, cloud services |

### Data Layer
- **JSON-RPC 2.0** format
- Standardized request/response structure

## Ví dụ MCP Servers phổ biến

### GitHub MCP
```python
# Agent có thể:
# - Tương tác với repositories
# - Đọc/tạo issues
# - Check commits
# - Manage PRs
```

### Atlassian MCP (Jira)
```python
# Agent có thể:
# - Đọc JIRA tickets
# - Tạo issues
# - Update status
# - Query projects
```

### PostgreSQL MCP
```python
# Agent có thể:
# - Query database
# - Get schema info
# - Execute SQL (với permissions)
```

### Slack MCP
```python
# Agent có thể:
# - Send messages
# - Read channels
# - Manage notifications
```

### Google Maps MCP
```python
# Agent có thể:
# - Get directions
# - Search places
# - Calculate distances
```

### Udemy MCP
```python
# Agent có thể:
# - Lấy thông tin khóa học
# - Recommend learning paths
```

## Triển khai MCP Server trên AWS

### Hướng dẫn chọn service

```
┌─────────────────────────────────────────────────────────┐
│           Bạn cần gì cho MCP Server?                     │
└─────────────────────────────────────────────────────────┘
                         │
        ┌────────────────┴────────────────┐
        │                                 │
   Stateless,                      Complex,
   Lightweight                     Stateful
        │                                 │
        ▼                                 ▼
┌─────────────────┐              ┌─────────────────┐
│  AWS Lambda     │              │  Amazon ECS/    │
│                 │              │  Fargate        │
│  • Serverless   │              │  • Containers   │
│  • Pay-per-use  │              │  • Scalable     │
│  • Simple tools │              │  • Complex logic│
└─────────────────┘              └─────────────────┘
                                          │
                                          ▼
                                 ┌─────────────────┐
                                 │  AgentCore      │
                                 │  Deploy         │
                                 │  (recommended)  │
                                 └─────────────────┘
```

### Kiến trúc tham khảo AWS

```
                     ┌─────────────┐
                     │   WAF       │ ← Protection
                     └──────┬──────┘
                            │
                     ┌──────▼──────┐
                     │ CloudFront  │ ← CDN/Scaling
                     └──────┬──────┘
                            │
                     ┌──────▼──────┐
                     │ API Gateway │ ← Entry point
                     └──────┬──────┘
                            │
           ┌────────────────┼────────────────┐
           │                │                │
    ┌──────▼──────┐  ┌──────▼──────┐  ┌──────▼──────┐
    │   Lambda    │  │    ECS      │  │  AgentCore  │
    │  (simple)   │  │  (complex)  │  │  (managed)  │
    └──────┬──────┘  └──────┬──────┘  └──────┬──────┘
           │                │                │
           └────────────────┼────────────────┘
                            │
    ┌───────────────────────┼───────────────────────┐
    │                       │                        │
┌───▼────┐          ┌───────▼──────┐         ┌──────▼─────┐
│ Cognito│          │   DynamoDB   │         │ CloudWatch │
│ (auth) │          │  (storage)   │         │  (logs)    │
└────────┘          └──────────────┘         └────────────┘
```

### Lambda cho MCP Server (đơn giản)

```python
# lambda_function.py
import json

def lambda_handler(event, context):
    """MCP Server đơn giản cho weather data"""
    
    method = event.get('method')
    params = event.get('params', {})
    
    if method == 'tools/list':
        return {
            'tools': [
                {
                    'name': 'get_weather',
                    'description': 'Get current weather for a city',
                    'inputSchema': {
                        'type': 'object',
                        'properties': {
                            'city': {'type': 'string'}
                        },
                        'required': ['city']
                    }
                }
            ]
        }
    
    elif method == 'tools/call':
        tool_name = params.get('name')
        args = params.get('arguments', {})
        
        if tool_name == 'get_weather':
            city = args.get('city')
            # Mock data
            return {
                'content': [
                    {'type': 'text', 'text': f'Weather in {city}: 25°C, Sunny'}
                ]
            }
    
    return {'error': 'Unknown method'}
```

### ECS/AgentCore cho MCP Server (phức tạp)

```python
# Sử dụng MCP Python SDK
from mcp import Server, Tool
import asyncio

server = Server("weather-mcp")

@server.tool()
async def get_weather(city: str, units: str = "C") -> str:
    """Get current weather for a city."""
    # Complex logic với state, caching, etc.
    weather_data = await fetch_weather_api(city, units)
    return format_weather(weather_data)

@server.tool()
async def get_forecast(city: str, days: int = 7) -> str:
    """Get weather forecast."""
    forecast = await fetch_forecast_api(city, days)
    return format_forecast(forecast)

if __name__ == "__main__":
    asyncio.run(server.run())
```

## Tích hợp MCP với Strands

```python
from strands import Agent
from strands.mcp import MCPClient

# Kết nối MCP servers
github_mcp = MCPClient("http://github-mcp:8080")
jira_mcp = MCPClient("http://jira-mcp:8080")
weather_mcp = MCPClient("http://weather-mcp:8080")

# Agent sử dụng tools từ MCP servers
agent = Agent(
    model="bedrock/claude-3-sonnet",
    mcp_clients=[github_mcp, jira_mcp, weather_mcp]
)

# Agent tự động có access đến tất cả MCP tools
response = agent("""
1. Check latest commits on repo X
2. Create JIRA ticket for bug
3. What's the weather in Hanoi?
""")
```

## Best Practices

### 1. Sử dụng MCP Client Libraries
- Không tự implement protocol
- Sử dụng official SDKs:
  ```bash
  pip install mcp
  npm install @modelcontextprotocol/sdk
  ```

### 2. Chọn đúng deployment
| Criteria | Lambda | ECS/AgentCore |
|----------|--------|---------------|
| Stateless | ✅ | ✅ |
| Stateful | ❌ | ✅ |
| Cold start OK | ✅ | ❌ |
| Complex logic | ❌ | ✅ |
| Cost | Lower | Higher |

### 3. Security
- Authenticate MCP requests
- Validate inputs
- Limit permissions (least privilege)
- Use API Gateway + WAF

### 4. Observability
- Log all MCP calls
- Monitor latency
- Track errors

## Liên kết

- Agents cơ bản: [[LLM Agents & Agentic AI]]
- Strands SDK: [[Strands Agents SDK]]
- AgentCore: [[Amazon AgentCore]]
- OpenAPI: [[OpenAPI & Tool Usage]]


