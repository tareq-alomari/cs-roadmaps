# 02 — Backend Development

---

## Node.js / Express

```javascript
import express from 'express'
import { z } from 'zod'

const app = express()
app.use(express.json())

// Validation schema
const UserSchema = z.object({
  name: z.string().min(2),
  email: z.string().email(),
})

// Route with error handling
app.post('/users', async (req, res) => {
  try {
    const data = UserSchema.parse(req.body)
    const user = await db.user.create({ data })
    res.status(201).json(user)
  } catch (err) {
    if (err instanceof z.ZodError) return res.status(400).json(err.errors)
    res.status(500).json({ error: 'Internal server error' })
  }
})
```

---

## Python / FastAPI

```python
from fastapi import FastAPI, HTTPException, Depends
from pydantic import BaseModel
from sqlalchemy.orm import Session

app = FastAPI()

class UserCreate(BaseModel):
    name: str
    email: str

@app.post("/users", status_code=201)
async def create_user(user: UserCreate, db: Session = Depends(get_db)):
    db_user = User(**user.model_dump())
    db.add(db_user)
    db.commit()
    db.refresh(db_user)
    return db_user

# Auto-generated docs at /docs (Swagger)
```

---

## Authentication & Authorization

```javascript
// JWT
import jwt from 'jsonwebtoken'

const generateToken = (userId) =>
  jwt.sign({ sub: userId }, process.env.JWT_SECRET, { expiresIn: '7d' })

const verifyToken = (token) =>
  jwt.verify(token, process.env.JWT_SECRET)

// Middleware
const auth = (req, res, next) => {
  const token = req.headers.authorization?.split(' ')[1]
  if (!token) return res.status(401).json({ error: 'Unauthorized' })
  try {
    req.user = verifyToken(token)
    next()
  } catch {
    res.status(401).json({ error: 'Invalid token' })
  }
}
```

---

## REST vs GraphQL vs gRPC

```
REST      ← معيار الصناعة، سهل، مناسب لمعظم الحالات
GraphQL   ← عندما يحتاج كل client بيانات مختلفة
gRPC      ← للتواصل بين الـ Microservices (سريع جدًا)
```

---

## ⏱️ الوقت المقدر: 3-4 أشهر

## مصادر
- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
