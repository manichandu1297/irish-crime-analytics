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

