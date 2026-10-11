# Indian Patent Grants Analytics (2016–2023)

### An end-to-end Data Analytics project — Excel (Power Query, Power Pivot) + Power BI (DAX, Star Schema)

---

## 1. Business Problem

India's patent ecosystem has expanded rapidly, but stakeholders — IP strategists, law firms, corporate patent departments, and policy analysts — lack an accessible, consolidated view of grant activity.

**Core questions this project answers:**

1. How has the volume of granted patents in India changed from 2016 to 2023?
2. Which Intellectual Property Offices (IPOs) carry the heaviest grant workload, and is it evenly distributed?
3. Which technology domains dominate Indian patent grants?
4. How long does a patent take to move from filing to grant (pendency), and is the average representative of the typical case?

**Why it matters:** Grant pendency and IPO workload directly affect prosecution strategy, filing-location decisions, and IP portfolio planning. Patent term is 20 years from filing — every year spent awaiting grant reduces the enforceable commercial life of an asset.

**Outcome:** A cleaned, validated dataset of 151,439 granted patents and two interactive dashboards (Excel + Power BI) delivering answers to all four questions.

---

## 2. Dataset

| Attribute           | Detail                                                                                                                                                                                                        |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Source              | Kaggle (Silver-badged dataset) — granted patents in India                                                                                                                                                     |
| Files               | 8 CSV files, one per year (2016–2023)                                                                                                                                                                         |
| Raw appended rows   | ~153,074                                                                                                                                                                                                      |
| Clean analysis base | 151,439                                                                                                                                                                                                       |
| Records excluded    | 1,635 (structurally corrupted, ~1.07%)                                                                                                                                                                        |
| Fields              | Publication Number, Publication Date, IPO Location, Application Type Description, Application Number, Date of Filing, Title of Invention, Field of Invention, Number of Pages, Number of Claims, Date Updated |

---

## 3. Tools & Technologies

| Layer           | Tools                                                                                                                                       |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| ETL & Cleaning  | Excel 2016 Power Query (From Folder, Combine, Append, Custom Columns), Excel formulas (`ISNUMBER`, `VALUE`, `RIGHT`, `YEAR`)                |
| Data Model      | Power Pivot / Excel Data Model, Power BI star schema, `CALENDARAUTO()` Calendar dimension                                                   |
| Calculations    | Pivot Tables, `GETPIVOTDATA`, `INDEX`/`MATCH`/`MAX`, DAX (`COUNT`, `AVERAGE`, `MEDIAN`, `CALCULATE`, `SAMEPERIODLASTYEAR`, `SELECTEDVALUE`) |
| Visualisation   | Excel PivotCharts + Slicers, Power BI (line, bar, KPI cards, drill-through, custom tooltips, dynamic titles)                                |
| Version Control | Git / GitHub                                                                                                                                |

---

## 4. Approach (Methodology)

### Step 1 — Extraction & Consolidation

- Loaded all 8 CSVs via **Power Query → From File → From Folder → Combine & Edit**.
- The **2022 file was tab-separated** while all others were comma-separated. Handled this by building a **separate parse query** for the 2022 source and appending it to the master query rather than forcing a single delimiter.
- Retained the `Source.Name` column to preserve per-file lineage for auditing.

### Step 2 — Data Profiling & Cleaning

- Profiled every column for data type errors, blanks, and duplicates.
- Discovered `Publication Number` was 100% corrupted → **dropped the column** and adopted `Application Number` as the primary key.
- **Deduplicated** on `Application Number`.
- **Trimmed whitespace** across all text columns and stripped stray double-quote characters from `Title of Invention`.
- Loaded the result to **both** an Excel worksheet Table (for inspection and PivotTables) **and** the Data Model (for performance and distinct-count aggregations).

### Step 3 — Derived Columns (Validation Layer)

Built defensive columns to handle source inconsistencies that Power Query had silently nulled:

```excel
GrantYear       = IF(L2="","",IF(ISNUMBER(L2),YEAR(L2),VALUE(RIGHT(L2,4))))
Pendency_Days   = IF(OR(L2="",M2=""),"",IF(AND(ISNUMBER(L2),ISNUMBER(M2)),L2-M2,"CHECK"))
IPO_Clean       = IF(OR(K2="Delhi",K2="Chennai",K2="Kolkata",K2="Mumbai"),K2,"Other")
```
---

## 8. Dashboard Screenshots

### 8.1 Excel Dashboard

---<img width="1912" height="971" alt="excel_dashboard" src="https://github.com/user-attachments/assets/3b9c19cb-871e-4a35-b6a0-f1c3f2500e40" />

*Three PivotCharts (grant trend, Top-10 fields of invention, IPO workload), live KPI cards powered by `GETPIVOTDATA` and `INDEX/MATCH`, and cross-connected slicers (`GrantYear`, `IPO_Clean`) that filter all visuals simultaneously.*

<br>


### 8.2 Power BI — Executive Overview

<img width="1897" height="912" alt="powerbi_executive_overview" src="https://github.com/user-attachments/assets/b049368d-f678-4221-84e7-6a9f4dc55d64" />

*Star-schema-driven report with DAX-based KPI cards (Total Grants, Avg Pendency, Median Pendency, YoY Growth %), dynamic chart titles, and interactive slicers for Year and IPO location.*

<br>

### 8.3 Power BI — Drill-Through View (Patent Details)

<img width="1687" height="927" alt="powerbi_drill_through" src="https://github.com/user-attachments/assets/2c14aa48-53d7-474d-9c8e-e9053f4ba95d" />

*Right-click any technology field (e.g., Chemistry) on the Executive Overview and select "Drill through → Patent Details" to surface the underlying patent records — demonstrating record-level traceability from aggregate insight to source data.*

