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

