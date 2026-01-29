# Hands On: Xây dựng Agent trên Amazon Bedrock

## 1️⃣ Câu hỏi cốt lõi

> **Thực hành xây dựng Bedrock Agent với:**
> - **Knowledge Base**: RAG cho câu hỏi
> - **Action Groups**: Gọi Lambda functions
> - **Guardrails**: Lọc nội dung

Xem thêm: [[Tổng quan Amazon Bedrock]], [[Bedrock Agents vs Strands]], [[Amazon Bedrock Guardrails]]

---

## Tổng quan
Hướng dẫn thực hành xây dựng một Agent hoàn chỉnh với:
- Knowledge Base (về chủ đề tự doanh)
- Guardrails (lọc nội dung)
- Action Group (lấy thời tiết)

## Bước 1: Tạo Agent mới

### Truy cập Bedrock Console
1. Vào **Amazon Bedrock** → **Agents** → **Create agent**
2. AWS có thể quảng cáo AgentCore - đây là công cụ triển khai sản xuất (xem [[Amazon AgentCore]])

### Cấu hình cơ bản
```yaml
Agent Name: FrankAgent
Description: "Agent trả lời câu hỏi về tự doanh và thời tiết hiện tại"
```

> **Lưu ý**: Điền mô tả đầy đủ! Bedrock sử dụng mô tả này để định tuyến yêu cầu trong hệ thống đa tác nhân.

### Chọn Foundation Model
- Mặc định: **Claude 3.5 Sonnet**
- Có thể thay đổi sang model khác nếu cần

### Viết Instructions
```
Trả lời các truy vấn về tự doanh bằng Knowledge Base đính kèm.
Trả lời các câu hỏi về thời tiết bằng Weather Action Group.
Luôn nói như một con gà. 🐔
```

### Cài đặt bổ sung
- **Code Interpreter**: Bật nếu cần viết/chạy code
- **User input elicitation**: Bật để Agent hỏi lại khi thiếu thông tin
- **KMS Encryption**: Tùy chỉnh nếu cần
- **Timeout**: Mặc định 10 phút, có thể giảm

## Bước 2: Tạo Action Group

### Cấu hình Action Group
```yaml
Name: GetWeather
Description: "Lấy thời tiết hiện tại cho thành phố với đơn vị °C hoặc °F"
Type: Function with Lambda
```

### Định nghĩa Function

#### Thông tin function
```yaml
Function Name: get_weather
Description: "Lấy thời tiết hiện tại cho một thành phố nhất định"
```

#### Tham số
| Tên | Mô tả | Type | Required |
|-----|-------|------|----------|
| city | Tên thành phố cần lấy thời tiết | string | ✅ |
| units | Đơn vị nhiệt độ (C hoặc F) | string | ❌ |

### Tạo Lambda Function

Khi tạo Action Group, Bedrock sẽ tự động tạo Lambda function boilerplate.

#### Xem Lambda được tạo
1. Sau khi tạo Action Group → Click vào Action Group
2. Click **View Lambda function**
3. Mở Lambda editor

#### Cấu trúc event từ Agent
```python
def lambda_handler(event, context):
    action_group = event.get('actionGroup')
    function = event.get('function')
    message_version = event.get('messageVersion')
    parameters = event.get('parameters', [])
    
    # Trích xuất tham số
    city = None
    units = 'C'  # Default
    
    for param in parameters:
        if param['name'] == 'city':
            city = param['value']
        elif param['name'] == 'units':
            units = param['value']
    
    # Logic lấy thời tiết (ví dụ)
    weather_data = get_weather_from_api(city, units)
    
    # Format response cho Agent
    return {
        'messageVersion': '1.0',
        'response': {
            'actionGroup': action_group,
            'function': function,
            'functionResponse': {
                'responseBody': {
                    'TEXT': {
                        'body': f"Thời tiết ở {city}: {weather_data}"
                    }
                }
            }
        }
    }
```

### Mẫu Lambda function hoàn chỉnh

```python
import json
import requests

def lambda_handler(event, context):
    # Giá trị mặc định
    city = "Unknown"
    units = "C"
    
    # Trích xuất tham số từ event
    parameters = event.get('parameters', [])
    for param in parameters:
        if param['name'] == 'city':
            city = param['value']
        elif param['name'] == 'units':
            units = param['value']
    
    # Gọi Weather API (ví dụ: OpenWeatherMap)
    try:
        # API call logic here
        temperature = 25  # Mock data
        condition = "Nắng đẹp"
        
        result = f"Nhiệt độ: {temperature}°{units}, Điều kiện: {condition}"
    except Exception as e:
        result = f"Không thể lấy thời tiết: {str(e)}"
    
    # Response format cho Bedrock Agent
    return {
        'messageVersion': '1.0',
        'response': {
            'actionGroup': event.get('actionGroup'),
            'function': event.get('function'),
            'functionResponse': {
                'responseBody': {
                    'TEXT': {
                        'body': result
                    }
                }
            }
        }
    }
```

## Bước 3: Gắn Knowledge Base

### Liên kết KB với Agent
1. Trong Agent settings → **Knowledge bases**
2. Chọn KB đã tạo trước đó
3. Viết instruction: "Sử dụng KB này để trả lời câu hỏi về tự doanh"

### KB Integration
- Agent tự động truy vấn KB khi phát hiện câu hỏi liên quan
- Kết hợp RAG response vào câu trả lời cuối cùng
- Xem thêm: [[RAG, Cơ sở tri thức, Vector Store]]

## Bước 4: Gắn Guardrails

### Cấu hình Guardrails
- Lọc nội dung chính trị
- Chặn từ ngữ không phù hợp
- Bảo vệ thông tin cá nhân (PII)
- Xem thêm: [[Amazon Bedrock Guardrails]]

## Bước 5: Test Agent

### Prepare & Test
1. Click **Prepare** để build agent
2. Sử dụng **Test** panel trong console
3. Thử các prompt:
   - "Thời tiết Hà Nội hôm nay?"
   - "Làm sao để bắt đầu kinh doanh riêng?"
   - "Thời tiết ở đâu?" (test user input elicitation)

### Trace & Debug
- Xem trace để hiểu luồng xử lý
- Kiểm tra Action Group nào được gọi
- Xem tham số được trích xuất

## Bước 6: Deploy với Alias

### Tạo Alias
1. Sau khi test thành công → **Create alias**
2. Đặt tên: `production`, `staging`, etc.
3. Alias cho phép version management

### Invoke Agent qua API
```python
import boto3

client = boto3.client('bedrock-agent-runtime')

response = client.invoke_agent(
    agentId='AGENT_ID',
    agentAliasId='ALIAS_ID',
    sessionId='unique-session-id',
    inputText='Thời tiết Hà Nội thế nào?'
)

# Stream response
for event in response['completion']:
    if 'chunk' in event:
        print(event['chunk']['bytes'].decode())
```

## Troubleshooting

### Lỗi thường gặp

| Vấn đề | Nguyên nhân | Giải pháp |
|--------|-------------|-----------|
| Agent không gọi Action Group | Mô tả không rõ ràng | Viết mô tả chi tiết hơn |
| Lambda timeout | Function chạy quá lâu | Tối ưu code, tăng timeout |
| Permission denied | IAM role thiếu quyền | Thêm quyền cho Lambda role |
| Không trích xuất được tham số | Mô tả tham số không rõ | Cải thiện mô tả |

### Best Practices
1. **Test từng phần**: Test Lambda riêng trước khi tích hợp
2. **Log chi tiết**: Thêm CloudWatch logging trong Lambda
3. **Error handling**: Trả về message lỗi rõ ràng cho Agent
4. **Versioning**: Sử dụng aliases để quản lý versions

## Liên kết

- Kiến thức nền tảng: [[LLM Agents & Agentic AI]]
- Hệ thống đa tác nhân: [[Multi-Agent Workflows]]
- Triển khai production: [[Amazon AgentCore]]
- Knowledge Base: [[RAG, Cơ sở tri thức, Vector Store]]
- Guardrails: [[Amazon Bedrock Guardrails]]



