# Amazon Kinesis

## 1️⃣ Giải quyết vấn đề gì? (WHY)

**Vấn đề kinh điển**: Xử lý dữ liệu **streaming real-time** với khối lượng lớn, liên tục.

**Nếu không có Kinesis**:
- Phải tự build hệ thống Kafka/RabbitMQ + quản lý cluster
- Polling liên tục → latency cao, tốn tài nguyên
- Khó scale khi traffic đột biến

**Kinesis sinh ra để**:
- Thu thập logs, metrics, clickstream, IoT data **ngay lập tức**
- Xử lý và phản hồi trong milliseconds thay vì chờ batch hàng giờ
- Cho phép **nhiều consumers** đọc cùng một stream

---

## 2️⃣ Thuộc nhóm nào trong kiến trúc AWS?

| Phân loại | Vị trí |
|-----------|--------|
| **Category** | Analytics / Integration |
| **Pipeline** | **Ingestion → Processing** |
| **Pattern** | Event-driven, Streaming |

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│  Producers  │ ──► │   KINESIS   │ ──► │  Consumers  │
│ (IoT, Logs) │     │  (Ingestion)│     │ (Lambda, S3)│
└─────────────┘     └─────────────┘     └─────────────┘
```

---

## 3️⃣ Input – Output (DATA FLOW)

### Kinesis Data Streams
| Input | Output |
|-------|--------|
| SDK, KPL, Kinesis Agent | Lambda, KCL, Firehose |
| IoT Core, CloudWatch Logs | Kinesis Data Analytics |
| API Gateway | Custom applications |

### Kinesis Data Firehose
| Input | Output |
|-------|--------|
| Data Streams, Direct PUT | [[Amazon S3]], Redshift |
| CloudWatch Logs | [[Amazon OpenSearch Service]] |
| IoT | HTTP Endpoint, Splunk, Datadog |

### Kinesis Data Analytics (Managed Flink)
| Input | Output |
|-------|--------|
| Data Streams, Firehose | Data Streams, Firehose |
| MSK (Kafka) | [[Amazon S3]], Lambda |

---

## 4️⃣ Cách hoạt động (HOW – Conceptual)

### Data Streams
- **Streaming**: Liên tục, không batch
- **Shard-based**: Mỗi shard = 1 MB/s write, 2 MB/s read
- **Stateful**: Lưu trữ data 24h–365 ngày, hỗ trợ **replay**
- **Pull model**: Consumer tự poll data từ shards

### Firehose
- **Near real-time**: Buffer ≥60s rồi mới delivery
- **Push model**: Tự động đẩy data đến destination
- **Stateless**: Không lưu trữ, chỉ transform & deliver

### Data Analytics (Flink)
- **Streaming SQL** hoặc Apache Flink code
- **Stateful**: Giữ state cho aggregations, windows
- **Continuous**: Query chạy liên tục trên stream

---

## 5️⃣ Khi nào dùng – Khi nào KHÔNG dùng?

### ✅ Use Cases chuẩn
- **Real-time analytics**: Dashboard metrics, monitoring
- **Log aggregation**: Thu thập logs từ nhiều servers
- **IoT data ingestion**: Sensor data, device telemetry
- **Clickstream analysis**: User behavior tracking
- **Fraud detection**: Real-time transaction monitoring

### ❌ Anti-patterns
| Sai | Đúng |
|-----|------|
| Dùng Kinesis cho **request-response** đơn giản | → API Gateway + Lambda |
| Dùng Data Streams khi chỉ cần **load vào S3** | → Firehose (simpler) |
| Dùng Kinesis cho **message queue** (1 consumer/message) | → SQS |
| Dùng Firehose khi cần **replay data** | → Data Streams |
| Dùng Kinesis cho **low-volume events** | → EventBridge |

---

## 6️⃣ So sánh với services khác

### Kinesis Data Streams vs Firehose

| Aspect | Data Streams | Firehose |
|--------|--------------|----------|
| **Latency** | ~200ms (real-time) | ≥60s (near real-time) |
| **Management** | Quản lý shards | Fully managed |
| **Storage** | Có (24h–365 ngày) | Không |
| **Replay** | ✅ Có | ❌ Không |
| **Transform** | Tự code | Lambda tích hợp sẵn |
| **Pricing** | Per shard-hour | Per data volume |

### Kinesis vs SQS vs SNS vs EventBridge

| Feature | Kinesis | SQS | SNS | EventBridge |
|---------|---------|-----|-----|-------------|
| **Pattern** | Stream | Queue | Pub/Sub | Event Bus |
| **Ordering** | Per shard | FIFO only | ❌ | ❌ |
| **Replay** | ✅ | ❌ | ❌ | Archive only |
| **Multi-consumer** | ✅ (same data) | ❌ (1 msg/consumer) | ✅ | ✅ |
| **Retention** | 365 ngày | 14 ngày | ❌ | ❌ |
| **Throughput** | Unlimited (add shards) | Unlimited | Unlimited | Limited |
| **Best for** | High-volume streaming | Decoupling, retry | Fan-out | Event routing |

### Kinesis vs Kafka (MSK)

| Feature | Kinesis | MSK |
|---------|---------|-----|
| **Management** | Serverless | Managed clusters |
| **Ecosystem** | AWS native | Kafka ecosystem |
| **Cost** | Pay per shard/data | Pay per broker |
| **Flexibility** | Limited config | Full Kafka control |

Chi tiết so sánh messaging: xem thêm patterns trong [[ETL]].

---

## 7️⃣ Cost – Scale – Limitations

### Pricing Model

| Component | Pricing |
|-----------|---------|
| **Data Streams (Provisioned)** | $0.015/shard-hour + PUT payload |
| **Data Streams (On-demand)** | $0.04/GB ingested + $0.04/GB retrieved |
| **Firehose** | $0.029/GB (đến S3) |
| **Data Analytics** | $0.11/KPU-hour |
| **Enhanced Fan-Out** | $0.015/shard-hour + $0.013/GB |

### Scaling

| Mode | Behavior |
|------|----------|
| **Provisioned** | Manual add/remove shards (split/merge) |
| **On-demand** | Tự động scale đến 200 MB/s write, 400 MB/s read |

### Limits quan trọng

| Limit | Value |
|-------|-------|
| Shard write | 1 MB/s hoặc 1000 records/s |
| Shard read | 2 MB/s (shared) hoặc 2 MB/s per consumer (Enhanced Fan-Out) |
| Record size | Max 1 MB |
| Retention | 24h (default) → 365 ngày (max) |
| Firehose buffer | 60s–900s hoặc 1MB–128MB |
| Consumers per stream | 5 (default), 20 với Enhanced Fan-Out |

---

## Các thành phần chi tiết

### 1. Kinesis Data Streams (KDS)
- **Shards**: Đơn vị throughput và ordering
- **Partition Key**: Quyết định data vào shard nào → chọn key phân phối đều để tránh **hot shards**
- **Sequence Number**: Unique ID cho mỗi record trong shard
- **Producers**: SDK, KPL (batching, retry), Kinesis Agent
- **Consumers**: KCL (checkpointing), Lambda, Firehose

### 2. Kinesis Data Firehose
- Fully managed [[ETL]] streaming
- Transform với Lambda (JSON → Parquet, filtering)
- Destinations: [[Amazon S3]], Redshift, [[Amazon OpenSearch Service]], HTTP endpoints, 3rd party (Splunk, Datadog)
- **Không lưu data** – chỉ delivery

### 3. Managed Service for Apache Flink
- Thay thế Kinesis Data Analytics for SQL
- Chạy Flink apps (Java, Scala, Python)
- Complex Event Processing, ML trên streams
- Stateful với checkpoints

### 4. Kinesis Video Streams
- Stream video từ cameras, IoT
- Tích hợp Rekognition Video
- Retention 1 giờ → 10 năm

---

## Tích hợp với GenAI/ML

### Real-time Inference Pipeline
```
IoT/Logs → Kinesis Data Streams → Lambda → SageMaker Endpoint → Response
```

### Streaming RAG
```
Real-time Data → Kinesis → Lambda (Embedding) → OpenSearch → RAG Query
```

### Feature Engineering
```
Events → Kinesis → Managed Flink → SageMaker Feature Store
```
Chi tiết SageMaker: [[Amazon SageMaker AI]].

### Log Analysis với LLM
```
App Logs → Kinesis → Firehose → S3 → Bedrock Batch Analysis
```
Chi tiết Bedrock: [[Tổng quan Amazon Bedrock]].

---

## Best Practices

### Partition Key
- Chọn key phân phối đều (user_id, device_id)
- Tránh hot shards: thêm random suffix nếu cần
- Không dùng timestamp làm partition key

### Error Handling
- KPL: built-in retry, batching
- Lambda: configure batch size, on-failure destination
- DLQ cho failed records

### Monitoring (CloudWatch)

| Metric | Ý nghĩa |
|--------|---------|
| `GetRecords.IteratorAgeMilliseconds` | Consumer lag – **quan trọng nhất** |
| `WriteProvisionedThroughputExceeded` | Cần thêm shards |
| `ReadProvisionedThroughputExceeded` | Consumer quá tải |
| `IncomingBytes` / `IncomingRecords` | Throughput hiện tại |

Chi tiết monitoring: [[CloudWatch Metrics & quan sát]].

---

## Exam Tips 🎯

1. **Real-time processing** → Data Streams
2. **Load vào S3/Redshift không cần code** → Firehose
3. **Cần replay data** → Data Streams (Firehose không có)
4. **SQL trên streaming** → Managed Flink
5. **Buffer 60s OK** → Firehose đủ dùng
6. **Multiple consumers, same data** → Data Streams + Enhanced Fan-Out
7. **Video từ camera** → Video Streams
8. **Low volume, event routing** → EventBridge (không phải Kinesis)
9. **Message queue với retry** → SQS (không phải Kinesis)
10. **IoT ingestion** → IoT Core → Kinesis hoặc Kinesis trực tiếp
