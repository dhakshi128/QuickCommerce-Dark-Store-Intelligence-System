# 🛒 Quick Commerce Dark Store Intelligence System

<div align="center">

![Databricks](https://img.shields.io/badge/Databricks-FF3621?style=for-the-badge&logo=databricks&logoColor=white)
![PySpark](https://img.shields.io/badge/Apache_Spark-E25A1C?style=for-the-badge&logo=apachespark&logoColor=white)
![Delta Lake](https://img.shields.io/badge/Delta_Lake-0033CC?style=for-the-badge&logo=delta&logoColor=white)
![MLflow](https://img.shields.io/badge/MLflow-0194E2?style=for-the-badge&logo=mlflow&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)

**End-to-end Data & AI Pipeline | Bronze → Silver → Gold | MLflow | Delta Lake | PySpark**

*Databricks × Codebasics × Indian Data Club — 14 Day AI Challenge Capstone*

</div>

---

## 📊 Project Highlights

| Metric | Value |
|--------|-------|
| 📦 Rows Processed | 37M+ |
| 📓 Notebooks | 5 |
| 🤖 ML Models | 3 |
| 🎯 Best AUC-ROC | 0.9999 |
| 👥 Customers Scored | 206,209 |
| 🛍️ Products | 49,687 |

---

## 🎯 Problem Statement

Quick commerce companies operate hundreds of dark stores **without a centralized intelligence system**, leading to:

| Problem | Impact |
|---------|--------|
| 📦 **Inventory Blindness** | No system to predict stockouts before they happen |
| 👤 **Customer Churn** | 68% of customers churn with no early warning system |
| ⏰ **Demand Spikes** | Peak hours (10AM–2PM) handled reactively, not proactively |
| 📊 **No Segmentation** | All customers treated equally — loyal vs at-risk unknown |

> **Solution:** Build an end-to-end Databricks pipeline that **predicts churn**, **segments customers**, and **forecasts demand**.

---

## 🏗️ Architecture Overview

### Medallion Architecture (Bronze → Silver → Gold)

```
┌─────────────────────────────────────────────────────────────────────┐
│                        MEDALLION ARCHITECTURE                        │
├──────────────────┬──────────────────────┬───────────────────────────┤
│   🥉 BRONZE       │      🥈 SILVER        │        🥇 GOLD             │
│   Raw Ingestion  │  Cleaned & Enriched  │    Features & KPIs        │
├──────────────────┼──────────────────────┼───────────────────────────┤
│ orders.csv       │ Nulls handled        │ customer_features         │
│ → Delta          │ Tables joined        │ product_features          │
│                  │                      │                           │
│ products.csv     │ is_weekend added     │ hourly_demand             │
│ → Delta          │ time_of_day bucketed │ churn_predictions         │
│                  │                      │                           │
│ 32M+ rows stored │ 33M+ rows cleaned    │ 206K users scored         │
│ Schema enforced  │ 37M+ enriched        │ ML-ready features         │
└──────────────────┴──────────────────────┴───────────────────────────┘
         ▶ ACID Transactions  ▶ OPTIMIZE + ZORDER  ▶ Time Travel
```

---

## 📁 Repository Structure

```
quick-commerce-dark-store-intelligence/
│
├── 📓 notebooks/
│   ├── 01_bronze_ingestion.py        # Raw data → Delta Lake
│   ├── 02_silver_transformation.py   # Cleaning & enrichment
│   ├── 03_gold_feature_engineering.py# Feature store creation
│   ├── 04_ml_pipeline.py             # Model training & MLflow
│   └── 05_dashboard_queries.py       # Business KPI queries
│
├── 📊 data/
│   ├── orders.csv                    # 3.4M rows
│   ├── order_products__prior.csv     # 32.4M rows
│   ├── order_products__train.csv     # 1.4M rows
│   ├── products.csv                  # 49,688 rows
│   ├── aisles.csv                    # 134 rows
│   └── departments.csv              # 21 rows
│
├── 🤖 models/
│   └── mlflow_registry/              # Registered models (Unity Catalog)
│
├── 📈 dashboard/
│   └── databricks_dashboard.py       # Dashboard creation notebook
│
├── 📄 docs/
│   ├── architecture.md               # Architecture deep-dive
│   └── feature_dictionary.md         # Feature definitions
│
├── .gitignore
├── requirements.txt
└── README.md                         # ← You are here
```

---

## 📦 Dataset

**Instacart Market Basket Dataset — 6 Files**

| File | Rows | Description |
|------|------|-------------|
| `orders.csv` | 3,421,083 | Order metadata (user, time, day) |
| `order_products__prior.csv` | 32,434,489 | Historical product purchases |
| `order_products__train.csv` | 1,384,617 | Training set product purchases |
| `products.csv` | 49,688 | Product catalog with department/aisle |
| `aisles.csv` | 134 | Aisle reference |
| `departments.csv` | 21 | Department reference |

---

## ⚙️ Feature Engineering

| Feature | Description | Domain Logic |
|---------|-------------|--------------|
| `reorder_rate` | Product reorder likelihood | Signals high-value SKUs |
| `avg_days_between_orders` | Customer order frequency | Key churn predictor |
| `avg_basket_size` | Items per order | Engagement metric |
| `is_churning` | Binary churn flag | **>10 days** inactive (not 30 — q-comm reorders every 3–5 days) |
| `time_of_day` | Morning / Afternoon / Evening / Night | Demand segmentation |
| `peak_hour_flag` | High demand window (10AM–2PM) | Staffing & inventory trigger |

---

## 🤖 Machine Learning Pipeline

### Models Trained & Tracked

| # | Model | AUC-ROC | F1 / Sil. | Notes |
|---|-------|---------|-----------|-------|
| 01 | Logistic Regression | 0.9984 | 0.9762 | Baseline classifier |
| 02 | Random Forest (200 trees) | **0.9999** | **0.9979** | ✅ Production model |
| 03 | KMeans Clustering (k=3) | — | 0.8327* | Customer segmentation |

> *Silhouette score for KMeans. All models tracked in **MLflow**. Best model registered to **Unity Catalog**.*

### Customer Segments (KMeans)

```
🟢 Loyal       →   4.2%  (  8,661 customers) — High frequency, high basket
🟡 Occasional  →  18.2%  ( 37,530 customers) — Moderate engagement
🔴 At-Risk     →  77.6%  (141,148 customers) — >10 days inactive
```

---

## 📊 MLflow Experiment Tracking

```
Experiment:   /quick_commerce_churn
Total Runs:   5 runs logged
Params:       model, numTrees, maxDepth
Metrics:      auc_roc, f1_score
Artifacts:    Feature importances
Registered:   quick_commerce_churn_model  (v1 — Unity Catalog)
```

### Top Feature Importances (Tuned Random Forest)

| Feature | Importance |
|---------|-----------|
| `avg_days_between_orders` | **90.2%** |
| `max_order_number` | 7.1% |
| `total_orders` | 1.6% |

---

## 🔷 Delta Lake Implementation

| Feature | Layer | Description |
|---------|-------|-------------|
| 🔒 ACID Transactions | Bronze | Safe concurrent writes across multiple data streams |
| 📋 Schema Enforcement | Bronze | Malformed records rejected via `rlike` type filter |
| ⚡ OPTIMIZE + ZORDER | Bronze/Silver | Compacted small files; ZORDERed on `user_id` & `product_id` |
| 🕐 Time Travel | Gold | `DESCRIBE HISTORY` — query previous churn prediction states |
| 🏗️ Delta Tables | All | All 9 tables stored as Delta — not parquet or CSV |
| 🔄 Overwrite Mode | All | `mode('overwrite')` — idempotent, safe to re-run anytime |

---

## 📈 Business Dashboard Insights

| KPI | Value | Insight |
|-----|-------|---------|
| ⚠️ Churn Rate | **68.45%** | 141,148 at-risk customers |
| ⏰ Peak Hour | **10 AM** | 288,418 orders at peak |
| 🥬 Top Department | **Produce** | 9.8M orders, 41% reorder rate |
| 🌯 Top SKU Reorder | **Raw Veggie Wrappers** | 94% reorder rate |

---

## 💼 Business Impact

| Team | Action | Outcome |
|------|--------|---------|
| **Ops Team** | Staff dark stores 2× during 10AM–2PM | ✅ Reduce SLA breaches |
| **Category Team** | Never stockout Raw Veggie Wrappers (94% reorder) | ✅ Zero lost sales |
| **CRM Team** | Target 141K churning customers with retention offers | ✅ Recover lost revenue |
| **Strategy Team** | Convert Occasional → Loyal segment | ✅ Grow 4% → 10% loyal base |

> *"This system gives Zepto/Blinkit the intelligence layer to act on data — not gut feel."*

---

## 🚀 Getting Started

### Prerequisites

- Databricks workspace (Community or paid tier)
- Databricks Runtime 13.x+ (with ML)
- Python 3.9+
- Access to Instacart dataset (see [Data Setup](#data-setup))

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/<your-username>/quick-commerce-dark-store-intelligence.git
cd quick-commerce-dark-store-intelligence

# 2. Install Python dependencies
pip install -r requirements.txt

# 3. Upload notebooks to Databricks
# See GITHUB_SETUP.md for full CI/CD instructions
```

### Data Setup

```python
# In your Databricks notebook, set the data path
DATA_PATH = "/FileStore/quick_commerce/"

# Upload Instacart CSVs to DBFS
# Via UI: Data → DBFS → Upload → /FileStore/quick_commerce/
# Or via CLI:
# databricks fs cp ./data/ dbfs:/FileStore/quick_commerce/ --recursive
```

### Running the Pipeline

Execute notebooks **in order**:

```
1. 01_bronze_ingestion.py       → Ingest raw CSVs to Delta
2. 02_silver_transformation.py  → Clean & enrich
3. 03_gold_feature_engineering.py → Build feature tables
4. 04_ml_pipeline.py            → Train, log, register models
5. 05_dashboard_queries.py      → Generate KPI views
```

---

## 🔧 Requirements

```
pyspark>=3.3.0
mlflow>=2.0.0
delta-spark>=2.3.0
pandas>=1.5.0
scikit-learn>=1.1.0
matplotlib>=3.6.0
seaborn>=0.12.0
```

---

## 🤝 Contributing

1. Fork the repository
2. Create your feature branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m 'Add: your feature description'`
4. Push to the branch: `git push origin feature/your-feature`
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgements

- **Instacart** for the open-source Market Basket dataset
- **Databricks** for the platform and AI challenge
- **Codebasics** & **Indian Data Club** for the 14-Day AI Challenge framework

---

<div align="center">

Built with ❤️ using **Databricks** • **PySpark** • **Delta Lake** • **MLflow** • **Python**

*Databricks × Codebasics × Indian Data Club — 14 Day AI Challenge Capstone*

</div>
