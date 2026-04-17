# 05 — Monitoring & Observability

---

## الثلاثية الأساسية

```
Metrics  → Prometheus + Grafana
Logs     → ELK Stack / Loki
Traces   → Jaeger / Zipkin
```

---

## Prometheus + Grafana

```yaml
# docker-compose.yml
services:
  prometheus:
    image: prom/prometheus
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml

  grafana:
    image: grafana/grafana
    ports:
      - "3000:3000"
```

```python
# تصدير Metrics من تطبيقك
from prometheus_client import Counter, Histogram, start_http_server

REQUEST_COUNT = Counter('http_requests_total', 'Total requests', ['method', 'endpoint'])
REQUEST_LATENCY = Histogram('http_request_duration_seconds', 'Request latency')

@REQUEST_LATENCY.time()
def handle_request():
    REQUEST_COUNT.labels(method='GET', endpoint='/api').inc()
```

---

## Alerting

```yaml
# Prometheus Alert Rule
groups:
- name: api_alerts
  rules:
  - alert: HighErrorRate
    expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.05
    for: 2m
    labels:
      severity: critical
    annotations:
      summary: "Error rate above 5%"
```

---

## ⏱️ الوقت المقدر: 4-6 أسابيع

## مصادر
- [Prometheus Documentation](https://prometheus.io/docs/)
- [Grafana Tutorials](https://grafana.com/tutorials/)
