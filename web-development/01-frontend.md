# 01 — Frontend Development

---

## الأساسيات (HTML/CSS/JS)

```html
<!-- HTML Semantic -->
<header>, <nav>, <main>, <article>, <section>, <footer>
<!-- Accessibility -->
<img alt="description">
<button aria-label="Close menu">
<input id="email" aria-describedby="email-error">
```

```css
/* CSS Essentials */
/* Flexbox */
.container { display: flex; justify-content: space-between; align-items: center; }
/* Grid */
.grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 1rem; }
/* Custom Properties */
:root { --primary: #3b82f6; --spacing: 1rem; }
/* Responsive */
@media (max-width: 768px) { .sidebar { display: none; } }
```

```javascript
// JavaScript الحديث (ES2024)
// Async/Await
const data = await fetch('/api/users').then(r => r.json())

// Destructuring
const { name, age = 0, ...rest } = user
const [first, ...others] = items

// Optional Chaining
const city = user?.address?.city ?? 'Unknown'

// Array methods
const adults = users.filter(u => u.age >= 18).map(u => u.name)
const total = orders.reduce((sum, o) => sum + o.amount, 0)
```

---

## React

```jsx
// Modern React (2024)
import { useState, useEffect, useCallback } from 'react'

function UserList() {
  const [users, setUsers] = useState([])
  const [loading, setLoading] = useState(true)

  useEffect(() => {
    fetch('/api/users')
      .then(r => r.json())
      .then(data => { setUsers(data); setLoading(false) })
  }, [])

  if (loading) return <Spinner />

  return (
    <ul>
      {users.map(user => <UserCard key={user.id} {...user} />)}
    </ul>
  )
}

// Custom Hook
function useUsers() {
  const [users, setUsers] = useState([])
  // ...
  return { users, loading, error }
}
```

### State Management

```javascript
// Zustand (أبسط من Redux)
import { create } from 'zustand'

const useStore = create(set => ({
  count: 0,
  increment: () => set(state => ({ count: state.count + 1 }))
}))

// React Query (للـ Server State)
import { useQuery } from '@tanstack/react-query'

function Users() {
  const { data, isLoading } = useQuery({
    queryKey: ['users'],
    queryFn: () => fetch('/api/users').then(r => r.json())
  })
}
```

---

## Performance

```javascript
// Code Splitting
const Dashboard = React.lazy(() => import('./Dashboard'))

// Memoization
const expensive = useMemo(() => computeExpensive(data), [data])
const handler = useCallback(() => doSomething(id), [id])

// Virtual List للقوائم الطويلة
import { FixedSizeList } from 'react-window'
```

---

## ⏱️ الوقت المقدر: 4-6 أشهر

## مصادر
- [The Odin Project](https://www.theodinproject.com/) ← مجاني وشامل
- [React Documentation](https://react.dev/) ← الرسمي
- [Josh W Comeau CSS](https://www.joshwcomeau.com/) ← للـ CSS المتقدم
