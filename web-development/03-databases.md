# 03 — Databases for Web Development

---

## SQL vs NoSQL

```
SQL (Relational):
- بيانات منظمة في جداول
-.schema صارم
- ACID compliant
- Relations: Join tables
- Examples: PostgreSQL, MySQL

NoSQL (Non-relational):
- مرن، documents/key-value/graphs
-schema ديناميكي
- BASE (Basically Available)
- Examples: MongoDB, Redis, Cassandra
```

---

## PostgreSQL

```sql
-- Basics
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT NOW()
);

-- Relationships
CREATE TABLE posts (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id),
    title VARCHAR(255),
    content TEXT,
    created_at TIMESTAMP DEFAULT NOW()
);

-- Queries
SELECT u.name, COUNT(p.id) as post_count
FROM users u
LEFT JOIN posts p ON u.id = p.user_id
GROUP BY u.id
ORDER BY post_count DESC;

-- Indexes
CREATE INDEX idx_user_email ON users(email);
CREATE INDEX idx_post_user ON posts(user_id);
```

### PostgreSQL in Node.js

```javascript
import pg from 'pg';
const { Pool } = pg;

const pool = new Pool({
    connectionString: process.env.DATABASE_URL
});

// Query
const { rows } = await pool.query(
    'SELECT * FROM users WHERE email = $1',
    [email]
);
```

---

## MongoDB

```javascript
// Collections = Tables, Documents = Rows

// Insert
db.users.insertOne({
    name: 'Ahmed',
    email: 'ahmed@example.com',
    age: 25,
    skills: ['JavaScript', 'React']
});

// Query
db.users.find({ age: { $gte: 18 } });
db.users.findOne({ email: 'ahmed@example.com' });

// Update
db.users.updateOne(
    { _id: ObjectId('...') },
    { $set: { age: 26 } }
);

// Aggregation
db.users.aggregate([
    { $match: { age: { $gte: 18 } } },
    { $group: { _id: '$city', count: { $sum: 1 } } }
]);
```

### Mongoose (ODM)

```javascript
import mongoose from 'mongoose';

const userSchema = new mongoose.Schema({
    name: { type: String, required: true },
    email: { type: String, unique: true },
    age: Number,
    posts: [{ type: mongoose.Schema.Types.ObjectId, ref: 'Post' }]
}, { timestamps: true });

const User = mongoose.model('User', userSchema);

// Create
const user = await User.create({ name: 'Ahmed', email: 'ahmed@example.com' });

// Query with population
await User.findOne().populate('posts');
```

---

## Redis

```javascript
import redis from 'redis';
const client = redis.createClient();

await client.connect();

// Strings
await client.set('user:1:name', 'Ahmed');
const name = await client.get('user:1:name');

// Hashes (Objects)
await client.hSet('user:1', { name: 'Ahmed', age: '25' });
const user = await client.hGetAll('user:1');

// Lists
await client.lPush('queue', 'task1');
const task = await client.rPop('queue');

// Sets
await client.sAdd('online_users', 'user1', 'user2');

// Cache with TTL
await client.set('cached_data', JSON.stringify(data), { EX: 3600 });
```

---

## ORM vs Query Builder

```
ORM (Object-Relational Mapping):
- Prisma, Sequelize, TypeORM
- Objects = Database rows
- Less SQL knowledge needed

Query Builder:
- Knex.js
- More control, chainable
- Raw SQL when needed
```

### Prisma

```javascript
import { PrismaClient } from '@prisma/client';
const prisma = new PrismaClient();

// Create
const user = await prisma.user.create({
    data: { name: 'Ahmed', email: 'email@example.com' }
});

// Query
const users = await prisma.user.findMany({
    where: { age: { gte: 18 } },
    orderBy: { name: 'asc' },
    include: { posts: true }
});

// Raw SQL
await prisma.$queryRaw`SELECT * FROM users WHERE age > ${18}`;
```

---

## Database Design Patterns

```sql
-- migrations/001_create_users.sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT NOW()
);

-- migrations/002_add_soft_delete.sql
ALTER TABLE users ADD COLUMN deleted_at TIMESTAMP;
-- Use WHERE deleted_at IS NULL in all queries
```

### Caching Strategy

```
1. Read: Cache → DB (if miss)
2. Write: DB → Cache (with TTL)
3. Invalidate on update

Patterns:
- Cache-aside
- Write-through
- Write-back
```

---

## ⏱️ الوقت المقدر: 2-3 أشهر

## مصادر
- [PostgreSQL Tutorial](https://www.postgresqltutorial.com/)
- [MongoDB University](https://university.mongodb.com/)
- [Redis Documentation](https://redis.io/docs/)
- [Prisma Documentation](https://prisma.io/docs)