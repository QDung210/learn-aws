# Tối ưu hóa Hệ thống GenAI trên AWS

## 1️⃣ Câu hỏi cốt lõi

> **Tối ưu GenAI bao gồm những gì?**
> - **Token Efficiency**: Giảm chi phí qua việc tối ưu context
> - **Latency**: Time to First Token, caching, model cascading
> - **Scaling**: Auto-scaling, quota management
> - **Cost**: Provisioned throughput, tinh chỉnh vs RAG

Xem thêm: [[RAG Nâng Cao]], [[Tinh chỉnh & LoRA trên Bedrock]], [[CloudWatch Metrics & quan sát]]

---

## Tổng quan

Tối ưu hóa hệ thống AI tạo sinh bao gồm nhiều khía cạnh: chi phí, hiệu suất, độ trễ và khả năng mở rộng. Tài liệu này tổng hợp các chiến lược và kỹ thuật tối ưu hóa quan trọng.

---

## 1. Token Efficiency (Hiệu quả Token)

### Đo lường Token

```
┌─────────────────────────────────────────────────────────┐
│              CÔNG CỤ ĐO LƯỜNG TOKEN                      │
│                                                          │
│  1. Bedrock Token Count API                              │
│     - Đếm token mà không chạy inference                  │
│     - MIỄN PHÍ                                           │
│     - Thử nghiệm các prompt khác nhau                    │
│                                                          │
│  2. CloudWatch Metrics                                   │
│     - Token đầu vào/đầu ra                               │
│     - Số lượng request & thời gian                       │
│     - Time to First Token (TTFT)                         │
│     - Throttling & errors                                │
│     - Độ trễ ở cấp model                                 │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

### Kỹ thuật Tối ưu Token

#### Context Window Optimization
- Phần lớn token đến từ **context** (RAG), không phải prompt
- Cân bằng kích thước chunk vs relevance
- Giới hạn số lượng chunks truy xuất (top 2-5 thay vì top 10)

#### Lọc Chunks với Hybrid Search
```python
# Kết hợp vector search + keyword search
def hybrid_search(query, knowledge_base):
    # Vector similarity search
    semantic_results = vector_search(query)
    
    # Keyword matching
    keyword_results = keyword_search(query)
    
    # Combine và filter
    relevant_chunks = combine_scores(
        semantic_results, 
        keyword_results,
        threshold=0.7
    )
    
    # Loại bỏ chunks không liên quan
    return filter_irrelevant(relevant_chunks)
```

#### Metadata Filtering
- Lọc chunks dựa trên metadata trước khi gửi vào prompt
- Giảm số token không cần thiết

---

## 2. Cost-Effective Model Selection

### Đánh giá Chi phí vs Khả năng

```
┌─────────────────────────────────────────────────────────┐
│            LỰA CHỌN MÔ HÌNH THEO USE CASE                │
│                                                          │
│  USE CASE              │  MÔ HÌNH KHUYẾN NGHỊ            │
│  ──────────────────────┼────────────────────────────     │
│  PhD-level reasoning   │  Claude Opus, GPT-4             │
│  General tasks         │  Claude Sonnet, GPT-4o          │
│  Preprocessing:        │                                 │
│    - Summarization     │  Claude Haiku, cheaper models   │
│    - Classification    │                                 │
│    - Chunking          │                                 │
│    - Data compression  │                                 │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

### Intelligent Prompt Routing (Định tuyến Thông minh)

```
User Prompt
     │
     ▼
┌─────────────┐
│  Analyze    │
│  Complexity │
└──────┬──────┘
       │
   ┌───┴───┐
   │       │
Simple   Complex
   │       │
   ▼       ▼
┌──────┐ ┌──────┐
│Haiku │ │Sonnet│ 
│ $$$  │ │ $$$$ │
└──────┘ └──────┘
```

#### Triển khai trên AWS

1. **Bedrock Intelligent Prompt Routing** (tính năng có sẵn)
   - Tự động phân tích complexity
   - Định tuyến đến model phù hợp

2. **Amazon Prompt Flows**
   - Conditional statements
   - Classifier agent → Route to models

3. **Lambda Functions**
   - Custom routing logic

4. **Strands Agents SDK**
   - Tool-based routing

### Đo lường Price vs Performance

```python
# Sử dụng Amazon Bedrock Evaluations
from bedrock_evaluations import compare_models

# So sánh models với cùng prompts
results = compare_models(
    models=['claude-3-sonnet', 'claude-3-haiku'],
    prompts=test_prompts,
    evaluators=['human', 'llm_judge', 'metrics']
)

# Kết hợp với token counting
cost_per_prompt = token_count * price_per_token[model]
performance_score = results.quality_score

# Tính toán price-to-performance ratio
ratio = performance_score / cost_per_prompt
```

---

## 3. Maximizing Resource Utilization

### Batch Processing

```
┌─────────────────────────────────────────────────────────┐
│              CHIẾN LƯỢC BATCH PROCESSING                 │
│                                                          │
│  1. Embedding Batching                                   │
│     - Gửi nhiều documents cùng lúc                       │
│     - KHÔNG xử lý tuần tự                                │
│                                                          │
│  2. Bedrock Batch Inference                              │
│     - Đặt prompts vào S3 bucket                          │
│     - Chạy batch inference                               │
│     - Nhận tất cả responses từ S3                        │
│     - Phù hợp khi KHÔNG cần real-time                    │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

### Capacity Planning

```python
# Tính toán capacity cần thiết
expected_tokens_per_minute = calculate_tpm(workload)
expected_requests_per_minute = calculate_rpm(workload)

# Sử dụng AWS Service Quotas
# để ước tính và yêu cầu tăng quota nếu cần
```

### Provisioned Throughput

```python
# Khi cần hiệu suất nhất quán dưới tải cao
# Cung cấp theo:
# - Tokens
# - Model Units

# Lưu ý: Sử dụng Provisioned Model ARN
bedrock.invoke_model(
    modelId='arn:aws:bedrock:...:provisioned-model/...',
    body=request_body
)
```

### Tensor Parallelism
- Chia trọng số model trên nhiều GPU
- Tự động xử lý bởi AWS
- Tối ưu memory usage per container

### Auto Scaling với Serverless
- **Lambda** - Tự động scale
- **Bedrock** - Fully managed
- **OpenSearch Serverless** - Vector store scaling
- **Agent Core** - Agent infrastructure

### Monitoring với CloudWatch
- Dashboards tích hợp
- Theo dõi usage patterns
- **AWS Cost Explorer** - Quy kết chi phí theo business function

---

## 4. Intelligent Caching Systems

### Semantic Caching

```
┌─────────────────────────────────────────────────────────┐
│              SEMANTIC CACHING FLOW                       │
│                                                          │
│  Prompt đến                                              │
│       │                                                  │
│       ▼                                                  │
│  ┌──────────────┐                                       │
│  │ Tạo Vector   │                                       │
│  │ Embedding    │                                       │
│  └──────┬───────┘                                       │
│         │                                                │
│         ▼                                                │
│  ┌──────────────┐     ┌──────────────┐                  │
│  │ Tìm trong    │────►│ Cache Hit?   │                  │
│  │ Cache        │     └──────┬───────┘                  │
│  └──────────────┘            │                          │
│                         ┌────┴────┐                     │
│                         │         │                     │
│                       Yes        No                     │
│                         │         │                     │
│                         ▼         ▼                     │
│                    Return    Call Model                 │
│                    Cached    → Cache Result             │
│                    Response                             │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

#### Storage Options
- **ElastiCache for Valkey/Redis**
- **MemoryDB**
- **OpenSearch**

#### Lưu ý quan trọng
- **Điều chỉnh similarity threshold cẩn thận**
- Threshold quá thấp → trả về kết quả không liên quan
- **Đo lường ROI** - Chi phí cache + embeddings vs savings

### Prompt Caching (Bedrock Built-in)

```
┌─────────────────────────────────────────────────────────┐
│              PROMPT CACHING STRUCTURE                    │
│                                                          │
│  ┌─────────────────────────────────────┐                │
│  │     CACHED PREFIX                    │                │
│  │  - System instructions               │                │
│  │  - Few-shot examples                 │                │
│  │  - Static documents                  │                │
│  ├─────────────────────────────────────┤  ← Cache       │
│  │     CACHE CHECKPOINT                 │    Checkpoint  │
│  ├─────────────────────────────────────┤                │
│  │     DYNAMIC CONTENT                  │                │
│  │  - User query                        │                │
│  │  - Variable data                     │                │
│  └─────────────────────────────────────┘                │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

#### Pricing
- **Cache reads**: Giảm giá per token
- **Cache writes**: Đắt hơn trong nhiều trường hợp
- **Cần đo lường** để đảm bảo ROI positive

#### Kích hoạt
- Bedrock Prompt Management hỗ trợ enable prompt caching

### Edge Caching với CloudFront
- Cache responses ở edge locations
- Giảm latency cho users toàn cầu

---

## 5. Building Responsive AI Systems

### Parallel Processing

```
┌─────────────────────────────────────────────────────────┐
│           ORCHESTRATOR-WORKER PATTERN                    │
│                                                          │
│                  User Prompt                             │
│                       │                                  │
│                       ▼                                  │
│               ┌───────────────┐                         │
│               │  Orchestrator │                         │
│               │    Agent      │                         │
│               └───────┬───────┘                         │
│                       │                                  │
│        ┌──────────────┼──────────────┐                  │
│        │              │              │                  │
│        ▼              ▼              ▼                  │
│   ┌─────────┐   ┌─────────┐   ┌─────────┐              │
│   │Worker 1 │   │Worker 2 │   │Worker 3 │              │
│   └────┬────┘   └────┬────┘   └────┬────┘              │
│        │              │              │                  │
│        └──────────────┼──────────────┘                  │
│                       │                                  │
│                       ▼                                  │
│               ┌───────────────┐                         │
│               │  Aggregate    │                         │
│               │  Results      │                         │
│               └───────────────┘                         │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

### Response Streaming

```python
# LUÔN sử dụng streaming khi có thể
response = bedrock.invoke_model_with_response_stream(
    modelId='anthropic.claude-3-sonnet',
    body=json.dumps({
        "anthropic_version": "bedrock-2023-05-31",
        "messages": [{"role": "user", "content": prompt}]
    })
)

# Stream response về cho user
for event in response['body']:
    chunk = json.loads(event['chunk']['bytes'])
    yield chunk['content']
```

### Latency Optimized Inference

```python
# Sử dụng tính năng Bedrock
response = bedrock.invoke_model(
    modelId='anthropic.claude-3-sonnet',
    body=request_body,
    performanceConfig={
        'latency': 'optimized'  # Tối ưu latency
    }
)
```

**Tối ưu hóa:**
- Time to First Token (TTFT)
- Output tokens per second
- End-to-end latency

### Các Mẹo Tăng Tốc Độ Phản hồi

1. **Intelligent Prompt Routing** - Model nhỏ = phản hồi nhanh
2. **Keep prompts concise** - Ít thông tin = xử lý nhanh
3. **Đặt thông tin quan trọng lên đầu** - Phòng trường hợp truncation
4. **Context pruning** - Loại bỏ chunks không cần thiết
5. **Limit response size** - Giới hạn max tokens
6. **Break down complex tasks** - Hiển thị kết quả từng bước

---

## 6. Optimizing Retrieval Performance (RAG)

### Hybrid Search với Custom Scoring

```python
def hybrid_score(query, document):
    # Semantic similarity
    semantic_score = cosine_similarity(
        embed(query), 
        embed(document)
    )
    
    # Keyword matching
    keyword_score = bm25_score(query, document)
    
    # Custom combination
    alpha = 0.7  # Điều chỉnh theo use case
    final_score = alpha * semantic_score + (1 - alpha) * keyword_score
    
    return final_score
```

### Query Preprocessing

```
┌─────────────────────────────────────────────────────────┐
│              QUERY PREPROCESSING STEPS                   │
│                                                          │
│  1. Style Normalization                                  │
│     - Match query style với document style               │
│     - VD: Academic papers → formal query                 │
│                                                          │
│  2. Query Decomposition                                  │
│     - Chia query phức tạp thành nhiều sub-queries        │
│     - "Tìm A và B" → Search A + Search B riêng           │
│                                                          │
│  3. Irrelevant Filtering                                 │
│     - Loại bỏ phần không liên quan đến KB                │
│                                                          │
│  4. Ambiguity Resolution                                 │
│     - Giải quyết "điều tôi nói trước đó"                 │
│     - Thay thế bằng context cụ thể                       │
│                                                          │
│  5. Keyword Extraction                                   │
│     - Trích xuất keywords cho hybrid search              │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

---

## 7. Model Parameters Tuning

### Các Tham số Quan trọng

| Parameter | Ý nghĩa | Giá trị |
|-----------|---------|---------|
| **Temperature** | Mức độ ngẫu nhiên | 0 = nhất quán, 1 = sáng tạo |
| **Top P** (Nucleus Sampling) | Ngưỡng xác suất cho candidates | 0-1 |
| **Top K** | Số lượng token candidates | Integer |
| **Max Tokens** | Giới hạn độ dài response | Integer |

```
Temperature và Top P:
- Thường chỉ định MỘT trong hai
- Temperature: Ngẫu nhiên khi CHỌN từ candidates
- Top P: Ngưỡng để TRỞ THÀNH candidate
```

### A/B Testing
- **Bedrock Evaluations** - So sánh models
- **CloudWatch** - A/B testing features
- **SageMaker** - Experiment tracking

---

## 8. Exponential Backoff & Connection Pooling

### Exponential Backoff

```python
import time
import random

def call_with_backoff(api_func, max_retries=5):
    base_delay = 0.1  # 100ms
    backoff_factor = 2
    jitter = 0.1  # ±100ms
    
    for attempt in range(max_retries):
        try:
            return api_func()
        except Exception as e:
            if attempt == max_retries - 1:
                raise e
            
            delay = base_delay * (backoff_factor ** attempt)
            delay += random.uniform(-jitter, jitter)
            
            time.sleep(delay)
```

**Khuyến nghị AWS:**
- Base delay: 100ms
- Backoff factor: 2
- Max retries: 3-5
- Jitter: ±100ms

### Connection Pooling

```python
import urllib3

# Khuyến nghị AWS
http = urllib3.PoolManager(
    num_pools=10,           # 10-20 connections per instance
    maxsize=20,
    timeout=urllib3.Timeout(connect=5.0, read=60.0),
    retries=urllib3.Retry(3, backoff_factor=0.1)
)

# Connection lifetime: 60-300 seconds
```

---

## 9. Cross-Region Inference

### Tổng quan

```
┌─────────────────────────────────────────────────────────┐
│           BEDROCK CROSS-REGION INFERENCE                 │
│                                                          │
│  ┌─────────┐    ┌─────────┐    ┌─────────┐              │
│  │Region A │    │Region B │    │Region C │              │
│  │ (Full)  │    │(Avail.) │    │(Avail.) │              │
│  └────┬────┘    └────┬────┘    └────┬────┘              │
│       │              │              │                    │
│       └──────────────┼──────────────┘                    │
│                      │                                   │
│                      ▼                                   │
│              ┌───────────────┐                          │
│              │ Automatic     │                          │
│              │ Routing       │                          │
│              └───────────────┘                          │
│                                                          │
│  Benefits:                                               │
│  - Fault tolerance                                       │
│  - Higher throughput                                     │
│  - 10% cost savings (global)                            │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

### Các Mode

| Mode | Mô tả | Use Case |
|------|-------|----------|
| **Geographic** | Giới hạn trong khu vực địa lý | Data residency requirements (EU GDPR) |
| **Global** | Bất kỳ AWS commercial region | Maximum throughput, 10% savings |

### Lưu ý quan trọng

1. **SCP Conflicts**
   - Nếu dùng AWS Organizations SCPs chặn regions
   - Cần allow tất cả regions hoặc set "unspecified"

2. **Provisioned Throughput**
   - Cross-region inference KHÔNG hoạt động với provisioned throughput
   - Phải chọn một trong hai

3. **Pricing**
   - Luôn tính theo region **gọi từ**, không phải region thực thi

4. **Security**
   - Data encrypted in transit
   - Logged trong CloudTrail

---

## 10. SageMaker Optimization Tips

### Large Model Deployment

```python
# Điều chỉnh timeout cho models lớn (lên đến 500GB)
# Model cần thời gian để:
# - Download
# - Initialize
# - Health check

endpoint_config = {
    'ModelDataDownloadTimeoutInSeconds': 3600,
    'ContainerStartupHealthCheckTimeoutInSeconds': 600
}
```

### Model Parallelization Frameworks
- **Triton**
- **Faster Transformer**
- **DeepSpeed**

### Instance Recommendations

| Workload | Instance Type |
|----------|---------------|
| Large FM deployment | ml.p4d.24xlarge |
| Simple NER | ml.m5.9xlarge (CPU có thể đủ) |

### Ultraservers cho Distributed Models
- **TR2** (Trainium 2)
- **P60 GB200**
- Low latency, high bandwidth giữa instances

### Endpoint Lifecycle Management
- Sử dụng Lambda để quản lý model artifacts
- Load on-demand từ S3

---

## Best Practices Summary

```
┌─────────────────────────────────────────────────────────┐
│              CHECKLIST TỐI ƯU HÓA                        │
│                                                          │
│  □ Đo lường trước khi tối ưu (CloudWatch, Token Count)  │
│  □ Sử dụng model phù hợp với complexity                  │
│  □ Bật Intelligent Prompt Routing                        │
│  □ Implement caching (semantic/prompt)                   │
│  □ Sử dụng streaming responses                           │
│  □ Tối ưu context window (limit chunks)                  │
│  □ Batch processing khi có thể                           │
│  □ Bật Cross-Region Inference                            │
│  □ Implement exponential backoff                         │
│  □ Connection pooling cho API calls                      │
│  □ Feedback loops để liên tục cải thiện                  │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

---

## Liên kết

- [[RAG, Cơ sở tri thức, Vector Store]]
- [[Tổng quan Amazon Bedrock]]
- [[CloudWatch Metrics & quan sát]]
- [[Amazon OpenSearch Service]]
- [[Prompt Engineering Overview]]




