# 01 — الإحصاء لعلم البيانات

---

## الإحصاء الوصفي

```python
import pandas as pd
import numpy as np

df = pd.read_csv("data.csv")

# المقاييس الأساسية
df.describe()           # الملخص الإحصائي
df['col'].mean()        # المتوسط
df['col'].median()      # الوسيط
df['col'].std()         # الانحراف المعياري
df['col'].skew()        # الالتواء
df['col'].kurt()        # التفلطح
```

## الإحصاء الاستدلالي

```python
from scipy import stats

# Hypothesis Testing
t_stat, p_value = stats.ttest_ind(group_a, group_b)
if p_value < 0.05:
    print("الفرق دال إحصائيًا")

# Chi-square test (للمتغيرات الفئوية)
chi2, p, dof, expected = stats.chi2_contingency(contingency_table)

# Correlation
pearson_r, p_value = stats.pearsonr(x, y)
spearman_r, p_value = stats.spearmanr(x, y)
```

## التوزيعات الاحتمالية

```
Normal Distribution   ← أساس كثير من الاختبارات
Binomial              ← نتائج ثنائية (نجاح/فشل)
Poisson               ← عد الأحداث في فترة زمنية
Exponential           ← الوقت بين الأحداث
```

## A/B Testing

```python
# حساب حجم العينة المطلوب
from statsmodels.stats.power import tt_ind_solve_power
n = tt_ind_solve_power(effect_size=0.2, alpha=0.05, power=0.8)

# تحليل النتائج
from scipy.stats import chi2_contingency
chi2, p_val, _, _ = chi2_contingency([[control_conv, control_not],
                                       [test_conv,    test_not]])
```

## ⏱️ الوقت المقدر: 6-8 أسابيع

## مصادر
- [StatQuest with Josh Starmer](https://www.youtube.com/@statquest) ← مجاني وممتاز
- [Khan Academy Statistics](https://www.khanacademy.org/math/statistics-probability)
