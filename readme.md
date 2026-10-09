# Indian Patent Grants Analytics (2016–2023)

**An end-to-end Data Analytics portfolio project analyzing 153,000+ granted patents in India using Excel and Power BI.**

## 📖 Project Overview

This project analyzes 8 years of Indian patent grant data to uncover trends in technology fields, IPO (Intellectual Property Office) workloads, and grant pendency (time from filing to grant). The project demonstrates a complete data pipeline: from raw, messy CSV extraction and cleaning, to data modeling, DAX calculations, and interactive dashboard design.

**Domain Context:** As an ex-patent analyst, I combined my IP domain knowledge with data analytics tools to extract actionable insights for IP strategists, law firms, and policymakers.

## 🛠️ Tools & Skills Demonstrated

- **Excel (Power Query, Power Pivot, Pivot Tables):** ETL, data cleaning, GETPIVOTDATA, INDEX/MATCH, dynamic dashboards with slicers.
- **Power BI:** Star schema data modeling, DAX (Time Intelligence, CALCULATE, SAMEPERIODLASTYEAR), Drill-through, Custom Tooltips, Dynamic Titles.
- **Data Quality & ETL:** Handling heterogeneous file formats, mixed date storage, text parsing, and identifying structural corruption in raw data.

## 📊 Data Pipeline & Quality Findings (The "Gold" Section)

The raw data was not clean. I performed a rigorous data quality audit and resolved several critical issues:

1.  **Heterogeneous Formats:** The 2022 file was tab-separated, while all other files were comma-separated. I built a separate Power Query parse step for 2022 and appended it to the master table.
2.  **Corrupted Primary Key:** The `Publication Number` column was 100% corrupted in the source data (containing random date strings). I excluded it and used `Application Number` as the primary key for deduplication.
3.  **Mixed Date Storage:** ~108,000 rows had dates stored as text (e.g., "25-03-2016") instead of real dates. I wrote a robust parsing layer in Excel (`=IF(ISNUMBER(date),YEAR(date),VALUE(RIGHT(date,4)))`) to extract the year correctly.
4.  **Structural Row Shifting:** ~1,635 rows (1.07%) had column-shift corruption. Commas inside the `Title of Invention` broke the CSV delimiter, scattering title fragments into the `IPO Location` column. These rows were quarantined using an `IPO_Clean` derived column and excluded from visuals via slicer filters, but retained in the dataset for auditing.
5.  **Partial-Year Data:** 2023 data only covered Jan–Mar. This was excluded from trend and YoY conclusions to prevent misleading analysis.
6.  **Pendency Outliers:** 3 rows had impossible pendencies (>20 years). These were excluded from statistical averages but kept in the dataset.

## 📈 Key Insights

- **Grant Growth:** Patent grants grew **~3.8x** from 2016 to 2022 (8,064 to 30,499 grants).
- **IPO Workload:** Delhi and Chennai IPOs handle **~70%** of all patent grants in India (Delhi: 37.9%, Chennai: 32.4%).
- **Technology Fields:** Chemistry (38,547 grants) and Mechanical (32,649 grants) dominate, reflecting India's strength in pharmaceuticals and manufacturing.
- **Grant Pendency:** The median pendency is **~7 years (2,578 days)**. The distribution is left-skewed (Median > Mean), indicating a subset of patents are granted unusually fast, pulling the average down.

## 📂 Repository Structure

- `/data` - Raw CSV files (or link to Kaggle source).
- `/excel` - Excel workbook containing the Power Query ETL, Data Model, and Excel Dashboard.
- `/powerbi` - Power BI `.pbix` file containing the data model, DAX measures, and interactive report.
