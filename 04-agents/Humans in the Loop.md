# Humans in the Loop (Con người trong vòng lặp)

## 1️⃣ Câu hỏi cốt lõi

> **Khi nào cần Human in the Loop?**
> - **AI không đủ tin cậy** hoặc không chắc chắn
> - **Hành động quan trọng** cần xác nhận (cancel order, refund)
> - **Escalation** khi AI không thể giải quyết

Xem thêm: [[LLM Agents & Agentic AI]], [[Amazon Bedrock Guardrails]]

---

## Tổng quan

### Khái niệm
- Kết hợp **phán đoán của con người** vào hệ thống AI
- Khi AI không đủ tin cậy hoặc không chắc chắn
- Escalation từ AI đến Human khi cần thiết

### Các hình thức

```
┌─────────────────────────────────────────────────────────┐
│           HUMANS IN THE LOOP                             │
│                                                          │
│  1. Content Consumption                                  │
│     AI tạo draft → Human review/edit                     │
│                                                          │
│  2. Escalation Workflow                                  │
│     AI xử lý → Phức tạp/Không chắc → Human takeover     │
│                                                          │
│  3. Feedback Collection                                  │
│     AI response → Human rate/choose → Improve model      │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

## Use Cases

### 1. Escalation trong Customer Service

```
Customer Query
      │
      ▼
┌─────────────┐
│  AI Agent   │
└──────┬──────┘
       │
   ┌───┴───┐
   │       │
 Resolved  Can't resolve
   │       │
   ▼       ▼
Response  ┌─────────────┐
          │ Check       │
          │ Confidence  │
          │ Score       │
          └──────┬──────┘
                 │
            ┌────┴────┐
            │         │
         High       Low
            │         │
            ▼         ▼
       AI Response   ┌─────────────┐
                     │ Create      │
                     │ JIRA Ticket │ → MCP Integration
                     │ for Human   │
                     └─────────────┘
```

### 2. Content Review

```python
# AI tạo content
draft = ai_generate("Write blog post about AI")

# Human review interface
class ContentReview:
    def __init__(self, draft):
        self.draft = draft
        self.status = "pending_review"
    
    def approve(self):
        self.status = "approved"
        publish(self.draft)
    
    def reject(self, feedback):
        self.status = "rejected"
        # Feedback loop để cải thiện
        improve_model(feedback)
    
    def edit(self, edited_content):
        self.draft = edited_content
        self.status = "edited"
        publish(self.draft)
```

### 3. Feedback Collection (A/B Testing)

```
User Query
    │
    ▼
┌──────────────────────────────────────┐
│ Generate 2 responses                  │
│   Model A Response  │  Model B Response│
└──────────┬──────────┴────────┬───────┘
           │                   │
           └───────┬───────────┘
                   │
                   ▼
           ┌───────────────┐
           │ User chooses  │
           │ preferred     │
           │ response      │
           └───────┬───────┘
                   │
                   ▼
           ┌───────────────┐
           │ Store         │
           │ preference    │
           │ in DynamoDB   │
           └───────────────┘
                   │
                   ▼
           Model fine-tuning / selection
```

## Triển khai trên AWS

### Escalation Flow

```
                    ┌─────────────┐
 AI Agent ────────► │ API Gateway │
 (Low Confidence)   └──────┬──────┘
                           │
                           ▼
                    ┌─────────────┐
                    │   Lambda    │
                    │  Handler    │
                    └──────┬──────┘
                           │
           ┌───────────────┼───────────────┐
           │               │               │
           ▼               ▼               ▼
    ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
    │   Jira      │ │  Slack      │ │  Email      │
    │   Ticket    │ │  Message    │ │  (SES)      │
    └─────────────┘ └─────────────┘ └─────────────┘
```

### Feedback Collection Architecture

```
                    ┌─────────────┐
 User Feedback ───► │ API Gateway │
                    └──────┬──────┘
                           │
                           ▼
                    ┌─────────────┐
                    │   Lambda    │
                    │  Processor  │
                    └──────┬──────┘
                           │
                           ▼
                    ┌─────────────┐
                    │  DynamoDB   │
                    │  (Storage)  │
                    └──────┬──────┘
                           │
                           ▼
                    ┌─────────────┐
                    │  Analytics  │
                    │  Pipeline   │
                    └─────────────┘
```

### DynamoDB Schema cho Feedback

```python
# DynamoDB Table: UserFeedback
{
    "feedback_id": "uuid",           # Partition Key
    "timestamp": "2024-01-25T10:00", # Sort Key
    "user_id": "user123",
    "query": "Original user question",
    "response_a": "Model A response",
    "response_b": "Model B response",
    "chosen": "a",                   # User's choice
    "model_a_id": "claude-3-sonnet",
    "model_b_id": "claude-3-haiku",
    "metadata": {
        "session_id": "session123",
        "latency_a_ms": 1500,
        "latency_b_ms": 800
    }
}
```

### Lambda cho Feedback Processing

```python
import boto3
import json
from datetime import datetime
import uuid

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('UserFeedback')

def lambda_handler(event, context):
    body = json.loads(event['body'])
    
    item = {
        'feedback_id': str(uuid.uuid4()),
        'timestamp': datetime.utcnow().isoformat(),
        'user_id': body['user_id'],
        'query': body['query'],
        'response_a': body['response_a'],
        'response_b': body['response_b'],
        'chosen': body['chosen'],
        'model_a_id': body['model_a_id'],
        'model_b_id': body['model_b_id']
    }
    
    table.put_item(Item=item)
    
    return {
        'statusCode': 200,
        'body': json.dumps({'message': 'Feedback recorded'})
    }
```

## Confidence Scoring

### Trong Agent

```python
def should_escalate(response, threshold=0.7):
    """
    Quyết định có cần escalate đến human không
    """
    confidence = response.get('confidence', 0)
    
    if confidence < threshold:
        return True
    
    # Các điều kiện khác
    if response.get('is_sensitive_topic'):
        return True
    
    if response.get('requires_authorization'):
        return True
    
    return False

# Sử dụng
response = agent.process(query)
if should_escalate(response):
    create_ticket(query, response)
else:
    return response.content
```

### Risk Assessment

```python
def assess_risk(query, response):
    """
    Đánh giá mức độ rủi ro của response
    """
    risk_score = 0
    
    # Financial risk
    if mentions_money(response):
        risk_score += 0.3
    
    # Legal risk
    if mentions_legal(response):
        risk_score += 0.3
    
    # PII risk
    if contains_pii(response):
        risk_score += 0.4
    
    return {
        'score': risk_score,
        'requires_human': risk_score > 0.5
    }
```

## MCP Integration cho Escalation

```python
from strands import Agent
from strands.mcp import MCPClient

# Jira MCP cho ticket creation
jira_mcp = MCPClient("http://jira-mcp:8080")

agent = Agent(
    model="bedrock/claude-3-sonnet",
    mcp_clients=[jira_mcp]
)

# Agent có thể tự escalate
response = agent("""
Customer is very upset about billing issue.
If I can't resolve, create a JIRA ticket for human review.
""")
```

## Best Practices

### 1. Clear Escalation Criteria
- Định nghĩa rõ khi nào cần human
- Confidence thresholds
- Topic categories (sensitive, high-value, legal)

### 2. Feedback Loop
```
Human Action → Log → Analyze → Improve AI
```

### 3. Không block hoàn toàn
- AI vẫn có thể respond tạm
- "Tôi sẽ chuyển vấn đề này đến chuyên gia..."

### 4. Track metrics
- Escalation rate
- Resolution time (AI vs Human)
- User satisfaction

## Liên kết

- Agent basics: [[LLM Agents & Agentic AI]]
- MCP: [[Model Context Protocol]]
- DynamoDB: [[Amazon DynamoDB]]
- Guardrails: [[Amazon Bedrock Guardrails]]



