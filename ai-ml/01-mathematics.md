# 01 — الرياضيات للذكاء الاصطناعي

---

## 1. الجبر الخطي (Linear Algebra)

```python
import numpy as np

# Vectors & Matrices
v = np.array([1, 2, 3])
A = np.array([[1,2],[3,4]])

# العمليات الأساسية
A @ v              # Matrix multiplication
A.T                # Transpose
np.linalg.inv(A)   # Inverse
np.linalg.det(A)   # Determinant

# المفاهيم الحرجة للـ ML
# - Eigenvalues/Eigenvectors   → PCA
# - SVD (Singular Value Decomposition) → Dimensionality Reduction
# - Dot Product  → Similarity, Neural Networks
# - Norms (L1, L2) → Regularization
```

---

## 2. حساب التفاضل والتكامل (Calculus)

```
المشتقات (Derivatives):
- أساس الـ Gradient Descent
- Chain Rule → Backpropagation

Gradient:
- متجه يشير لاتجاه أكبر زيادة في الدالة
- في ML نريد الاتجاه المعاكس (تقليل الـ Loss)

الصيغة الأساسية:
θ = θ - α * ∇J(θ)
حيث α = learning rate، J = دالة الخسارة
```

---

## 3. الإحصاء والاحتمالات

```python
# التوزيعات المهمة
# Normal (Gaussian)  - افتراض كثير من الخوارزميات
# Binomial           - التصنيف الثنائي
# Poisson            - عد الأحداث

# المقاييس
mean     = np.mean(data)
std      = np.std(data)
variance = np.var(data)

# Bayes Theorem
# P(A|B) = P(B|A) * P(A) / P(B)
# أساس: Naive Bayes, Bayesian Networks

# مفاهيم حرجة
# - Probability Distributions
# - Hypothesis Testing
# - Confidence Intervals
# - Correlation vs Causation
# - Maximum Likelihood Estimation (MLE)
```

---

## 4. Optimization

```
Gradient Descent وأنواعه:
- Batch GD         - بطيء، دقيق
- Stochastic GD    - سريع، ضوضاء
- Mini-batch GD    - التوازن الأمثل ← المستخدم في الواقع

المحسّنات الحديثة:
- Adam    ← الأكثر استخدامًا
- RMSProp
- AdaGrad
```

---

## ⏱️ الوقت المقدر: 2-3 أشهر

## مصادر
- [3Blue1Brown - Linear Algebra](https://www.youtube.com/playlist?list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab) ← مجاني ومرئي
- [Khan Academy Statistics](https://www.khanacademy.org/math/statistics-probability)
- كتاب: *Mathematics for Machine Learning* - مجاني: [mml-book.github.io](https://mml-book.github.io)
