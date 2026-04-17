# 04 — Data Visualization

---

## Python Visualization

```python
import matplotlib.pyplot as plt
import seaborn as sns
import plotly.express as px

# Static - Matplotlib/Seaborn
fig, axes = plt.subplots(1, 2, figsize=(12, 5))
sns.histplot(df['revenue'], ax=axes[0])
sns.boxplot(x='category', y='revenue', data=df, ax=axes[1])
plt.tight_layout()

# Interactive - Plotly
fig = px.line(df, x='date', y='revenue', color='region', title='Revenue Over Time')
fig.show()

# Dashboard - Streamlit
import streamlit as st
st.title("Sales Dashboard")
st.plotly_chart(fig)
st.dataframe(df.head(100))
```

---

## مبادئ التصميم البصري

```
1. اختر النوع الصحيح:
   - Comparison    → Bar chart
   - Trend         → Line chart
   - Distribution  → Histogram, Box plot
   - Relationship  → Scatter plot
   - Part-of-whole → Pie (للقيم القليلة فقط)
   - Geographic    → Map

2. قلل الـ Noise:
   - أزل الـ gridlines غير الضرورية
   - ألوان محدودة (3-5 على الأكثر)
   - عنوان واضح يصف الـ Insight

3. Highlight الـ Insight:
   - لوّن النقطة المهمة بلون مختلف
   - أضف annotation للـ outliers
```

---

## BI Tools

```
Tableau    ← الأقوى، مدفوع
Power BI   ← Microsoft، شائع في الشركات
Looker     ← Google Cloud
Metabase   ← مفتوح المصدر، مجاني
Superset   ← Apache، مجاني، للمطورين
```

---

## ⏱️ الوقت المقدر: 4-6 أسابيع

## مصادر
- [Storytelling with Data](https://www.storytellingwithdata.com/) ← كتاب + مدونة
- [Streamlit Documentation](https://docs.streamlit.io/)
