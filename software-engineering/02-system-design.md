# 02 — System Design

---

## المفاهيم الأساسية

### Scalability

```
Vertical Scaling   - رفع مواصفات الجهاز (CPU, RAM)
Horizontal Scaling - إضافة أجهزة أكثر

Load Balancer      - توزيع الطلبات على عدة servers
  - Round Robin
  - Least Connections
  - IP Hash
```

### Caching

```
مستويات الـ Cache:
Browser Cache → CDN → Load Balancer → App Cache → DB Cache

أدوات:
Redis     - In-memory, Persistent, Data structures
Memcached - In-memory, Simple key-value

Strategies:
- Cache-aside (Lazy loading)
- Write-through
- Write-behind
- Read-through

Cache Invalidation - المشكلة الأصعب!
```

### Databases

```
SQL vs NoSQL:

SQL (PostgreSQL, MySQL):
✓ ACID transactions
✓ Complex queries
✓ Strong consistency
✗ Harder to scale horizontally

NoSQL:
MongoDB   - Document store
Redis     - Key-value
Cassandra - Wide-column (Write-heavy)
Neo4j     - Graph database
Elasticsearch - Search engine

CAP Theorem:
لا يمكنك الحصول على الثلاثة دفعة واحدة:
Consistency + Availability + Partition Tolerance
```

### شبكة التوصيل (CDN)

```
CDN = Content Delivery Network
- توزيع الملفات الثابتة جغرافيًا
- تقليل الـ Latency
- أمثلة: Cloudflare, AWS CloudFront
```

---

## أنماط التصميم المعماري

```
Monolith         - تطبيق واحد
Microservices    - خدمات صغيرة مستقلة
Event-Driven     - التواصل عبر Events
CQRS             - فصل القراءة عن الكتابة
Event Sourcing   - حفظ الأحداث بدلًا من الحالة
```

### Message Queues

```
Kafka   - High throughput, Log-based
RabbitMQ - Traditional message broker
SQS     - AWS managed

استخدامات:
- Async processing
- Decoupling services
- Event streaming
```

---

## تصميم أنظمة شائعة (مقابلات)

```
1. URL Shortener (مثل bit.ly)
   - Hash function
   - Database للـ mapping
   - Cache للـ popular URLs

2. Twitter/X Feed
   - Fan-out on write vs Fan-out on read
   - Timeline generation
   - Celebrity problem

3. YouTube
   - Video storage (CDN)
   - Encoding pipeline
   - Recommendation system

4. Rate Limiter
   - Token Bucket
   - Sliding Window
   - Redis implementation

5. Distributed Cache
   - Consistent Hashing
   - Replication
   - Eviction policies
```

---

## أرقام يجب حفظها

```
Latency:
L1 cache:     ~1 ns
RAM:          ~100 ns
SSD:          ~100 μs
Network:      ~1-10 ms
HDD:          ~10 ms

Throughput:
SSD read:     ~500 MB/s
Network:      ~1 Gb/s = 125 MB/s
RAM:          ~10 GB/s
```

---

## ⏱️ الوقت المقدر: 2-3 أشهر

## مصادر
- كتاب: *System Design Interview* - Alex Xu (Vol 1 & 2)
- [ByteByteGo](https://bytebytego.com) ← أفضل محتوى بصري
- [System Design Primer](https://github.com/donnemartin/system-design-primer) ← مجاني
