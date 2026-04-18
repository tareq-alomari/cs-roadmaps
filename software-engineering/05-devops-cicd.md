# 05 — DevOps & CI/CD

---

## Git

```bash
# Workflow احترافي
git flow init
git checkout -b feature/user-auth
git commit -m "feat: add JWT authentication"
git push -u origin feature/user-auth
# → Pull Request → Code Review → Merge

# أوامر مهمة
git rebase main              # تحديث الـ branch
git cherry-pick <commit>     # أخذ commit محدد
git bisect start             # إيجاد الـ commit الذي سبب البug
git stash                    # حفظ التغييرات مؤقتًا
```

---

## Docker

```dockerfile
# Dockerfile محسّن
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/node_modules ./node_modules
COPY . .
USER node
EXPOSE 3000
CMD ["node", "server.js"]
```

```bash
docker build -t myapp:1.0 .
docker run -p 3000:3000 --env-file .env myapp:1.0
docker compose up -d
```

---

## Kubernetes (K8s)

```yaml
# Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-server
spec:
  replicas: 3
  selector:
    matchLabels:
      app: api-server
  template:
    spec:
      containers:
      - name: api
        image: myapp:1.0
        resources:
          requests: { cpu: "100m", memory: "128Mi" }
          limits:   { cpu: "500m", memory: "512Mi" }
```

```bash
kubectl apply -f deployment.yaml
kubectl get pods
kubectl logs -f pod-name
kubectl scale deployment api-server --replicas=5
```

---

## CI/CD Pipeline

```yaml
# GitHub Actions مثال
name: CI/CD
on:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci
      - run: npm test

  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - run: docker build -t myapp .
      - run: docker push myapp
      - run: kubectl set image deployment/api myapp=myapp:${{ github.sha }}
```

---

## Monitoring

```
Prometheus  - جمع الـ Metrics
Grafana     - عرض الـ Dashboards
ELK Stack   - الـ Logs
Jaeger      - Distributed Tracing
PagerDuty   - التنبيهات والإنذارات

الـ 4 Golden Signals:
1. Latency    - وقت الاستجابة
2. Traffic    - عدد الطلبات
3. Errors     - معدل الأخطاء
4. Saturation - مدى امتلاء الموارد
```

---

## أمثلة عملية متقدمة

### Docker Networking & Compose

```yaml
# docker-compose.yml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=postgres://user:pass@db:5432/myapp
      - REDIS_URL=redis://redis:6379
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_started
    networks:
      - backend
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s

  db:
    image: postgres:15-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: myapp
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user -d myapp"]
      interval: 10s
    networks:
      - backend

  redis:
    image: redis:7-alpine
    command: redis-server --appendonly yes
    volumes:
      - redis_data:/data
    networks:
      - backend

networks:
  backend:

volumes:
  postgres_data:
  redis_data:
```

```bash
# Commands
docker compose up -d              # Start all
docker compose logs -f app         # View logs
docker compose exec app sh          # Shell into container
docker compose down -v              # Remove with volumes
docker compose build --no-cache    # Rebuild
```

### Kubernetes المتقدم

```yaml
# Service (ClusterIP)
apiVersion: v1
kind: Service
metadata:
  name: myapp-svc
spec:
  selector:
    app: myapp
  ports:
    - port: 80
      targetPort: 3000
  type: ClusterIP

---
# Ingress (external access)
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myapp-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - host: myapp.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: myapp-svc
                port:
                  number: 80

---
# ConfigMap
apiVersion: v1
kind: ConfigMap
metadata:
  name: myapp-config
data:
  DATABASE_URL: "postgres://user:pass@db:5432/myapp"
  REDIS_URL: "redis://redis:6379"

---
# Secret (base64 encoded)
apiVersion: v1
kind: Secret
metadata:
  name: myapp-secret
type: Opaque
data:
  # echo -n "password" | base64
  DB_PASSWORD: cGFzc3dvcmQ=
  API_KEY: YXBpLWtleS1oZXJl

---
# HorizontalPodAutoscaler
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: myapp-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
```

### CI/CD مع Testing

```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: test
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432

    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run linter
        run: npm run lint

      - name: Run tests
        env:
          DATABASE_URL: postgres://test:test@localhost:5432/test
        run: npm run test:ci

      - name: Upload coverage
        uses: codecov/codecov-action@v3

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Build Docker image
        run: docker build -t myapp:${{ github.sha }} .

      - name: Push to registry
        run: |
          echo "${{ secrets.DOCKER_TOKEN }}" | docker login -u "${{ secrets.DOCKER_USER }}" --password-stdin
          docker push myapp:${{ github.sha }}

  deploy:
    needs: build
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to production
        run: |
          kubectl set image deployment/myapp myapp=myapp:${{ github.sha }}
          kubectl rollout status deployment/myapp
```

### Terraform

```hcl
# main.tf
provider "aws" {
  region = "us-east-1"
}

variable "project_name" {
  default = "myapp"
}

resource "aws_vpc" "main" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true
  enable_dns_support   = true
}

resource "aws_subnet" "public" {
  vpc_id                  = aws_vpc.main.id
  cidr_block             = "10.0.1.0/24"
  map_public_ip_on_launch = true
}

resource "aws_eip" "nat" {
  domain = "vpc"
}

resource "aws_nat_gateway" "main" {
  allocation_id = aws_eip.nat.id
  subnet_id    = aws_subnet.public.id
}

resource "aws_instance" "app" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t3.micro"
  subnet_id    = aws_subnet.public.id

  tags = {
    Name = "${var.project_name}-app"
  }
}

output "app_ip" {
  value = aws_instance.app.public_ip
}
```

---

## ⏱️ الوقت المقدر: 2-3 أشهر

## مصادر
- [Docker Documentation](https://docs.docker.com)
- [Kubernetes.io](https://kubernetes.io/docs/)
- [GitHub Actions Docs](https://docs.github.com/en/actions)
- [Terraform Docs](https://www.terraform.io/docs)
