# SageMaker Ground Truth vs Mechanical Turk

## 1️⃣ Câu hỏi cốt lõi

> **Khi nào chọn cái nào cho data labeling?**
> - **Ground Truth**: ML-specific labeling, Active Learning, SageMaker integration
> - **Mechanical Turk**: General crowdsourcing, custom tasks, lower cost

Xem thêm: [[Amazon SageMaker AI]]

---

## Tổng quan nhanh

| Tiêu chí | Ground Truth | Mechanical Turk |
|----------|--------------|-----------------|
| **Focus** | ML data labeling | General crowdsourcing |
| **Integration** | SageMaker native | Standalone service |
| **Workflow** | Pre-built ML tasks | Custom HITs |
| **Quality** | Active Learning, consensus | Manual QC |
| **Output** | SageMaker-ready format | Custom format |
| **Cost** | Higher (managed) | Lower (DIY) |

---

## Khi nào dùng Ground Truth?

✅ **ML-specific labeling** - image, text, video, point cloud  
✅ **Active Learning** - giảm labeling cost  
✅ **SageMaker integration** - direct to training  
✅ **Built-in QC** - consensus, audit  
✅ **Enterprise workforce** - private team  
✅ **Complex annotations** - bounding boxes, semantic segmentation  

### Đặc điểm
- Pre-built task templates
- Automated data distribution
- Model-assisted labeling
- Direct SageMaker integration

---

## Khi nào dùng Mechanical Turk?

✅ **General crowdsourcing** - không chỉ ML  
✅ **Custom tasks** - surveys, data collection  
✅ **Lower cost** - direct to workers  
✅ **High volume, simple tasks** - categorization  
✅ **Flexible format** - any output structure  
✅ **Existing MTurk workflow** - already integrated  

### Đặc điểm
- General-purpose platform
- Custom HIT design
- Pay-per-task pricing
- Large worker pool

---

## So sánh chi tiết

### Task Types

| Task | Ground Truth | Mechanical Turk |
|------|--------------|-----------------|
| Image classification | ✅ Template | 🔧 Build custom |
| Bounding boxes | ✅ Template | 🔧 Build custom |
| Semantic segmentation | ✅ Template | 🔧 Complex |
| Text classification | ✅ Template | 🔧 Build custom |
| NER labeling | ✅ Template | 🔧 Build custom |
| 3D point cloud | ✅ Template | ❌ Difficult |
| Video tracking | ✅ Template | ❌ Difficult |
| Custom surveys | ❌ | ✅ Flexible |
| Data collection | ❌ | ✅ Flexible |

### Workforce Options

**Ground Truth:**
```
1. Amazon Mechanical Turk (public)
2. Private workforce (your team)
3. Vendor workforce (certified companies)
```

**Mechanical Turk:**
```
1. Public workers only
2. Qualifications for filtering
3. Masters qualification (premium)
```

### Quality Control

| Feature | Ground Truth | Mechanical Turk |
|---------|--------------|-----------------|
| Consensus voting | ✅ Built-in | 🔧 Implement yourself |
| Active Learning | ✅ Auto-labeling | ❌ |
| Audit workflows | ✅ Built-in | 🔧 Custom |
| Worker quality tracking | ✅ Automatic | 🔧 Manual |
| Rejection handling | ✅ Managed | 🔧 Manual |

### Active Learning (Ground Truth)

```
┌─────────────────────────────────────────────────────────────┐
│              GROUND TRUTH ACTIVE LEARNING                    │
│                                                              │
│  Initial Data ──► Human Label (small set)                   │
│                        │                                     │
│                        ▼                                     │
│                   Train Model                                │
│                        │                                     │
│                        ▼                                     │
│              Auto-label (confident)                          │
│                        │                                     │
│                        ▼                                     │
│              Human Review (uncertain)                        │
│                        │                                     │
│                        ▼                                     │
│              Iterate until done                              │
│                                                              │
│  Result: 40-70% cost reduction                               │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## Cost Comparison

### Ground Truth
```
Labeling cost = Base task cost + Worker cost + AWS fee

Example (1000 images, bounding boxes):
- AWS fee: ~$0.08/image = $80
- Worker (MTurk): ~$0.05/image = $50
- Total: ~$130

With Active Learning (70% auto):
- Human: 300 × $0.13 = $39
- Auto: 700 × $0.01 = $7
- Total: ~$46
```

### Mechanical Turk
```
Labeling cost = Task reward + MTurk fee (20-40%)

Example (1000 images, bounding boxes):
- Reward: ~$0.03/image = $30
- MTurk fee: $12
- Total: ~$42

But:
- No Active Learning
- Build custom UI
- Manual QC effort
- Format conversion needed
```

---

## Integration

### Ground Truth Output → SageMaker
```python
# Direct use in SageMaker training
from sagemaker import TrainingInput

training_input = TrainingInput(
    s3_data="s3://bucket/ground-truth-output/",
    content_type="application/x-image"
)

estimator.fit({'train': training_input})
```

### Mechanical Turk Output → SageMaker
```python
# Requires conversion
import json

# 1. Download MTurk results
# 2. Parse custom format
# 3. Convert to SageMaker manifest
# 4. Upload to S3
# 5. Then use in training
```

---

## Decision Matrix

| Scenario | Choice |
|----------|--------|
| ML project with SageMaker | Ground Truth |
| Need Active Learning | Ground Truth |
| Complex annotations (3D, video) | Ground Truth |
| Enterprise compliance needed | Ground Truth (private) |
| Budget-constrained, simple tasks | Mechanical Turk |
| Custom survey/data collection | Mechanical Turk |
| Already have MTurk workflow | Mechanical Turk |
| High volume, low complexity | Mechanical Turk |
| Need immediate SageMaker integration | Ground Truth |

---

## Hybrid Approach

```
┌─────────────────────────────────────────────────────────────┐
│              USE BOTH STRATEGICALLY                          │
│                                                              │
│  Ground Truth:                                               │
│  • Complex annotations                                       │
│  • Training data for models                                  │
│  • Active Learning to reduce cost                            │
│                                                              │
│  Mechanical Turk:                                            │
│  • Simple categorization                                     │
│  • Data collection                                           │
│  • Validation tasks                                          │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## Xem thêm
- [[Amazon SageMaker AI]] - Ground Truth section
- [[Tổng quan Amazon Bedrock]]
