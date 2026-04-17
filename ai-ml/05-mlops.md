# 05 — MLOps

---

## ما هو MLOps؟

DevOps للـ Machine Learning — نقل النماذج من notebook إلى إنتاج وإدارتها.

---

## دورة حياة النموذج

```
Data Collection → Preprocessing → Training → Evaluation
      ↑                                            ↓
   Retraining ← Monitoring ← Deployment ← Packaging
```

---

## الأدوات الأساسية

```python
# تتبع التجارب
import mlflow

with mlflow.start_run():
    mlflow.log_param("learning_rate", 0.01)
    mlflow.log_metric("accuracy", 0.95)
    mlflow.sklearn.log_model(model, "model")

# Feature Store
# Feast, Tecton, Hopsworks

# Model Registry
# MLflow, Weights & Biases, Neptune

# Data Versioning
# DVC (Data Version Control)
dvc init
dvc add data/dataset.csv
dvc push
```

---

## Serving النماذج

```python
# FastAPI + Model Serving
from fastapi import FastAPI
import joblib

app = FastAPI()
model = joblib.load("model.pkl")

@app.post("/predict")
def predict(data: dict):
    features = [[data["feature1"], data["feature2"]]]
    prediction = model.predict(features)
    return {"prediction": int(prediction[0])}
```

```bash
# BentoML
bentoml serve service:svc --port 3000

# TorchServe (PyTorch)
torchserve --start --model-store model_store --models mymodel=model.mar

# Triton Inference Server (NVIDIA) - للـ Production الثقيل
```

---

## Monitoring في الإنتاج

```
مشاكل يجب رصدها:
- Data Drift     ← البيانات الجديدة تختلف عن بيانات التدريب
- Model Drift    ← دقة النموذج تتراجع مع الوقت
- Data Quality   ← قيم مفقودة، outliers

أدوات:
- Evidently AI   ← مجاني، ممتاز
- WhyLabs
- Arize AI
```

---

## ⏱️ الوقت المقدر: 2 أشهر

## مصادر
- [MLflow Documentation](https://mlflow.org/docs/latest/)
- [Made With ML](https://madewithml.com/) ← مجاني، شامل جدًا
- [Full Stack Deep Learning](https://fullstackdeeplearning.com/)
