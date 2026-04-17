# 04 — Databases

---

## SQL

```sql
-- أساسيات لا تُنسى
SELECT u.name, COUNT(o.id) as orders
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
WHERE u.created_at > '2024-01-01'
GROUP BY u.id
HAVING COUNT(o.id) > 5
ORDER BY orders DESC
LIMIT 10;

-- Indexes — الأهم للـ Performance
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_orders_user ON orders(user_id, created_at);

-- متى تُضيف Index؟
-- ✓ Columns في WHERE, JOIN, ORDER BY
-- ✗ Columns كثيرة التعديل
-- ✗ جداول صغيرة

-- Transactions
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
-- أو ROLLBACK عند الخطأ
```

### ACID

```
Atomicity    - كل العملية أو لا شيء
Consistency  - البيانات تبقى صحيحة
Isolation    - العمليات لا تتداخل
Durability   - البيانات تُحفظ بعد الـ Commit
```

### Query Optimization

```sql
-- استخدم EXPLAIN
EXPLAIN ANALYZE SELECT * FROM orders WHERE user_id = 123;

-- تجنب SELECT *
-- استخدم Pagination
SELECT * FROM users LIMIT 20 OFFSET 0;
-- أو Cursor-based (أفضل للـ Performance)
SELECT * FROM users WHERE id > :last_id LIMIT 20;
```

---

## NoSQL

### MongoDB

```javascript
// CRUD
db.users.insertOne({ name: "Ahmed", age: 25 })
db.users.find({ age: { $gt: 20 } }).sort({ name: 1 }).limit(10)
db.users.updateOne({ _id: id }, { $set: { age: 26 } })
db.users.deleteOne({ _id: id })

// Aggregation Pipeline
db.orders.aggregate([
  { $match: { status: "completed" } },
  { $group: { _id: "$user_id", total: { $sum: "$amount" } } },
  { $sort: { total: -1 } }
])
```

### Redis

```bash
# Key-Value
SET user:123 '{"name":"Ahmed"}'  EX 3600   # مع TTL
GET user:123

# Patterns شائعة
# Caching
SET cache:products:page:1 <json> EX 300

# Rate Limiting
INCR rate:user:123
EXPIRE rate:user:123 60

# Pub/Sub
SUBSCRIBE notifications
PUBLISH notifications '{"msg":"hello"}'
```

---

## Database Design

```
Normalization:
1NF - لا تكرار في الأعمدة
2NF - كل عمود يعتمد على المفتاح الأساسي بالكامل
3NF - لا تعتمد الأعمدة على بعضها

Denormalization:
- عكس الـ Normalization لتحسين الـ Read performance
- مقبول في أنظمة OLAP والـ Reporting
```

---

## ⏱️ الوقت المقدر: 2-3 أشهر

## مصادر
- [Use The Index, Luke](https://use-the-index-luke.com/) ← مجاني، للـ SQL Optimization
- [MongoDB University](https://learn.mongodb.com/) ← مجاني
- كتاب: *Designing Data-Intensive Applications* - Martin Kleppmann ← الأفضل
