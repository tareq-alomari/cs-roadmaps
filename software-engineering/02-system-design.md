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

## أمثلة عملية مفصلة

### 1. تصميم URL Shortener

```
Requirements:
- Input: long URL → Output: short URL
- Redirect: short URL → long URL
- Analytics: click counting
- High availability

Architecture:
┌────────────┐     ┌────────────┐     ┌─────────┐
│   API     │────▶│  Redis   │────▶│  MySQL  │
│  Server   │     │ (Cache)  │     │ (持久)   │
└────────────┘     └────────────┘     └─────────┘

Database Schema:
- id (PK)
- short_code (unique, 6 chars)
- original_url
- clicks
- created_at
- expires_at

Hash Function:
- CRC32 modulo → collisions
- Base62 encoding (a-z0-9A-Z)
- أوsnowflake IDs

Code:
def shorten_url(long_url):
    short_code = base62_encode(id_counter)
    redis.set(f"url:{short_code}", long_url, ex=3600)
    mysql.insert(short_code, long_url)
    return f"https://short.io/{short_code}"

def expand_url(short_code):
    url = redis.get(f"url:{short_code}")
    if url:
        mysql.update(clicks+1 where short_code)
        return url
    return mysql.get(short_code)
```

### 2. تصميم Rate Limiter

```
Requirements:
- 100 requests/minute per user
- Accurate counting
- Distributed (multi-server)

Approaches:

A. Token Bucket:
┌─────────┐
│  Bucket │ max_tokens=100
│   ┌─┐  │ tokens_added=1/sec
│   │ │  │ token_per_request=1
│   └─┘  │
└─────────┘

Redis Implementation:
def rate_limit(user_id, limit=100, window=60):
    key = f"rate:{user_id}"
    now = time.time()
    window_start = now - window

    # Remove old requests
    redis.zremrangebyscore(key, 0, window_start)

    # Count requests
    count = redis.zcard(key)

    if count >= limit:
        return False

    # Add new request
    redis.zadd(key, {str(now): now})
    redis.expire(key, window)

    return True

B. Sliding Window:
def sliding_window(user_id, limit=100, window=60):
    key = f"rate:{user_id}"
    now = time.time()

    pipe = redis.pipeline()
    pipe.zremrangebyscore(key, 0, now - window)
    pipe.zadd(key, {f"{now}": now})
    pipe.zcard(key)
    pipe.expire(key, window)

    count = pipe.execute()[2]
    return count <= limit
```

### 3. تصميم Chat System

```
Requirements:
- Real-time messaging
- Groups up to 1000 members
- Message history
- Online status

Architecture:
┌─────────┐    ┌──────────┐    ┌─────────────┐
│  WebSocket  │───▶│ KAFKA   │───▶│ Consumers  │
│  Server    │    │        │    │ (Workers)  │
└─────────┘    └──────────┘    └─────────────┘
                              │
                         ┌────▼────┐
                         │  Redis │
                         │ (Cache)│
                         └────┘
                         │
                         ▼
                    ┌──────────┐
                    │  MySQL  │
                    │ (Cassandra)│
                    └──────────┘

Database Schema (Cassandra):
- messages (channel_id, message_id, user_id, content, created_at)
- channels (channel_id, name, members)
- user_status (user_id, status, last_seen)

WebSocket Protocol:
{
    "type": "message",
    "channel_id": "123",
    "user_id": "456",
    "content": "Hello!",
    "created_at": 1699999999
}

Redis for Presence:
- SET user:456:online 1 EX 30
- SUBSCRIBE user:456:status
- PSUBSCRIBE channel:123:typing
```

### 4. تصميم E-commerce Inventory

```
Requirements:
- Real-time inventory tracking
- Prevent overselling
- Multi-warehouse support

Architecture:
┌─────────┐    ┌─────────────┐    ┌────────────┐
│   API   │────│  KAFKA    │────│ Inventory │
│   GW    │    │  (Events)  │    │  Worker   │
└─────────┘    └───────────┘    └───────────┘
                                    │
                         ┌─────────┴─────────┐
                         │                │
                    ┌────▼────┐     ┌────▼────┐
                    │ MySQL   │     │  Redis │
                    │ (持久)  │     │ (Lock)  │
                    └─────────┘     └────────┘

Optimistic Locking:
def reserve_stock(item_id, quantity):
    while True:
        item = mysql.get(item_id)

        if item.stock < quantity:
            raise OutOfStock()

        # version field للـ optimistic locking
        updated = mysql.update(
            f"UPDATE items SET stock = stock - {quantity}, version = version + 1
             WHERE id = {item_id} AND version = {item.version}"
        )

        if updated:
            return True

        # Retry if version changed (conflict)
        continue

Redis Lock:
def reserve_stock_redis(item_id, quantity):
    lock_key = f"lock:item:{item_id}"
    lock = redis.lock(lock_key, timeout=5)

    try:
        with redis.lock(lock_key):
            stock = redis.get(f"stock:{item_id}")
            if stock >= quantity:
                redis.decrby(f"stock:{item_id}", quantity)
                return True
            return False
    except redis.LockError:
        pass
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
