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

## ⏱️ الوقت المقدر: 2-3 أشهر

## مصادر
- [Docker Documentation](https://docs.docker.com)
- [Kubernetes.io](https://kubernetes.io/docs/)
- [GitHub Actions Docs](https://docs.github.com/en/actions)
