# 🚔 Irish Crime Intelligence Platform — Garda Station Analytics

> An end-to-end data analytics platform analysing 22 years of recorded crime across all 564 Garda stations in Ireland — from raw CSO API ingestion to risk scoring, strategy clustering, SQL warehouse, Power BI dashboard, forecasting, and operational decision simulation.

---

## 📌 Project Overview

Ireland's 564 Garda stations handle vastly different crime workloads. A small number of urban stations account for the majority of national incidents, while hundreds of stations across the country show emerging growth patterns that risk being overlooked in national-level reporting.

This project builds a complete analytical intelligence platform that:
- Ingests 22 years of recorded crime data (2003–2024) directly from the CSO PxStat API
- Engineers a composite Risk Index to rank all 564 Garda stations by operational pressure
- Segments stations into 4 strategic profiles using unsupervised machine learning
- Builds a production-style PostgreSQL star schema with 15 validated analytical queries
- Delivers insights through an interactive Power BI dashboard
- Forecasts station and cluster-level crime volume for 2025–2026
- Simulates resource prioritisation scenarios to support Garda leadership decisions

**All data is sourced from the official CSO PxStat API (dataset CJA07) — no Kaggle, no synthetic data.**

---

## 🎯 Target Stakeholders

| Stakeholder | Use Case |
|---|---|
| Garda Divisional Commanders | Identify which stations need urgent operational attention |
| National Operations Planning | Allocate resources across 4 strategic station profiles |
| Crime Analysts | Station-level risk scoring, growth detection, offence profiling |
| Policy Makers | Evidence base for resource investment and strategic planning |
| Researchers | Clean, structured 22-year Irish crime dataset by station |

---

## 📊 Data Source

| Source | Dataset | Coverage |
|---|---|---|
| CSO PxStat API | CJA07 — Recorded Crime Offences by Garda Station | 2003–2024 |

**API endpoint:** `https://ws.cso.ie/public/api.restful/PxStat.Data.Cube_API.PxAPIv1/en/20/RC/CJA07`

Raw data grain: **Year × Garda Station × Offence Group** — 173,712 rows across 564 stations, 14 offence groups, and 22 years.

---

## 🏗️ Project Architecture

```
File 1 — Data Ingestion & Cleaning          (Python + CSO PxStat API)
File 2 — Risk Metrics & Geographic Profile  (Python + Pandas)
File 3 — Station Strategy Clustering       (Python + Scikit-learn)
File 4 — SQL Analytical Warehouse          (PostgreSQL + SQLAlchemy)
File 5 — Power BI Dashboard                (Power BI Desktop)
File 6 — Forecasting & Early Warning       (Prophet + ARIMA)
File 7 — Risk Index v2 & Scenario Simulation (Python + Decision Science)
```

---

## 📁 Repository Structure

```
irish-crime-intelligence/
├── data/
│   ├── raw/                              # Original API pull (not tracked in Git)
│   └── processed/                        # Cleaned outputs from each file
├── notebooks/
│   ├── 01_Data_Ingestion_And_Exploration.ipynb
│   ├── 02_Risk_Metrics_and_Geographic_Profile.ipynb
│   ├── 03_Station_Strategy_Clustering.ipynb
│   ├── 04_SQL_Warehouse_Business_Queries.ipynb
│   ├── 05_PowerBI_Exports.ipynb
│   ├── 06_Forecasting_Early_Warning.ipynb
│   └── 07_Risk_Index_Simulation.ipynb
├── dashboard/
│   └── garda_crime_dashboard.pbix
├── exports/
│   └── pbix_*.csv                        # Power BI ready CSVs
├── requirements.txt
├── PROGRESS.md
└── README.md
```

---

## 🔧 Technology Stack

| Layer | Tools |
|---|---|
| Data Ingestion | Python, Requests, CSO PxStat REST API |
| Data Processing | Python, Pandas, NumPy |
| Visualisation | Matplotlib, Seaborn |
| Database | PostgreSQL, SQLAlchemy, psycopg2 |
| Machine Learning | Scikit-learn (KMeans, PCA, StandardScaler) |
| Statistical Validation | SciPy (ANOVA, Chi-square) |
| Forecasting | Prophet, ARIMA (statsmodels, pmdarima) |
| BI Dashboard | Power BI Desktop |
| Version Control | Git, GitHub |

---

## 📈 Key Insights *(Updated as project progresses)*

> *(This section will be populated as analysis is completed)*

---

## 🚀 How to Run

### 1. Clone the repository
```bash
git clone https://github.com/manichandu1297/irish-crime-intelligence.git
cd irish-crime-intelligence
```

### 2. Install dependencies
```bash
pip install -r requirements.txt
```

### 3. Run notebooks in order
Open notebooks in Google Colab or locally, running from 01 to 07. Each notebook saves output CSVs that are picked up by the next file.

### 4. File 4 requires PostgreSQL
File 4 runs on Google Colab with a local PostgreSQL instance. The notebook handles installation and setup automatically.

---

## 📋 Requirements

```
pandas
numpy
matplotlib
seaborn
scikit-learn
scipy
statsmodels
pmdarima
prophet
sqlalchemy
psycopg2-binary
requests
```

---

## 📌 Project Status

| File | Description | Status |
|---|---|---|
| File 1 | Data Ingestion & Cleaning | 🔄 In Progress |
| File 2 | Risk Metrics & Geographic Profile | ⏳ Pending |
| File 3 | Station Strategy Clustering | ⏳ Pending |
| File 4 | SQL Analytical Warehouse | ⏳ Pending |
| File 5 | Power BI Dashboard | ⏳ Pending |
| File 6 | Forecasting & Early Warning | ⏳ Pending |
| File 7 | Risk Index v2 & Scenario Simulation | ⏳ Pending |

---

## 👤 Author

**Mani Chandu**
MSc Data Analytics | Based in Ireland
[LinkedIn](#) | [GitHub](https://github.com/manichandu1297)

> Built as a portfolio project demonstrating end-to-end data engineering, analytics, machine learning, and decision science using 22 years of real Irish government crime data.

---

## 📄 Licence

This project uses publicly available data from the CSO PxStat public API (dataset CJA07).
All analysis and code is original work. Data sources are credited throughout.
