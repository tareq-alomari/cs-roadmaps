# 03 — Data Engineering

---

## Data Pipelines

```python
# Apache Airflow — جدولة الـ Pipelines
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime

def extract(): pass
def transform(): pass
def load(): pass

with DAG('etl_pipeline', start_date=datetime(2024,1,1), schedule='@daily') as dag:
    t1 = PythonOperator(task_id='extract', python_callable=extract)
    t2 = PythonOperator(task_id='transform', python_callable=transform)
    t3 = PythonOperator(task_id='load', python_callable=load)
    t1 >> t2 >> t3
```

---

## Data Warehousing

```sql
-- Star Schema
-- Fact Table (المقاييس)
CREATE TABLE fact_sales (
    sale_id BIGINT,
    date_key INT,        -- FK
    product_key INT,     -- FK
    customer_key INT,    -- FK
    amount DECIMAL(10,2),
    quantity INT
);

-- Dimension Tables (السياق)
CREATE TABLE dim_date (date_key INT, date DATE, month INT, year INT);
CREATE TABLE dim_product (product_key INT, name TEXT, category TEXT);
```

```
أدوات الـ Data Warehouse:
- Snowflake      ← Cloud-native، الأكثر انتشارًا
- BigQuery       ← Google Cloud
- Redshift       ← AWS
- DuckDB         ← للتحليل المحلي (مجاني، سريع جدًا)
```

---

## Spark للبيانات الضخمة

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, sum

spark = SparkSession.builder.appName("ETL").getOrCreate()

df = spark.read.parquet("s3://bucket/data/")
result = df.groupBy("category").agg(sum("revenue").alias("total"))
result.write.parquet("s3://bucket/output/")
```

---

## Stack الحديث (Modern Data Stack)

```
Ingestion:  Fivetran, Airbyte (مجاني)
Storage:    Snowflake, BigQuery, Delta Lake
Transform:  dbt (data build tool) ← الأهم
Orchestration: Airflow, Prefect
BI:         Looker, Metabase (مجاني)
```

```sql
-- dbt model مثال
-- models/marts/revenue_by_region.sql
SELECT
    region,
    SUM(amount) as total_revenue,
    COUNT(DISTINCT customer_id) as customers
FROM {{ ref('fct_orders') }}
WHERE status = 'completed'
GROUP BY 1
```

---

## ⏱️ الوقت المقدر: 3 أشهر

## مصادر
- [dbt Learn](https://courses.getdbt.com/) ← مجاني
- [Data Engineering Zoomcamp](https://github.com/DataTalksClub/data-engineering-zoomcamp) ← مجاني كامل
