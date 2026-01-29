# Amazon Q Developer

## 1️⃣ Câu hỏi cốt lõi

> **Q Developer giải quyết vấn đề gì?**
> - **AWS Expert**: Trả lời câu hỏi về AWS, account resources
> - **Code Companion**: Hỗ trợ viết code (như GitHub Copilot cho AWS)
> - Chuyên biệt cho **AWS development**

Xem thêm: [[Q Business vs Q Developer]], [[Amazon Q Business]]

---

## Tổng quan

### Amazon Q Developer là gì?
- **AI assistant** cho developers
- **Hai mặt chính**:
  1. **AWS Expert**: Trả lời câu hỏi về AWS
  2. **Code Companion**: Hỗ trợ viết code (như GitHub Copilot)
- Chuyên biệt cho **AWS development**

## Mặt 1: AWS Expert

### Khả năng

#### 1. AWS Documentation Q&A
```
Developer: "Khác biệt giữa SQS và SNS là gì?"
Q Developer: "SQS là message queue (pull-based), SNS là pub/sub (push-based)..."
```

#### 2. Account Resources Discovery
```
Developer: "Liệt kê tất cả Lambda functions của tôi"
Q Developer: "Bạn có 5 Lambda functions ở us-east-1:
- func-api-handler
- func-data-processor
- func-auth
- func-notification
- func-cleanup"
```

#### 3. CLI Command Suggestions
```
Developer: "Thay đổi timeout của Lambda test-api1 thành 10 giây"
Q Developer: "Chạy lệnh sau:
aws lambda update-function-configuration \
  --function-name test-api1 \
  --timeout 10 \
  --region ap-southeast-1"
```

#### 4. Cost Analysis
```
Developer: "Top 3 services tốn chi phí nhất Q1?"
Q Developer: "Theo hóa đơn AWS của bạn:
1. Amazon SageMaker: $1,200
2. Amazon ECS: $800
3. AWS Config: $300"
```

#### 5. Troubleshooting
- Phân tích error logs
- Đề xuất fixes
- Giải thích error messages

### Truy cập
- **AWS Console** - Chat widget
- **AWS CLI** - Terminal integration
- **IDEs** - VS Code, JetBrains

## Mặt 2: Code Companion

### Tương tự GitHub Copilot
- Real-time code suggestions
- Code completion
- Code generation từ comments

### Ngôn ngữ hỗ trợ
| Language | Support Level |
|----------|--------------|
| Python | ⭐⭐⭐ |
| JavaScript | ⭐⭐⭐ |
| TypeScript | ⭐⭐⭐ |
| Java | ⭐⭐⭐ |
| C# | ⭐⭐ |

### Tính năng

#### 1. Code Generation
```python
# Comment: Write function to list S3 bucket files
# Q Developer generates:

import boto3

def list_s3_files(bucket_name: str) -> list:
    """List all files in an S3 bucket."""
    s3 = boto3.client('s3')
    response = s3.list_objects_v2(Bucket=bucket_name)
    
    files = []
    if 'Contents' in response:
        files = [obj['Key'] for obj in response['Contents']]
    
    return files
```

#### 2. Code Completion
```python
# Khi bạn type:
client = boto3.client('dynamodb')
response = client.put_

# Q Developer suggests:
response = client.put_item(
    TableName='my-table',
    Item={
        'pk': {'S': 'value'},
        'sk': {'S': 'value'}
    }
)
```

#### 3. Security Scanning
- Phát hiện vulnerabilities
- Best practice violations
- Secrets in code

#### 4. Code Debugging
- Giải thích bugs
- Đề xuất fixes
- Performance optimization

### Software Agent
- **Implement features** từ description
- **Generate documentation**
- **Bootstrap projects** - tạo project structure

## IDE Integration

### Supported IDEs
| IDE | Extension |
|-----|-----------|
| VS Code | Amazon Q extension |
| Visual Studio | Amazon Q extension |
| JetBrains (IntelliJ, PyCharm) | Amazon Q plugin |
| AWS Cloud9 | Built-in |

### Setup VS Code
```bash
# 1. Install extension
# Extensions → Search "Amazon Q" → Install

# 2. Sign in
# Amazon Q icon → Sign in with AWS Builder ID

# 3. Start using
# Type code → See suggestions
# Or use chat panel for questions
```

## Ví dụ Use Cases

### 1. AWS Service Selection
```
Developer: "Tôi cần lưu trữ session data với low latency, dùng gì?"
Q Developer: "Recommend ElastiCache (Redis) hoặc DynamoDB:
- ElastiCache: Sub-millisecond latency, in-memory
- DynamoDB: Single-digit millisecond, persistent
Nếu cần persistence: DynamoDB. Nếu cần fastest: ElastiCache."
```

### 2. Infrastructure as Code
```
Developer: "Viết Terraform cho Lambda + API Gateway"
Q Developer: [Generates complete Terraform config]
```

### 3. Debugging
```
Developer: "Lambda timeout error, làm sao debug?"
Q Developer: "Check các nguyên nhân:
1. Function timeout setting (max 15 min)
2. Downstream service latency
3. Cold start issues
Recommend: Enable X-Ray tracing, check CloudWatch logs"
```

## Amazon Q Developer Rules

### Khái niệm
- **Customization rules** cho code generation
- Định nghĩa **coding standards** của team
- Ensure **consistency** trong codebase

### Cách sử dụng
```yaml
# .amazonq/rules.yaml
rules:
  - name: "Use async/await"
    description: "Always use async/await instead of callbacks"
    pattern: "callback"
    suggestion: "Convert to async/await pattern"
  
  - name: "Error handling"
    description: "All AWS calls must have try/catch"
    scope: "boto3, aws-sdk"
    enforcement: required
```

### Ví dụ Rules

#### Python Style
```yaml
rules:
  - name: "Type hints required"
    language: python
    pattern: "def * (*)"
    require: "type annotations"
  
  - name: "Docstrings required"
    language: python
    pattern: "def * (*)"
    require: "docstring"
```

#### Security
```yaml
rules:
  - name: "No hardcoded credentials"
    pattern: "aws_access_key|aws_secret"
    action: block
    message: "Use environment variables or Secrets Manager"
```

## So sánh với GitHub Copilot

| Feature | Q Developer | GitHub Copilot |
|---------|-------------|----------------|
| AWS Knowledge | ⭐⭐⭐ | ⭐ |
| General coding | ⭐⭐ | ⭐⭐⭐ |
| Account access | ✅ | ❌ |
| Cost analysis | ✅ | ❌ |
| CLI suggestions | ✅ | ❌ |
| Security scan | ✅ | ⭐⭐ |

## Pricing

### Free Tier
- Basic code suggestions
- AWS Q&A
- Limited usage

### Pro Tier
- Unlimited suggestions
- Security scanning
- Team features
- Custom rules

## Best Practices

### 1. Detailed Comments
```python
# ❌ Bad
# Get data

# ✅ Good
# Fetch user profile from DynamoDB by user_id
# Returns None if user not found
```

### 2. Context in Chat
```
❌ "How to do this?"
✅ "I'm building a Lambda function that processes S3 events. 
    How do I handle errors and retry failed operations?"
```

### 3. Review Suggestions
- Không blind accept
- Verify security
- Test generated code

### 4. Use Rules
- Define team standards
- Enforce consistency
- Reduce review overhead

## Liên kết

- Q Business: [[Amazon Q Business]]
- Q Apps: [[Amazon Q Apps]]
- Bedrock: [[Tổng quan Amazon Bedrock]]

