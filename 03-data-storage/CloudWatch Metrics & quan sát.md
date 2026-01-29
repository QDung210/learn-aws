# CloudWatch Metrics & Quan sát

## 1️⃣ Giải quyết vấn đề gì? (WHY)

**Vấn đề kinh điển**: **Monitoring & Observability** - theo dõi health, performance, costs của AWS resources.

**Nếu không có CloudWatch**:
- Tự setup Prometheus, Grafana, ELK
- Khó tích hợp với AWS services
- Riêng rẽ logs, metrics, traces

**CloudWatch sinh ra để**:
- Unified monitoring cho toàn bộ AWS
- Alarms & automated actions
- Log aggregation & analysis
- Native integration với mọi AWS service

---

## 2️⃣ Thuộc nhóm nào trong kiến trúc AWS?

| Phân loại | Vị trí |
|-----------|--------|
| **Category** | Management & Governance |
| **Pipeline** | **Observability layer** (cross-cutting) |
| **Pattern** | Monitoring, Alerting, Logging |

---

## 3️⃣ Input – Output (DATA FLOW)

| Input | Output |
|-------|--------|
| AWS service metrics (auto) | Dashboards |
| Custom metrics (SDK) | Alarms → SNS, Lambda |
| Application logs | Log Insights queries |
| Bedrock invocations | Cost/performance reports |

---

## 4️⃣ Cách hoạt động (HOW – Conceptual)

- **Push model**: Services push metrics/logs to CloudWatch
- **Near real-time**: ~1 min granularity (standard), 1s (detailed)
- **Stateful**: Data retained (15 months for metrics)
- **Query-able**: Log Insights, Metrics Insights

---

## 5️⃣ Khi nào dùng – Khi nào KHÔNG dùng?

### ✅ Use Cases chuẩn
- Infrastructure monitoring (EC2, RDS, Lambda)
- Application logs aggregation
- Cost & usage tracking
- Bedrock/GenAI performance monitoring
- Automated alerting

### ❌ Anti-patterns
| Sai | Đúng |
|-----|------|
| Long-term log archive | → S3 |
| Advanced APM (traces) | → X-Ray |
| Third-party integration heavy | → Datadog, Splunk |

---

## 6️⃣ Components

### Metrics
- **Standard**: 1-minute resolution
- **Detailed**: 1-second (extra cost)
- **Custom**: Push from application

### Logs
- **Log Groups**: Container cho logs
- **Log Streams**: Sequences trong group
- **Log Insights**: SQL-like queries

### Alarms
- **Threshold-based**: CPU > 80%
- **Anomaly detection**: ML-based
- **Actions**: SNS, Lambda, Auto Scaling

### Dashboards
- Visual representation
- Cross-account, cross-region
- Automatic dashboards cho services

---

## Bedrock-Specific Metrics

### Request Level
| Metric | Ý nghĩa |
|--------|---------|
| `InvocationCount` | Số requests |
| `InvocationLatency` | Tổng latency |
| `FirstByteLatency` (TTFT) | Time to First Token |
| `ThrottledCount` | Số lần bị throttle |
| `ClientErrors` / `ServerErrors` | Lỗi |

### Model Level
| Metric | Ý nghĩa |
|--------|---------|
| `InputTokenCount` | Tokens đầu vào |
| `OutputTokenCount` | Tokens đầu ra |
| `ModelLatency` | Latency ở model |

### Cache Metrics
| Metric | Ý nghĩa |
|--------|---------|
| `CacheReads` | Prompt cache hits |
| `CacheWrites` | Prompt cache writes |

---

## 7️⃣ Cost – Scale – Limitations

### Pricing
| Component | Giá |
|-----------|-----|
| **Metrics** | $0.30/metric/month (first 10K) |
| **Logs ingested** | $0.50/GB |
| **Logs stored** | $0.03/GB/month |
| **Dashboards** | $3/dashboard/month |
| **Alarms** | $0.10/alarm/month |

### Limits
| Limit | Value |
|-------|-------|
| Metrics per account | 10,000+ (soft) |
| Log retention | 1 day → indefinite |
| Alarm actions | 5 per alarm |

---

## Best Practices

### Bedrock Monitoring
```
Dashboard gồm:
├── Latency (p50, p90, p99)
├── Error rate
├── Token usage (input + output)
├── Throttling events
├── Cost estimation
└── TTFT (streaming)
```

### Alarming
- **Throttling** → Yêu cầu tăng quota
- **Error rate** > threshold → Investigate
- **IteratorAge** (Kinesis) → Consumer lag
- **Cost** → Budget alerts

### Log Insights Queries
```sql
-- Bedrock errors
fields @timestamp, @message
| filter @message like /error/i
| sort @timestamp desc
| limit 100
```

### Cost Optimization
- Reduce log retention cho non-critical
- Use metric math thay vì nhiều alarms
- Sample logs thay vì 100%

---

## Liên kết

- Bedrock monitoring: [[Tổng quan Amazon Bedrock]]
- Guardrails logging: [[Amazon Bedrock Guardrails]]
- Streaming metrics: [[Amazon Kinesis]]
- RAG monitoring: [[RAG Nâng Cao]]
- Cost optimization: [[Tối ưu hóa Hệ thống GenAI]]
