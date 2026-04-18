# 04 — System Design for Web Applications

---

## Why System Design?

```
 monolith → microservices
     ↓              ↓
  Simple        Complex
  deployment   scaling
```

---

## Scalability

### Horizontal vs Vertical

```
Vertical Scaling:
- Add more CPU/RAM to server
- Simple but limited
- Single point of failure

Horizontal Scaling:
- Add more servers
- Load balancer
- Auto-scaling
```

### Caching

```
Memcached:
- Simple key-value
- No persistence
- Multi-get support

Redis:
- Data structures
- Persistence (RDB/AOF)
- Cluster support
- Pub/Sub
```

### Database Scaling

```
Read Replicas:
- Master → Multiple Slaves
- Async replication
- Eventually consistent

Sharding:
- Partition data across DBs
- By user_id, region, etc.
- More complex queries

CAP Theorem:
Consistency vs Availability vs Partition Tolerance
Pick 2 of 3
```

---

## Load Balancing

```nginx
# Nginx upstream
upstream backend {
    server 10.0.0.1:8080;
    server 10.0.0.2:8080;
    server 10.0.0.3:8080;
}

server {
    location / {
        proxy_pass http://backend;
    }
}
```

### Algorithms

```
Round Robin    ← Equal distribution
Least Connections ← Current load
IP Hash        ← Same client → Same server
Weighted       ← By capacity
```

---

## API Design

### REST

```markdown
GET    /api/users          ← List users
GET    /api/users/:id    ← Get user
POST   /api/users        ← Create user
PUT    /api/users/:id    ← Update user
DELETE /api/users/:id    ← Delete user

Query params:
GET /api/users?page=1&limit=10&sort=created_at
```

### GraphQL

```graphql
type User {
    id: ID!
    name: String!
    posts: [Post!]!
}

type Query {
    user(id: ID!): User
    users(limit: Int): [User!]!
}

type Mutation {
    createUser(input: CreateUserInput!): User
}
```

### gRPC

```protobuf
service UserService {
    rpc GetUser (UserRequest) returns (User);
    rpc CreateUser (CreateUserRequest) returns (User);
    rpc ListUsers (ListUsersRequest) returns (stream User);
}

message User {
    string id = 1;
    string name = 2;
}
```

---

## Microservices

```
Architecture:

┌─────────┐
│   API   │ Gateway
└────┬────┘
     │
┌────┼────┬────┐
│    │    │    │
▼    ▼    ▼    ▼
User Product Order Notification
└────┴────┴────┘
     │
┌────▼────┐
│ Message │
│   Bus   │
└─────────┘
```

### Communication

```
Sync (HTTP/gRPC):
- Request-Response
- Direct

Async (Message Queue):
- Kafka, RabbitMQ
- Event-driven
- Loose coupling
```

---

## Reliability

### Fault Tolerance

```
Circuit Breaker:
Closed → Open → Half-Open

retry with exponential backoff:
attempt 1: 1s
attempt 2: 2s
attempt 3: 4s
```

### Monitoring

```
Metrics:
- Response time (p50, p95, p99)
- Error rate
- Throughput (RPS)

Logs:
- Structured JSON
- Correlation IDs
- Log levels

Tracing:
- OpenTelemetry
- Jaeger
```

---

## ⏱️ الوقت المقدر: 2-3 أشهر

## مصادر
- [System Design Primer](https://github.com/donnemartin/system-design-primer)
- [High Scalability](http://highscalability.com/)
- [Alex Xu - System Design Interview](https://www.amazon.com/System-Design-Interview-Insiders-Guide/dp)