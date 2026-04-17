# 02 — Machine Learning الكلاسيكي

---

## Supervised Learning

```python
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import train_test_split
from sklearn.metrics import classification_report

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

# الخوارزميات الرئيسية
# Linear/Logistic Regression   ← أبسطها، مفهوم أساسي
# Decision Trees               ← قابل للتفسير
# Random Forest                ← قوي، مقاوم للـ Overfitting
# Gradient Boosting (XGBoost)  ← الأفضل للبيانات الجدولية
# SVM                          ← للـ High-dimensional data
# KNN                          ← بسيط، بطيء على البيانات الكبيرة

model = RandomForestClassifier(n_estimators=100)
model.fit(X_train, y_train)
print(classification_report(y_test, model.predict(X_test)))
```

## Unsupervised Learning

```python
# Clustering
from sklearn.cluster import KMeans
kmeans = KMeans(n_clusters=3)
labels = kmeans.fit_predict(X)

# Dimensionality Reduction
from sklearn.decomposition import PCA
pca = PCA(n_components=2)
X_2d = pca.fit_transform(X)
```

---

## مفاهيم حرجة

```
Bias-Variance Tradeoff:
High Bias   → Underfitting → نموذج بسيط جدًا
High Variance → Overfitting → نموذج معقد جدًا

الحل:
- Cross-validation
- Regularization (L1/Lasso, L2/Ridge)
- More data
- Feature selection

Feature Engineering:
- الأهم في الـ ML الكلاسيكي
- Normalization / Standardization
- Encoding categorical variables
- Handling missing values
- Feature selection
```

---

## تقييم النماذج

```python
# Classification
Accuracy, Precision, Recall, F1-score
ROC-AUC curve
Confusion Matrix

# Regression
MAE, MSE, RMSE, R²

# Cross-validation
from sklearn.model_selection import cross_val_score
scores = cross_val_score(model, X, y, cv=5)
```

---

## ⏱️ الوقت المقدر: 2-3 أشهر

## مصادر
- [Scikit-learn Documentation](https://scikit-learn.org/stable/)
- [Kaggle Learn](https://www.kaggle.com/learn) ← مجاني وعملي
- كتاب: *Hands-On Machine Learning* - Aurélien Géron
