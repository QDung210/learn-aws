# Amazon SageMaker AI

## 1️⃣ Giải quyết vấn đề gì? (WHY)

**Vấn đề kinh điển**: **End-to-end ML platform** - từ data prep, training, đến deployment & monitoring.

**Nếu không có SageMaker**:
- Tự setup Jupyter, training infrastructure
- Quản lý GPU clusters, containers
- Build deployment pipeline riêng

**SageMaker sinh ra để**:
- Unified platform cho toàn bộ ML lifecycle
- Managed training với auto-scaling
- One-click deployment với monitoring
- Tích hợp MLOps (registry, pipelines, lineage)

---

## 2️⃣ Thuộc nhóm nào trong kiến trúc AWS?

| Phân loại | Vị trí |
|-----------|--------|
| **Category** | AI/ML (Platform) |
| **Pipeline** | **Full lifecycle** (prep → train → deploy → monitor) |
| **Pattern** | MLOps, Model serving |

---

## 3️⃣ Input – Output (DATA FLOW)

| Input | Output |
|-------|--------|
| Training data từ [[Amazon S3]] | Model artifacts (S3) |
| [[AWS Glue & Data Catalog]] | Inference endpoints |
| Feature Store | Batch predictions |
| Docker images (ECR) | Bedrock import |

---

## 4️⃣ Cách hoạt động (HOW – Conceptual)

- **Managed instances**: AWS quản lý compute cho training & inference
- **Stateful training**: Checkpoints, distributed training
- **Stateless inference**: Auto-scaling endpoints
- **Container-based**: Bring your own container hoặc built-in

---

## 5️⃣ Khi nào dùng – Khi nào KHÔNG dùng?

### ✅ Use Cases chuẩn
- Custom model training (ML truyền thống + GenAI)
- Fine-tuning foundation models
- Production ML với MLOps
- Full control over infrastructure

### ❌ Anti-patterns
| Sai | Đúng |
|-----|------|
| Chỉ cần inference FM có sẵn | → Bedrock (serverless) |
| No-code ML | → SageMaker Canvas |
| Simple preprocessing | → Lambda, Glue |

Chi tiết: [[Bedrock vs SageMaker]]

---

## 6️⃣ So sánh SageMaker vs Bedrock

| Feature | SageMaker | Bedrock |
|---------|-----------|---------|
| **Control** | Full (instances, containers) | Limited (serverless) |
| **Training** | ✅ Custom training | Fine-tuning only |
| **Models** | Any (bring your own) | Curated FMs |
| **Deployment** | Managed endpoints | Serverless |
| **Cost model** | Instance hours | Per token |
| **Use case** | Custom ML, full control | Quick FM access |

---

## 7️⃣ Cost – Scale – Limitations

### Pricing
| Component | Giá |
|-----------|-----|
| **Training** | Instance hours (ml.p3.2xlarge: $3.825/hr) |
| **Inference** | Instance hours + requests |
| **Studio** | Instance hours |
| **Processing** | Instance hours |

### Scaling
- **Training**: Distributed training, Spot instances
- **Inference**: Auto-scaling policies
- **Multi-model endpoints**: Share resources

### Limits
| Limit | Value |
|-------|-------|
| Endpoint instances | 4 per endpoint (default) |
| Training job duration | 28 days |
| Model size | No hard limit |

---

## Tổng quan

SageMaker AI là nền tảng toàn diện của AWS cho toàn bộ vòng đời Machine Learning - từ xử lý dữ liệu, đào tạo, đến triển khai và giám sát mô hình.

### Kiến trúc cơ bản

```
┌──────────────────────────────────────────────────────────────┐
│                   SAGEMAKER WORKFLOW                          │
│                                                               │
│  Training Data (S3)                                           │
│        │                                                      │
│        ▼                                                      │
│  ┌─────────────┐     ┌─────────────┐     ┌─────────────┐     │
│  │  Training   │ ──► │   Model     │ ──► │  Inference  │     │
│  │  Instances  │     │  Artifacts  │     │  Endpoint   │     │
│  └─────────────┘     │    (S3)     │     └─────────────┘     │
│                      └─────────────┘           │              │
│                                                ▼              │
│  Training Code (ECR)              Inference Code (ECR)       │
│        │                                       │              │
│        └───────────────────────────────────────┘              │
│                    Orchestrated by Notebooks                  │
└──────────────────────────────────────────────────────────────┘
```

---

## SageMaker Unified Studio

Giao diện thống nhất cho tất cả công việc ML/AI:
- **Notebooks** (JupyterLab)
- **Visual Studio Code** integration
- **Data processing & ETL**
- **Model training & deployment**
- **Bedrock, Q, QuickSight** integration
- **MLflow** integration

---

## Deployment Safeguards (Bảo vệ triển khai)

### Blue/Green Deployment

```
┌─────────────────────────────────────────────────────────────┐
│              BLUE/GREEN DEPLOYMENT OPTIONS                   │
│                                                              │
│  1. All-at-once                                              │
│     Blue (old) ──────────────► Green (new)                  │
│     100% traffic chuyển ngay lập tức                        │
│                                                              │
│  2. Canary Deployment                                        │
│     Blue 90% ◄─────────────► Green 10%                      │
│     Tăng dần traffic sang Green, monitor liên tục           │
│                                                              │
│  3. Linear Deployment                                        │
│     10% → 20% → 30% → ... → 100%                            │
│     Tự động tăng theo các bước định sẵn                     │
│                                                              │
│  ✓ Auto-rollback nếu phát hiện lỗi                          │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Shadow Testing (Thử nghiệm bóng)

- Chạy **cả 2 models song song** với cùng input
- Model mới (shadow) **không trả kết quả cho production**
- So sánh output để đánh giá trước khi triển khai thật
- Phát hiện divergence mà không ảnh hưởng users

---

## Optimizing FM Deployments

### Multi-Model Endpoints
- Một endpoint phục vụ **nhiều models**
- Tối ưu resource utilization
- Mỗi model có **scaling policy riêng** (Inference Components)

### Multi-Container Endpoints
- Nhiều containers trong một endpoint
- Mỗi endpoint có **VPC và network isolation riêng**

### Tích hợp với Bedrock
- Train/fine-tune model trong SageMaker
- **Import vào Bedrock** qua Custom Model Import
- Inference trở thành serverless

### Model Servers hỗ trợ
- **TorchServe** (PyTorch)
- **DJL Serving** (Deep Java Library - sản phẩm Amazon)
- **Triton Inference Server** (NVIDIA)

---

## SageMaker Ground Truth

Dịch vụ gắn nhãn dữ liệu sử dụng con người:

### Cơ chế hoạt động
1. Dữ liệu cần gắn nhãn được gửi đến workforce
2. Ground Truth **xây dựng model** từ labels đã nhận
3. Chỉ gửi **trường hợp mơ hồ** cho con người
4. **Giảm 70% chi phí** labeling

### Workforce Options
- **Amazon Mechanical Turk** - Lực lượng toàn cầu, giá rẻ
- **Internal Team** - Cho dữ liệu nhạy cảm
- **Professional Labeling Companies** - Chất lượng cao hơn

### Dịch vụ thay thế
- **Amazon Rekognition** - Nhận dạng hình ảnh tự động
- **Amazon Comprehend** - Phân tích văn bản, sentiment, topic modeling

---

## SageMaker Model Monitor

Giám sát models đã triển khai trong production:

### Phát hiện
- **Data Drift** - Dữ liệu đầu vào thay đổi theo thời gian
- **Model Quality Drift** - Độ chính xác giảm
- **Anomalies & Outliers** - Điểm bất thường
- **New Features** - Tính năng mới xuất hiện trong data
- **Missing Features** - Dữ liệu bị thiếu

### Tích hợp
- Metrics → **CloudWatch** → Alarms → Actions
- Visualization: **SageMaker Studio, TensorBoard, QuickSight, Tableau**
- Lên lịch với **Monitoring Schedules**

---

## SageMaker Clarify

Phát hiện và giải thích **bias** trong models:

### Chức năng
- Phát hiện bias trong dữ liệu (age, gender, income groups...)
- Giải thích **feature importance** - tính năng nào ảnh hưởng nhiều nhất
- Nhiều **bias metrics** (Kullback-Leibler divergence, etc.)

### Tích hợp với Model Monitor
- Theo dõi bias drift theo thời gian
- Alert qua CloudWatch

---

## SageMaker Model Registry

Catalog tập trung cho tất cả models trong tổ chức:

### Chức năng
- **Version management** - Quản lý phiên bản
- **Metadata association** - Gắn siêu dữ liệu
- **Approval workflow** - Quy trình phê duyệt
- **Model sharing** - Chia sẻ giữa các ứng dụng
- **Model Cards** integration - Thông tin về model

### Trong CI/CD Pipeline

```
Train Model → Register → EventBridge → Lambda
                │                        │
                ▼                        ▼
         Model Registry           Approval Request
                │                        │
                ▼                        ▼
        Update Approval ◄──────── Approver
                │
                ▼
        Deploy to Production
```

---

## SageMaker Lineage Tracking

Theo dõi lịch sử và nguồn gốc của models:

### Entities được theo dõi
- **Trial Components**: Processing jobs, training jobs, transform jobs
- **Trials**: Nhóm các trial components
- **Experiments**: Nhóm các trials
- **Artifacts**: S3 objects, ECR images, outputs
- **Actions**: Workflow steps, deployments
- **Context**: Nhóm các entities liên quan

### Use Cases
- **Audit & Compliance** - Kiểm toán
- **Impact Analysis** - Ai đang dùng artifact này?
- **Traceability** - Theo dõi data flow từ đầu đến cuối

### Truy vấn
- Visual trong SageMaker Studio
- Python SDK để query programmatically
- Tích hợp **AWS Resource Access Manager** cho cross-account

---

## SageMaker Pipelines

Orchestration cho ML workflows (DAG - Directed Acyclic Graph):

### Ví dụ Pipeline

```
Data Processing
      │
      ▼
Model Training
      │
      ▼
Model Evaluation
      │
  ┌───┴───┐
  │       │
Pass    Fail
  │       │
  ▼       ▼
Register  Stop
Model     
  │
  ▼
Create Model
  │
  ▼
Deploy (Batch Inference)
```

### Tính năng
- **Visual Designer** hoặc JSON definition
- Tích hợp SageMaker Unified Studio
- Conditional logic (pass/fail)
- Tự động hóa end-to-end

---

## SageMaker Neo (Edge Deployment)

Triển khai models lên **edge devices**:

### Đặc điểm
- **Compile** inference code cho edge devices
- Train once, run anywhere
- Hỗ trợ: ARM, Intel, NVIDIA processors

### Frameworks hỗ trợ
- TensorFlow
- MXNet
- PyTorch
- ONNX
- XGBoost

### Thành phần
- **Neo Compiler**: Biên dịch sang bytecode cho edge
- **Neo Runtime**: Chạy trên edge devices

### Tích hợp với IoT Greengrass

```
┌─────────────────────────────────────────────────────────────┐
│                    EDGE DEPLOYMENT FLOW                      │
│                                                              │
│  SageMaker (Cloud)                                           │
│       │                                                      │
│  Train Model                                                 │
│       │                                                      │
│       ▼                                                      │
│  Neo Compile ──────► IoT Greengrass ──────► Edge Device     │
│                          │                      │            │
│                          │                      ▼            │
│                          │              Local Inference     │
│                          │              (Low Latency)       │
│                          │                                   │
│                     Device Types:                            │
│                     - Self-driving cars                      │
│                     - Smart cameras (DeepLens)               │
│                     - Industrial IoT                         │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Use Cases
- **Autonomous vehicles** - Cần quyết định ngay lập tức
- **Smart cameras** - Xử lý video local
- **Industrial IoT** - Không phụ thuộc internet

---

## SageMaker JumpStart

Hub cho pre-trained models:
- Foundation Models (Llama, Falcon, Stable Diffusion...)
- Computer Vision models
- NLP models
- Hugging Face models

### Lưu ý chi phí
- Các instance GPU đắt tiền
- Dễ quên tắt → hóa đơn lớn
- Nên dọn dẹp endpoints sau khi test

---

## Các tính năng khác

### SageMaker Autopilot
- Tự động hóa ML workflow
- AutoML capabilities

### SageMaker Experiments
- Track và so sánh experiments

### SageMaker Debugger
- Debug training issues

### Model Tuning
- Hyperparameter optimization

---

## Best Practices

```
┌─────────────────────────────────────────────────────────────┐
│              SAGEMAKER CHECKLIST                             │
│                                                              │
│  Deployment:                                                 │
│  □ Sử dụng Blue/Green cho safe deployment                   │
│  □ Shadow testing trước khi go-live                         │
│  □ Multi-model endpoints để tối ưu resources                │
│                                                              │
│  Monitoring:                                                 │
│  □ Model Monitor cho drift detection                         │
│  □ Clarify cho bias detection                                │
│  □ Lineage Tracking cho audit                                │
│                                                              │
│  Governance:                                                 │
│  □ Model Registry cho version control                        │
│  □ Approval workflows cho production                         │
│  □ Model Cards cho documentation                             │
│                                                              │
│  Cost:                                                       │
│  □ Dọn dẹp endpoints không dùng                             │
│  □ Cân nhắc Bedrock import cho serverless inference         │
│  □ Auto-scaling cho variable workloads                       │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## Liên kết

- [[Tổng quan Amazon Bedrock]]
- [[Tối ưu hóa Hệ thống GenAI]]
- [[SageMaker Data Wrangler & Canvas]]
- [[CloudWatch Metrics & quan sát]]
- [[Amazon Comprehend]]


