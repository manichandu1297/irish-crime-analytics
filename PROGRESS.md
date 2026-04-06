# 📅 Project Progress Log

## Day 1 — 10 March 2026

**Phase:** Project Setup & Planning

**What I did:**
- Read and understood the full project scope
- Defined aim, objectives, stakeholders, and deliverables across 7 files
- Researched the CSO PxStat API and confirmed CJA07 as the primary dataset
- Created GitHub repository with README and .gitignore
- Created project progress tracker
- Downloaded CJA07 from CSO PxStat API — 173,712 rows across 564 stations, 14 offence groups, 22 years (2003–2024)
- Ran initial inspection — shape, nulls, duplicates, data types, unique value counts

**Key decisions made:**
- Pulled data directly via the CSO PxStat REST API — no manual CSV download, keeps the pipeline reproducible
- Used `station_code` as the primary station identifier rather than name — 3 station names (Blackrock, Milford, Newport) appear in multiple counties so names are not unique
- Confirmed no duplicates at the Year × Station × Offence level
- Dropped `Statistic Label` and `UNIT` columns — single value columns with no analytical use

**Outcome:** Project foundation established. Repository live on GitHub. Dataset loaded and understood.

## Day 2 — 11 March 2026

**Phase:** File 1 — Data Ingestion & Cleaning

**What I did:**
- Built the data ingestion pipeline pulling directly from the CSO PxStat REST API
- Parsed the raw "Garda Station" string into three separate columns — station_code, station_name, station_division
- Parsed the "Type of Offence" string into offence_group_code and offence_group_name
- Ran shape, null, duplicate and value count checks on the raw API response
- Investigated the 3 station names that appear in multiple counties — Blackrock, Milford, Newport
- Confirmed station_code is unique across all 564 stations even where names overlap

**Key decisions made:**
- Used station_code as the primary key rather than station name — 3 station names appear in multiple counties so name alone is not a reliable identifier
- Pulled data via API rather than downloading a CSV manually — keeps the pipeline reproducible and anyone can rerun it
- Kept all 14 offence groups — no filtering at ingestion stage, analysis files will do any filtering needed

**Outcome:** Raw API data parsed and structured. Station and offence fields cleaned and split. Ready for cleaning and validation pass tomorrow.

---

## Day 3 — 12 March 2026

**Phase:** File 1 — Data Ingestion & Cleaning (completion)

**What I did:**
- Completed all cleaning and validation checks on the structured dataset
- Confirmed no duplicates at the Year × Station × Offence level
- Confirmed no nulls in any column after parsing
- Dropped Statistic Label and UNIT columns — both are single value columns with no analytical use
- Added sense check plots — incident trend over time nationally and by offence group
- Saved final output as `ireland_crime_structured_dataset.csv` — 173,712 rows, 7 columns
- Updated README to mark File 1 as complete

**Key decisions made:**
- Kept incidents value of 0 in the dataset — confirmed as genuine zero reporting, not missing data
- Did not filter by year at this stage — kept full 2003–2024 range for downstream files to decide what they need

**Outcome:** File 1 complete. Clean structured dataset saved and ready for File 2 risk metrics.

---
## Day 4 — 16 March 2026

**Phase:** File 2 — Risk Metrics & Geographic Profile

**What I did:**
- Started building the risk metrics notebook
- Added county mapping from division names — mapped 564 stations across 21 analytical units
- Defined two comparison windows — historical baseline 2015–2019 and recent period 2020–2024
- Calculated total incidents for each station in both windows
- Investigated stations with zero historical incidents — confirmed as genuine new activity, not missing data

**Key decisions made:**
- Used division names as the geographic unit rather than the 26 counties — CSO data is structured by Garda division not county boundaries
- Chose 2015–2019 as historical baseline and 2020–2024 as recent window — gives a clean pre-COVID vs post-COVID comparison
- Added new_activity_flag for stations that had no incidents in the historical window but do in the recent window

**Outcome:** Base incident aggregations done. County mapping complete. Ready to build the risk formula tomorrow.

---
## Day 5 — 17 March 2026

**Phase:** File 2 — Risk Metrics & Geographic Profile

**What I did:**
- Built the growth rate calculation — recent vs historical incident volumes per station
- Investigated outlier growth rates — some stations showed 10x or 20x growth due to very small historical base
- Applied growth cap at 10x to stop small-base stations dominating the risk index
- Built the Risk Index v1 formula — 0.6 × normalised recent volume + 0.4 × normalised growth rate
- Min-max normalised both components so they are on the same scale before combining

**Key decisions made:**
- Capped growth ratio at 10x — a station going from 2 to 25 incidents is not genuinely 12x riskier than a station going from 500 to 1000, the cap prevents distortion
- Used min-max normalisation rather than standard scaling — keeps values between 0 and 1 which makes the weighted formula intuitive
- Weighted recent volume higher than growth (0.6 vs 0.4) — current workload is a more reliable signal than growth rate alone

**Outcome:** Risk Index v1 built and ranked across all 564 stations. Growth capping logic validated. Ready for offence-level breakdown and final outputs tomorrow.

---
## Day 6 — 18 March 2026

**Phase:** File 2 — Risk Metrics & Geographic Profile (completion)

**What I did:**
- Built station × offence level metrics — growth rate and volume for each of the 14 offence groups per station
- Added emerging offence flag — stations where a new offence group appeared in recent window that was absent historically
- Generated sense check plots — top 20 stations by risk index, geographic distribution by county
- Saved three output files — station_risk_metrics_full.csv, station_offence_metrics.csv, station_risk_metrics.csv
- Pushed File 2 notebook to GitHub
- Updated README to mark File 2 as complete

**Key decisions made:**
- Kept station_risk_metrics_full.csv as the master station file — 564 rows, one per station, used by all downstream files
- Saved a separate top 20 summary CSV for quick reference without loading the full file

**Outcome:** File 2 complete. Risk Index v1 scores and ranks available for all 564 stations. Offence-level breakdown ready for File 3 clustering.

---
## Day 7 — 24 March 2026

**Phase:** File 3 — Station Strategy Clustering

**What I did:**
- Started building the clustering notebook
- Aggregated station × offence metrics into a single station-level feature matrix — 564 rows, one per station
- Engineered 9 features: total_volume_recent, station_growth_unweighted, hhi, growth_volatility, emerging_count, num_active_offences, share_growth_positive, risk_intensity
- Checked feature distributions — identified 3 heavily skewed features and applied log transformation
- Investigated correlation between features — found top_offence_share and HHI were nearly identical (r=0.96)

**Key decisions made:**
- Dropped top_offence_share — r=0.96 with HHI means they carry almost identical information, keeping both would double-count concentration in the model
- Log-transformed 3 skewed features — clustering algorithms are sensitive to scale and outliers, transformation brings them in line
- Kept urban_station_flag out of the feature matrix — used for validation only, not as a clustering input

**Outcome:** Feature matrix ready. Correlation issues resolved. Ready to run clustering and select k tomorrow.

---
## Day 8 — 25 March 2026

**Phase:** File 3 — Station Strategy Clustering

**What I did:**
- Ran K-Means clustering for k=2 through k=10
- Plotted silhouette scores and inertia to find the right number of clusters
- Tested k=4 and k=7 — both gave similar silhouette scores around 0.20
- Chose k=4 over k=7 — more interpretable for a planning audience
- Labelled the 4 clusters based on their feature profiles
- Validated clusters using ANOVA and chi-square tests

**Key decisions made:**
- Selected k=4 — same silhouette as k=7 but 4 clusters are much easier to explain to a non-technical planner. More clusters does not always mean better analysis
- Silhouette of 0.20 is low but expected — real-world station data does not form clean separate blobs, there is genuine overlap between station types
- Named clusters descriptively — National Workhorses, Emerging Growth, Low-Volume Rural, Specialised Baseline — so the labels carry meaning without needing to read the methodology

**Outcome:** 4 clusters selected and validated. ANOVA p<0.001, chi-square p<0.001 — cluster separation is statistically significant despite the modest silhouette score.

---
## Day 9 — 27 March 2026

**Phase:** File 3 — Station Strategy Clustering (completion) + File 4 start

**What I did:**
- Generated cluster profile visualisations — radar chart per cluster, feature distribution boxplots
- Saved Garda_Station_Strategy_Master.csv — 564 rows, 11 columns including cluster label, county, urban flag, HHI
- Pushed File 3 notebook to GitHub and updated README
- Started File 4 — SQL Analytical Warehouse
- Set up PostgreSQL connection using SQLAlchemy
- Designed star schema — 4 dimension tables and 2 fact tables
- Created and loaded all dimension tables — dim_station, dim_offence, dim_division, dim_year

**Key decisions made:**
- Used star schema rather than a flat table — makes the SQL queries cleaner and mirrors how a real analytics warehouse would be structured
- Used SQLAlchemy for the database connection — keeps the Python-to-SQL pipeline clean and avoids raw psycopg2 connection strings in the notebook

**Outcome:** File 3 complete. Star schema designed and dimension tables loaded. Ready to load fact tables and write analytical queries tomorrow.

---
## Day 10 — 28 March 2026

**Phase:** File 4 — SQL Analytical Warehouse (completion)

**What I did:**
- Loaded fact_crime_incidents — 173,712 rows
- Loaded fact_station_offence_window — 7,896 rows covering recent and historical aggregations
- Created 7 indexes for query performance
- Built 5 reporting views covering top stations, cluster summaries, offence trends, county rankings, and year-on-year comparisons
- Wrote 15 analytical queries covering resource allocation, emerging hotspots, offence concentration, and geographic distribution
- Pushed File 4 notebook to GitHub and updated README to mark Files 3 and 4 as complete

**Key decisions made:**
- Built views rather than saving query results as tables — views stay in sync if the underlying data changes and are better practice for a reporting layer
- Added 7 indexes on commonly joined and filtered columns — without indexes, queries on 173,712 rows slow down significantly
- Wrote queries in plain SQL with comments explaining the business question each one answers — makes the notebook readable to someone without a data background

**Outcome:** File 4 complete. Full star schema warehouse live with 6 tables, 7 indexes, 5 views and 15 analytical queries. Pipeline is now 4 files deep end to end.

