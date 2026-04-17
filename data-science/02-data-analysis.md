# 02 — تحليل البيانات

---

## Pandas — الأداة الرئيسية

```python
import pandas as pd

df = pd.read_csv("sales.csv")

# استكشاف البيانات
df.head(), df.info(), df.describe()
df.isnull().sum()              # القيم المفقودة
df.duplicated().sum()          # السجلات المكررة

# تنظيف البيانات
df.dropna(subset=['price'])    # حذف الصفوف الناقصة
df['price'].fillna(df['price'].median(), inplace=True)
df.drop_duplicates(inplace=True)
df['date'] = pd.to_datetime(df['date'])

# التحويل
df['category'] = df['category'].str.lower().str.strip()
df['revenue'] = df['price'] * df['quantity']

# التجميع والتحليل
monthly = df.groupby(df['date'].dt.month)['revenue'].agg(['sum','mean','count'])

# Merge
result = pd.merge(orders, customers, on='customer_id', how='left')

# Pivot Table
pivot = df.pivot_table(values='revenue', index='region', columns='category', aggfunc='sum')
```

---

## SQL للتحليل

```sql
-- Window Functions — الأهم في التحليل
SELECT
    date,
    revenue,
    SUM(revenue) OVER (ORDER BY date) as cumulative_revenue,
    AVG(revenue) OVER (PARTITION BY region ORDER BY date ROWS 6 PRECEDING) as moving_avg,
    RANK() OVER (PARTITION BY category ORDER BY revenue DESC) as rank
FROM sales;

-- Cohort Analysis
WITH cohorts AS (
    SELECT user_id,
           DATE_TRUNC('month', MIN(created_at)) as cohort_month
    FROM orders
    GROUP BY user_id
)
SELECT cohort_month,
       DATE_TRUNC('month', o.created_at) as order_month,
       COUNT(DISTINCT o.user_id) as users
FROM orders o
JOIN cohorts c ON o.user_id = c.user_id
GROUP BY 1, 2;
```

---

## EDA (Exploratory Data Analysis)

```python
# خطوات منهجية
# 1. شكل البيانات وأنواع المتغيرات
# 2. القيم المفقودة والمكررة
# 3. توزيع المتغيرات (Histograms)
# 4. العلاقات بين المتغيرات (Correlation, Scatter)
# 5. Outliers (Box plots)
# 6. الأنماط الزمنية إن وجدت

import seaborn as sns
import matplotlib.pyplot as plt

sns.heatmap(df.corr(), annot=True, cmap='coolwarm')
sns.pairplot(df[['price', 'revenue', 'quantity']])
```

## ⏱️ الوقت المقدر: 2 أشهر

## مصادر
- [Pandas Documentation](https://pandas.pydata.org/docs/)
- [Kaggle Datasets](https://www.kaggle.com/datasets) ← للتطبيق العملي
