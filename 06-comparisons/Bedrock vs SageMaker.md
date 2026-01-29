# Amazon Bedrock vs Amazon SageMaker

## 1️⃣ Câu hỏi cốt lõi

> **Khi nào chọn cái nào?**
> - **Bedrock**: Muốn dùng LLM nhanh, serverless, không cần train
> - **SageMaker**: Cần custom training, full control, MLOps

---

## Tổng quan nhanh

| Tiêu chí | Bedrock | SageMaker |
|----------|---------|-----------|
| **Focus** | GenAI (Foundation Models) | Full ML Lifecycle |
| **Management** | Fully managed, serverless | More control, managed infra |
| **Models** | Pre-built FMs (Claude, Llama...) | Bring your own + JumpStart |
| **Training** | Fine-tuning, LoRA | Full training control |
| **Deployment** | Serverless inference | Endpoints with control |
| **Pricing** | Per token | Per instance-hour |
| **Người dùng** | App developers | ML Engineers, Data Scientists |

---

## Khi nào dùng Bedrock?

✅ **Prototype nhanh** với LLMs  
✅ **Không muốn quản lý infra** - serverless  
✅ **Cần nhiều FMs** - swap models dễ dàng  
✅ **RAG với Knowledge Bases** - managed  
✅ **Agents & Flows** - orchestration có sẵn  
✅ **Guardrails** - content filtering built-in  
✅ **Multi-modal** - text, image, video (Nova)  

### Đặc điểm
- API chung cho mọi model
- Không cần expertise ML
- Pay-per-token
- Focus vào application layer

---

## Khi nào dùng SageMaker?

✅ **Custom models** - train từ đầu  
✅ **Fine-tune sâu** - cần control hyperparameters  
✅ **ML truyền thống** - classification, regression, forecasting  
✅ **MLOps pipeline** - CI/CD cho models  
✅ **Cost optimization** - Spot instances, multi-model endpoints  
✅ **Specialized hardware** - specific GPU/instance types  
✅ **Governance** - Model Registry, Lineage Tracking  

### Đặc điểm
- Full control over training
- Notebook-first development
- Flexible deployment options
- Enterprise MLOps features

---

## So sánh chi tiết

### Training

| Aspect | Bedrock | SageMaker |
|--------|---------|-----------|
| Pre-trained models | ✅ Many FMs | ✅ JumpStart |
| Fine-tuning | ✅ Limited | ✅ Full control |
| LoRA/PEFT | ✅ | ✅ |
| Train from scratch | ❌ | ✅ |
| Custom algorithms | ❌ | ✅ |
| Hyperparameter tuning | ❌ | ✅ |
| Distributed training | ❌ | ✅ |

### Deployment

| Aspect | Bedrock | SageMaker |
|--------|---------|-----------|
| Serverless | ✅ Default | ✅ Serverless Inference |
| Real-time endpoints | ✅ | ✅ |
| Batch inference | ✅ | ✅ |
| Multi-model endpoint | ❌ | ✅ |
| Shadow testing | ❌ | ✅ |
| Blue/Green, Canary | ❌ | ✅ |
| Auto-scaling | ✅ | ✅ |

### Monitoring & Governance

| Aspect | Bedrock | SageMaker |
|--------|---------|-----------|
| Model Monitor | ❌ | ✅ |
| Clarify (Bias/Explain) | ❌ | ✅ |
| Model Registry | ❌ | ✅ |
| Lineage Tracking | ❌ | ✅ |
| Guardrails | ✅ Built-in | ❌ (Build own) |

### Cost Model

| Bedrock | SageMaker |
|---------|-----------|
| Pay-per-token | Pay-per-instance-hour |
| Provisioned Throughput | Spot Instances available |
| Simple pricing | Complex but optimizable |
| Good for variable load | Good for predictable load |

---

## Hybrid Architecture

```
┌─────────────────────────────────────────────────────────────┐
│              BEST PRACTICE: USE BOTH                         │
│                                                              │
│  SageMaker                          Bedrock                  │
│  ─────────                          ───────                  │
│  • Train custom model               • Serve via API          │
│  • Fine-tune with control           • Add Guardrails         │
│  • Model Registry                   • RAG with KB            │
│  • Validate & approve               • Agent orchestration    │
│                                                              │
│  ┌─────────────┐                   ┌─────────────┐          │
│  │  Train in   │ ──── Import ───► │  Serve in   │          │
│  │  SageMaker  │                   │  Bedrock    │          │
│  └─────────────┘                   └─────────────┘          │
│                                                              │
│  Train where you have control,                               │
│  Serve where it's simple.                                    │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Custom Model Import Flow
```
SageMaker Training → Model Artifacts (S3) → Bedrock Custom Model Import → Bedrock Inference
```

---

## Decision Matrix

| Scenario | Choice |
|----------|--------|
| Quick GenAI prototype | Bedrock |
| Production chatbot with guardrails | Bedrock |
| RAG application | Bedrock (KB) or SageMaker (more control) |
| Custom classification model | SageMaker |
| Time-series forecasting | SageMaker |
| Fine-tune LLM with full control | SageMaker → Import to Bedrock |
| MLOps pipeline with governance | SageMaker |
| A/B testing models | SageMaker |
| Edge deployment | SageMaker Neo |
| Multi-modal generation | Bedrock (Nova) |

---

## Summary Decision Tree

```
Chỉ cần dùng pre-trained LLMs?        → Bedrock
Cần train model từ đầu?               → SageMaker
Cần MLOps pipeline phức tạp?          → SageMaker
Cần Guardrails built-in?              → Bedrock
Cần Model Monitor, Clarify?           → SageMaker
Serverless, không quản lý infra?      → Bedrock
Cần fine-tune + serve serverless?     → SageMaker train → Bedrock serve
```

---

## Xem thêm
- [[Tổng quan Amazon Bedrock]]
- [[Amazon SageMaker AI]]
- [[Tinh chỉnh & LoRA trên Bedrock]]
- [[RAG vs Finetune]]
