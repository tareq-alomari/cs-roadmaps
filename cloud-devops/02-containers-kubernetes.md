# 02 — Containers & Kubernetes

انظر ملف `05-devops-cicd.md` في مسار `software-engineering/` للأوامر الأساسية.

---

## Kubernetes المتقدم

```yaml
# HPA - Horizontal Pod Autoscaler
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: api
  minReplicas: 2
  maxReplicas: 20
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70

# Ingress
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: api.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80
```

```bash
# Helm - Package Manager لـ Kubernetes
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install my-postgres bitnami/postgresql
helm upgrade my-postgres bitnami/postgresql --set auth.password=secret
```

---

## ⏱️ الوقت المقدر: 2-3 أشهر

## مصادر
- [Kubernetes.io Interactive Tutorial](https://kubernetes.io/docs/tutorials/)
- [KodeKloud](https://kodekloud.com/) ← مدفوع لكن الأفضل للـ CKA
