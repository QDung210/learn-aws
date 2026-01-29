# OpenAPI & Tool Usage

## 1️⃣ Câu hỏi cốt lõi

> **OpenAPI giúp AI Agents như thế nào?**
> - **Specification** định nghĩa giao diện tools chính xác
> - Giúp LLM gọi tools một cách **đáng tin cậy**
> - (Không phải OpenAI!)

Xem thêm: [[Model Context Protocol]], [[Strands Agents SDK]], [[LLM Agents & Agentic AI]]

---

## Tổng quan

### OpenAPI là gì?
- **Specification** để định nghĩa giao diện giữa web services
- Trước đây gọi là **Swagger**
- Format: YAML hoặc JSON
- **Không phải** OpenAI!

### Vai trò trong AI Agents
- Định nghĩa **chính xác** interface của tools
- Giúp LLM gọi tools một cách **đáng tin cậy**
- Standardized format cho tất cả frameworks

## OpenAPI Specification

### Cấu trúc cơ bản

```yaml
openapi: 3.0.0
info:
  title: Calculator API
  version: 1.0.0
  description: API để thực hiện các phép tính

paths:
  /calculate:
    post:
      operationId: calculate
      summary: Thực hiện phép tính
      description: Tính toán dựa trên operator và operands
      
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required:
                - operation
                - a
                - b
              properties:
                operation:
                  type: string
                  enum: [add, subtract, multiply, divide]
                  description: Phép tính cần thực hiện
                a:
                  type: number
                  description: Số thứ nhất
                b:
                  type: number
                  description: Số thứ hai
      
      responses:
        '200':
          description: Kết quả phép tính
          content:
            application/json:
              schema:
                type: object
                properties:
                  result:
                    type: number
                    description: Kết quả của phép tính
        '400':
          description: Lỗi input không hợp lệ
```

### Các thành phần quan trọng

| Thành phần    | Mô tả                    | Tầm quan trọng        |
| ------------- | ------------------------ | --------------------- |
| `operationId` | Tên unique của operation | LLM dùng để gọi       |
| `description` | Mô tả chức năng          | LLM hiểu khi nào dùng |
| `parameters`  | Input parameters         | LLM biết truyền gì    |
| `responses`   | Output format            | LLM xử lý kết quả     |
| `required`    | Bắt buộc hay không       | LLM validate input    |

## Sử dụng với Bedrock Action Groups

### Workflow

```
┌─────────────────────────────────────────────────────────┐
│                   Bedrock Agent                          │
│                                                          │
│   User Prompt: "Tính 15 x 24"                           │
│         │                                                │
│         ▼                                                │
│   ┌─────────────────┐                                   │
│   │  Parse & Match  │ ← OpenAPI spec guides LLM         │
│   │  Tool Selection │                                   │
│   └────────┬────────┘                                   │
│            │                                             │
│            ▼                                             │
│   Action Group: Calculator                               │
│   - operation: "multiply"                                │
│   - a: 15                                                │
│   - b: 24                                                │
│            │                                             │
│            ▼                                             │
│   Lambda Function                                        │
│   - Returns: {result: 360}                              │
│            │                                             │
│            ▼                                             │
│   Response: "15 x 24 = 360"                             │
└─────────────────────────────────────────────────────────┘
```

### Upload OpenAPI Schema

**Cách 1: Upload lên S3**
```bash
# Upload schema file
aws s3 cp calculator-api.yaml s3://my-bucket/schemas/

# Reference trong Action Group
# S3 URI: s3://my-bucket/schemas/calculator-api.yaml
```

**Cách 2: Paste trực tiếp trong Console**
- Bedrock Console → Agent → Action Group
- Chọn "Define with OpenAPI schema"
- Paste YAML/JSON content

### Ví dụ Weather API

```yaml
openapi: 3.0.0
info:
  title: Weather API
  version: 1.0.0
  description: Lấy thông tin thời tiết

paths:
  /weather:
    get:
      operationId: getWeather
      summary: Lấy thời tiết hiện tại
      description: >
        Sử dụng tool này khi người dùng hỏi về thời tiết
        của một thành phố bất kỳ.
      
      parameters:
        - name: city
          in: query
          required: true
          description: Tên thành phố (VD: Hanoi, Tokyo)
          schema:
            type: string
        
        - name: units
          in: query
          required: false
          description: >
            Đơn vị nhiệt độ. 
            "C" cho Celsius (mặc định), 
            "F" cho Fahrenheit
          schema:
            type: string
            enum: [C, F]
            default: C
      
      responses:
        '200':
          description: Thông tin thời tiết
          content:
            application/json:
              schema:
                type: object
                properties:
                  temperature:
                    type: number
                    description: Nhiệt độ hiện tại
                  condition:
                    type: string
                    description: Điều kiện thời tiết
                  humidity:
                    type: number
                    description: Độ ẩm (%)
        
        '404':
          description: Thành phố không tìm thấy
```

## Best Practices

### 1. Descriptions rõ ràng
```yaml
# ❌ Bad
description: Get weather

# ✅ Good  
description: >
  Lấy thông tin thời tiết hiện tại cho một thành phố.
  Sử dụng khi người dùng hỏi "thời tiết ở X thế nào?"
  hoặc "nhiệt độ ở X là bao nhiêu?"
```

### 2. Parameter descriptions chi tiết
```yaml
# ❌ Bad
city:
  type: string

# ✅ Good
city:
  type: string
  description: >
    Tên thành phố cần lấy thời tiết.
    Có thể là tên tiếng Việt (Hà Nội) hoặc tiếng Anh (Hanoi).
    Không bao gồm tên quốc gia.
```

### 3. Sử dụng enums khi có thể
```yaml
# Giúp LLM biết các giá trị hợp lệ
operation:
  type: string
  enum: [add, subtract, multiply, divide]
```

### 4. Xác định required fields
```yaml
required:
  - city  # LLM sẽ hỏi lại nếu thiếu
```

### 5. Error responses
```yaml
responses:
  '400':
    description: Input không hợp lệ
    content:
      application/json:
        schema:
          type: object
          properties:
            error:
              type: string
              description: Mô tả lỗi
```

## So sánh với MCP

| Aspect | OpenAPI | MCP |
|--------|---------|-----|
| Purpose | API specification | Runtime protocol |
| Format | YAML/JSON static | JSON-RPC dynamic |
| Discovery | Manual | Automatic |
| Use case | Bedrock Action Groups | Strands, external tools |
| Standardization | Industry standard | AI-specific |

### Kết hợp OpenAPI + MCP
- OpenAPI định nghĩa interface
- MCP để communicate
- Best of both worlds

## Ví dụ Lambda với OpenAPI

```python
# lambda_function.py
import json

def lambda_handler(event, context):
    """Lambda cho Action Group với OpenAPI schema"""
    
    # Extract từ Bedrock Agent event
    action_group = event.get('actionGroup')
    api_path = event.get('apiPath')
    http_method = event.get('httpMethod')
    parameters = event.get('parameters', [])
    
    # Parse parameters
    params = {p['name']: p['value'] for p in parameters}
    
    if api_path == '/weather' and http_method == 'GET':
        city = params.get('city')
        units = params.get('units', 'C')
        
        # Mock weather data
        weather = {
            'temperature': 28 if units == 'C' else 82,
            'condition': 'Sunny',
            'humidity': 65
        }
        
        return {
            'messageVersion': '1.0',
            'response': {
                'actionGroup': action_group,
                'apiPath': api_path,
                'httpMethod': http_method,
                'httpStatusCode': 200,
                'responseBody': {
                    'application/json': {
                        'body': json.dumps(weather)
                    }
                }
            }
        }
    
    return {
        'messageVersion': '1.0',
        'response': {
            'httpStatusCode': 404,
            'responseBody': {
                'application/json': {
                    'body': json.dumps({'error': 'Not found'})
                }
            }
        }
    }
```

## Liên kết

- Agent basics: [[LLM Agents & Agentic AI]]
- Hands-on: [[Hands On Bedrock Agents]]
- MCP: [[Model Context Protocol]]
- Bedrock: [[Tổng quan Amazon Bedrock]]



