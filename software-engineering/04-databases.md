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

## أمثلة عملية متقدمة

### PostgreSQL — Common Table Expressions (CTE)

```sql
-- Hierarchical data ( organizational chart)
WITH RECURSIVE org_chart AS (
    SELECT id, name, manager_id, 1 as level
    FROM employees
    WHERE manager_id IS NULL

    UNION ALL

    SELECT e.id, e.name, e.manager_id, oc.level + 1
    FROM employees e
    INNER JOIN org_chart oc ON e.manager_id = oc.id
)
SELECT * FROM org_chart;

-- Running total
SELECT
    date,
    amount,
    SUM(amount) OVER (ORDER BY date) as running_total
FROM transactions;

--排名
SELECT
    name,
    sales,
    RANK() OVER (ORDER BY sales DESC) as rank
FROM employees;
```

### MongoDB — Advanced Queries

```javascript
// Transactions (multi-document)
const session = mongoose.startSession();
session.startTransaction();
try {
    await User.findByIdAndUpdate(userId, { $inc: { balance: -100 } }, { session });
    await Order.create([{ userId, amount: 100 }], { session });
    await session.commitTransaction();
} catch (e) {
    await session.abortTransaction();
} finally {
    await session.endSession();
}

// Aggregation with $lookup (joins)
db.orders.aggregate([
    {
        $lookup: {
            from: "users",
            localField: "user_id",
            foreignField: "_id",
            as: "user"
        }
    },
    { $unwind: "$user" },
    {
        $group: {
            _id: "$user.country",
            total: { $sum: "$amount" }
        }
    }
]);
```

### Redis — Patterns المتقدمة

```python
# Distributed Lock
def acquire_lock(lock_name, timeout=10):
    return redis.set(lock_name, "1", nx=True, ex=timeout)

def release_lock(lock_name):
    redis.delete(lock_name)

# Rate Limiter with Sliding Window
def sliding_rate_limit(key, limit, window):
    now = time.time()
    start = now - window

    pipe = redis.pipeline()
    pipe.zremrangebyscore(key, 0, start)
    pipe.zadd(key, {f"{now}": now})
    pipe.zcard(key)
    pipe.expire(key, window)

    count = pipe.execute()[2]
    return count <= limit

# Leaderboard
redis.zadd("leaderboard", {"user1": 1000, "user2": 2000})
redis.zrevrange("leaderboard", 0, 10, WITHSCORES=True)
redis.zrank("leaderboard", "user1")  #排名
redis.zincrby("leaderboard", 50, "user1")
```

### Database Migration

```sql
-- Adding column safely
ALTER TABLE users ADD COLUMN status VARCHAR(20) DEFAULT 'active';

-- Rename column (PostgreSQL 11+)
ALTER TABLE users RENAME COLUMN status TO account_status;

-- Create index concurrently (no lock)
CREATE INDEX CONCURRENTLY idx_user_email ON users(email);

-- Multiple queries in one migration
-- migrations/001_add_users.rb
class AddUsers < ActiveRecord::Migration[7.0]
  def change
    create_table :users do |t|
      t.string :name
      t.string :email
      t.timestamps
    end

    add_index :users, :email, unique: true
  end
end
```

---

## ⏱️ الوقت المقدر: 2-3 أشهر

## مصادر
- [Use The Index, Luke](https://use-the-index-luke.com/) ← مجاني، للـ SQL Optimization
- [MongoDB University](https://learn.mongodb.com/) ← مجاني
- كتاب: *Designing Data-Intensive Applications* - Martin Kleppmann ← الأفضل
