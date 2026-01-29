# Bedrock Cross-Region Inference

## 1️⃣ Câu hỏi cốt lõi

> **Cross-Region Inference giải quyết vấn đề gì?**
> - **Phân phối workload** trên nhiều regions
> - **Bypass quota limits** của một region
> - **Tự động failover** khi có disruption
> - Tiết kiệm 10% chi phí với Global mode

Xem thêm: [[Tổng quan Amazon Bedrock]]

---

## Tổng quan

Cross-Region Inference là tính năng quan trọng của Bedrock để tối đa hóa hiệu suất và độ tin cậy của hệ thống AI.

### Chức năng chính
- **Phân phối workload** trên nhiều AWS regions
- **Tự động failover** khi có service disruption
- **Bypass quota limits** ở một region cụ thể
- **Tối ưu thông lượng** dựa trên peak usage

---

## Các Mode

### 1. Geographic Cross-Region Inference

```
┌─────────────────────────────────────────────────────────┐
│              GEOGRAPHIC MODE                             │
│                                                          │
│  Giới hạn trong một khu vực địa lý cụ thể               │
│                                                          │
│  VD: EU regions only                                     │
│      ┌─────────┐  ┌─────────┐  ┌─────────┐             │
│      │eu-west-1│  │eu-west-2│  │eu-central│             │
│      └─────────┘  └─────────┘  └─────────┘             │
│                                                          │
│  Use cases:                                              │
│  - Data residency requirements (GDPR)                    │
│  - Legal obligations về vị trí dữ liệu                   │
│  - Compliance với regulations địa phương                 │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

### 2. Global Cross-Region Inference

```
┌─────────────────────────────────────────────────────────┐
│              GLOBAL MODE                                 │
│                                                          │
│  Sử dụng BẤT KỲ AWS commercial region                   │
│                                                          │
│  ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐ ┌─────┐              │
│  │US-E1│ │US-W2│ │EU-W1│ │AP-SE1│ │AP-NE1│             │
│  └─────┘ └─────┘ └─────┘ └─────┘ └─────┘              │
│                                                          │
│  Benefits:                                               │
│  ✓ Maximum throughput                                    │
│  ✓ 10% cost savings                                      │
│  ✓ Best fault tolerance                                  │
│  ✓ Tự động chọn optimal region                          │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

---

## Pricing

### Nguyên tắc
- Giá **luôn dựa trên region gọi từ**, không phải region thực thi
- Global mode: **Tiết kiệm 10%** so với single-region
- Không bị tính thêm cho việc route đến region khác

### Ví dụ
```
Gọi từ us-east-1 → Route đến ap-southeast-1
Giá tính = us-east-1 pricing (KHÔNG phải ap-southeast-1)
```

---

## Lưu ý Quan trọng

### 1. Service Control Policies (SCPs)

```
⚠️ CONFLICT POTENTIAL

Nếu AWS Organizations có SCP chặn certain regions:
→ Cross-Region Inference KHÔNG thể sử dụng những regions đó

GIẢI PHÁP:
- Allow tất cả regions mà cross-region có thể sử dụng
- HOẶC set region = "unspecified" trong SCP
```

### 2. Provisioned Throughput

```
⚠️ KHÔNG TƯƠNG THÍCH

Cross-Region Inference ≠ Provisioned Throughput

Nếu bạn provision capacity ở region A:
→ Phải gọi trực tiếp region A
→ Không thể dùng cross-region inference
```

### 3. Không cần bật region thủ công
- Không cần enable regions trong account settings
- Cross-region tự động xử lý

### 4. Security
- **Encrypted in transit** - Dữ liệu được mã hóa khi truyền
- **CloudTrail logging** - Audit trail cho việc routing

---

## Khi nào sử dụng

| Scenario | Mode khuyến nghị |
|----------|------------------|
| Maximum throughput, no restrictions | Global |
| GDPR/EU data residency | Geographic (EU) |
| PCI-DSS với region requirements | Geographic |
| Cost optimization | Global (10% savings) |
| High availability/DR | Global |
| Sử dụng custom models | ❌ Không dùng được |
| Provisioned throughput | ❌ Không dùng được |

---

## Best Practices

```
┌─────────────────────────────────────────────────────────┐
│              CROSS-REGION INFERENCE CHECKLIST            │
│                                                          │
│  □ Xác định data residency requirements                  │
│  □ Review SCPs trong AWS Organizations                   │
│  □ Chọn Geographic nếu có legal obligations              │
│  □ Chọn Global để maximize throughput + save 10%         │
│  □ Không dùng cùng Provisioned Throughput                │
│  □ Enable CloudTrail để audit routing decisions          │
│  □ Monitor via CloudWatch cho cross-region metrics       │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

---

## Liên kết

- [[Tổng quan Amazon Bedrock]]
- [[Tối ưu hóa Hệ thống GenAI]]
- [[CloudWatch Metrics & quan sát]]


