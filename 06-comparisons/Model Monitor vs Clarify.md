# SageMaker Model Monitor vs Clarify

## 1️⃣ Câu hỏi cốt lõi

> **Khi nào chọn cái nào cho ML Monitoring?**
> - **Model Monitor**: Drift detection, model còn hoạt động tốt không?
> - **Clarify**: Bias detection, model có fair và giải thích được không?

Xem thêm: [[Amazon SageMaker AI]]

---

## Tổng quan nhanh

| Tiêu chí | Model Monitor | Clarify |
|----------|---------------|---------|
| **Focus** | Drift detection, quality | Bias detection, explainability |
| **When** | Post-deployment (continuous) | Pre & post deployment |
| **Question** | "Model còn hoạt động tốt?" | "Model có fair và giải thích được?" |
| **Output** | Alerts, metrics | Bias reports, feature importance |
| **Use case** | Operations/MLOps | Compliance/Governance |

---

## Model Monitor

### Mục đích
Giám sát liên tục để phát hiện **suy giảm hiệu suất** của model sau deployment.

### Các loại monitoring

```
┌─────────────────────────────────────────────────────────────┐
│              MODEL MONITOR TYPES                             │
│                                                              │
│  1. Data Quality                                             │
│     └─ Schema drift, missing values, statistics changes      │
│                                                              │
│  2. Model Quality                                            │
│     └─ Accuracy, precision, recall (cần ground truth)        │
│                                                              │
│  3. Bias Drift                                               │
│     └─ Protected attributes changing over time               │
│                                                              │
│  4. Feature Attribution Drift                                │
│     └─ Feature importance shifting                           │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Khi nào dùng?
✅ **Production monitoring** - model đang serve  
✅ **Automated alerts** - khi metrics vượt threshold  
✅ **Data drift detection** - input thay đổi  
✅ **Scheduled checks** - hourly, daily  
✅ **Trigger retraining** - khi drift detected  

### Workflow
```
Baseline (training data)
        │
        ▼
Schedule Monitor ──► Capture Inference Data
                            │
                            ▼
                     Compare to Baseline
                            │
                            ▼
               ┌────────────┴────────────┐
               │                         │
          OK (continue)          Violation (alert)
                                         │
                                         ▼
                                  CloudWatch Alarm
                                         │
                                         ▼
                                  Trigger Action
```

---

## Clarify

### Mục đích
Phát hiện **bias** và **giải thích** quyết định của model.

### Các tính năng

```
┌─────────────────────────────────────────────────────────────┐
│              CLARIFY CAPABILITIES                            │
│                                                              │
│  1. Pre-training Bias Detection                             │
│     └─ Imbalance trong training data                        │
│     └─ Class imbalance, label imbalance                     │
│                                                              │
│  2. Post-training Bias Detection                            │
│     └─ Bias trong model predictions                         │
│     └─ Disparate impact, demographic parity                 │
│                                                              │
│  3. Explainability (SHAP)                                   │
│     └─ Feature importance                                   │
│     └─ Individual prediction explanations                   │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Khi nào dùng?
✅ **Pre-deployment validation** - trước khi đưa lên production  
✅ **Compliance requirements** - regulated industries  
✅ **Explain decisions** - tại sao model cho kết quả này  
✅ **Audit trails** - documentation cho regulators  
✅ **Debug model** - hiểu feature importance  

### Bias Metrics
```
┌─────────────────────────────────────────────────────────────┐
│  Class Imbalance (CI)                                       │
│  └─ Nhóm nào có ít/nhiều samples hơn                       │
│                                                              │
│  Difference in Proportions of Labels (DPL)                  │
│  └─ Tỷ lệ positive labels khác nhau giữa groups             │
│                                                              │
│  Disparate Impact (DI)                                      │
│  └─ Tỷ lệ acceptance khác nhau (legal metric)               │
│                                                              │
│  Demographic Parity Difference                              │
│  └─ Positive prediction rate difference                     │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## So sánh chi tiết

### Scope

| Aspect | Model Monitor | Clarify |
|--------|---------------|---------|
| When to use | Production (continuous) | Development & Production |
| Automation | Scheduled jobs | On-demand & scheduled |
| Focus | Operations | Governance |
| Output | Metrics, alerts | Reports, explanations |

### Capabilities

| Feature | Model Monitor | Clarify |
|---------|---------------|---------|
| Data drift | ✅ | ❌ |
| Model quality | ✅ | ❌ |
| Bias detection | ✅ (drift only) | ✅ (comprehensive) |
| Pre-training bias | ❌ | ✅ |
| Post-training bias | ✅ (drift) | ✅ |
| Explainability | ❌ | ✅ SHAP |
| Feature importance | ✅ (drift) | ✅ (detailed) |
| Individual explanations | ❌ | ✅ |

### Integration

| Aspect | Model Monitor | Clarify |
|--------|---------------|---------|
| Real-time endpoint | ✅ | ✅ |
| Batch transform | ✅ | ✅ |
| CloudWatch | ✅ Metrics & Alarms | ✅ |
| SageMaker Pipelines | ✅ | ✅ |
| Model Cards | ❌ | ✅ |

---

## Use Together

```
┌─────────────────────────────────────────────────────────────┐
│              BEST PRACTICE: COMBINE BOTH                     │
│                                                              │
│  DEVELOPMENT PHASE                                           │
│  ─────────────────                                           │
│  Clarify: Pre-training bias check                           │
│  Clarify: Post-training bias check                          │
│  Clarify: Explainability report                             │
│  Clarify: Document in Model Cards                           │
│                                                              │
│  PRODUCTION PHASE                                            │
│  ────────────────                                            │
│  Model Monitor: Data Quality (continuous)                    │
│  Model Monitor: Model Quality (if ground truth)              │
│  Model Monitor: Bias Drift (detect changes)                  │
│  Model Monitor: Alert → Trigger Clarify re-analysis         │
│                                                              │
│  RETRAINING TRIGGER                                          │
│  ─────────────────                                           │
│  Monitor detects drift → Re-run Clarify → Retrain if needed │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## Decision Matrix

| Need | Tool |
|------|------|
| "Is my model degrading?" | Model Monitor |
| "Is my input data shifting?" | Model Monitor |
| "Is my model fair?" | Clarify |
| "Why did model predict X?" | Clarify |
| "Continuous production monitoring" | Model Monitor |
| "Pre-deployment validation" | Clarify |
| "Compliance documentation" | Clarify |
| "Automated alerts" | Model Monitor |
| "Is bias changing over time?" | Model Monitor (Bias Drift) |
| "What are the bias metrics?" | Clarify |

---

## Summary

```
Model Monitor = "Is it still working?"  (Operations)
Clarify       = "Is it fair and explainable?" (Governance)

Use both for comprehensive ML governance.
```

---

## Xem thêm
- [[Amazon SageMaker AI]]
- [[CloudWatch Metrics & quan sát]]
