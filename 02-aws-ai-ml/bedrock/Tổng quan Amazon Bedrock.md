# Tổng quan Amazon Bedrock

## 1️⃣ Giải quyết vấn đề gì? (WHY)

**Vấn đề kinh điển**: **Truy cập Foundation Models serverless** - dùng LLMs mà không cần quản lý infrastructure.

**Nếu không có Bedrock**:
- Tự host models trên SageMaker/EC2
- Quản lý GPU instances, scaling
- Tích hợp từng model riêng biệt

**Bedrock sinh ra để**:
- Unified API cho nhiều FMs (Claude, Titan, Llama, Nova...)
- Serverless, pay-per-token
- Built-in RAG, Agents, Guardrails
- Swap models dễ dàng

---

## 2️⃣ Thuộc nhóm nào trong kiến trúc AWS?

| Phân loại | Vị trí |
|-----------|--------|
| **Category** | AI/ML (GenAI) |
| **Pipeline** | **Serving layer** (inference) |
| **Pattern** | LLM-as-a-Service |

---

## 3️⃣ Input – Output (DATA FLOW)

| Input | Output |
|-------|--------|
| Prompts (text, images) | Generated text/images |
| Knowledge Base queries | RAG responses |
| Agent requests | Tool calls + responses |
| Embedding requests | Vector embeddings |

---

## 4️⃣ Cách hoạt động (HOW – Conceptual)

- **Serverless**: Không quản lý servers
- **Sync/Streaming**: Response đầy đủ hoặc stream tokens
- **Stateless**: Mỗi request độc lập (conversation history do client)
- **Multi-modal**: Text, images, (video với Nova)

---

## 5️⃣ Khi nào dùng – Khi nào KHÔNG dùng?

### ✅ Use Cases chuẩn
- Chatbots, Q&A systems
- Content generation
- RAG với Knowledge Bases
- Agentic workflows
- Embedding generation

### ❌ Anti-patterns
| Sai | Đúng |
|-----|------|
| Custom model training | → [[Amazon SageMaker AI]] |
| Full control over infrastructure | → SageMaker endpoints |
| Non-AI workloads | → Lambda, EC2 |

Chi tiết: [[Bedrock vs SageMaker]]

---

## 6️⃣ So sánh với SageMaker

| Feature | Bedrock | SageMaker |
|---------|---------|-----------|
| **Management** | Serverless | Managed instances |
| **Models** | Curated FMs | Any (bring your own) |
| **Training** | Fine-tuning only | Full training |
| **Pricing** | Per token | Per instance-hour |
| **Control** | Limited | Full |
| **Best for** | Quick GenAI access | Custom ML |

---

## 7️⃣ Cost – Scale – Limitations

### Pricing Models
| Mode | Description |
|------|-------------|
| **On-Demand** | Pay per token (input + output) |
| **Provisioned** | Reserved capacity (Model Units) |
| **Batch** | Async processing, cheaper |

### Tối ưu chi phí
- [[Bedrock Cross-Region Inference]]: 10% savings với Global mode
- **Prompt Caching**: Cache system prompts
- **Intelligent Routing**: Auto-select model by complexity
- **Batch Inference**: Cho non-real-time workloads

### Limits
| Limit | Value |
|-------|-------|
| Context window | Model-dependent (8K–200K) |
| Max output | Model-dependent |
| Requests/min | Quota-based |

---

## Thành phần lõi

### Runtime APIs
- `InvokeModel`: Single inference
- `Converse`: Chat với history
- `InvokeModelWithResponseStream`: Streaming

### Agents
- Orchestration với tools
- Multi-step reasoning
- [[LLM Agents & Agentic AI]]

### Knowledge Bases
- Managed RAG pipeline
- Auto chunking, embedding, retrieval
- [[RAG, Cơ sở tri thức, Vector Store]]

### Guardrails
- Content filtering
- PII protection
- [[Amazon Bedrock Guardrails]]

---

## Tính năng Tối ưu hóa

### Token Count API
- Đếm token **miễn phí** mà không cần chạy inference
- Thử nghiệm prompts khác nhau để tối ưu chi phí

### Intelligent Prompt Routing
- Tự động phân tích complexity của prompt
- Định tuyến đến model phù hợp (tiết kiệm chi phí)

### Latency Optimized Inference
- Tối ưu Time to First Token (TTFT)
- Bật qua `performanceConfig: { latency: 'optimized' }`

### Prompt Caching
- Cache prefix của prompt (system instructions, few-shot)
- Reads rẻ hơn, writes đắt hơn

### Cross-Region Inference
- Geographic hoặc Global mode
- 10% savings, better availability
- [[Bedrock Cross-Region Inference]]

---

## Liên kết

- Runtime/Agent/KB: [[Runtime API, Agent, Knowledge Base]]
- Playground: [[Bedrock Playground & cấu hình suy luận]]
- RAG/KB: [[RAG, Cơ sở tri thức, Vector Store]]
- Fine-tuning: [[Tinh chỉnh & LoRA trên Bedrock]]
- Data extraction: [[Bedrock Data Automation (BDA)]]
- Tối ưu hóa: [[Tối ưu hóa Hệ thống GenAI]]
- So sánh: [[Bedrock vs SageMaker]]
