# U.S. Air Quality Analysis Dashboard

![SQL](https://img.shields.io/badge/SQL-Querying-blue)
![Google BigQuery](https://img.shields.io/badge/Google%20BigQuery-Data%20Warehouse-%234285F4)
![Looker Studio](https://img.shields.io/badge/Looker%20Studio-Data%20Viz-%23DC3932)
![Data Analysis](https://img.shields.io/badge/Project-Data%20Analysis-brightgreen)

An end-to-end data analysis project that investigates U.S. Carbon Monoxide (CO) pollution levels. This project processes, analyzes, and visualizes environmental data to uncover spatial and temporal trends, transforming raw data into actionable insights.

**🔗 [View Interactive Dashboard](https://lnkd.in/gCjDrfWX)**  


---

## 📌 Problem Statement

Air quality is a significant public health and environmental concern. This project aims to answer key questions:
- What are the long-term trends in Carbon Monoxide (CO) levels across the United States?
- Are there specific geographic "hotspots" with consistently higher pollution levels?
- How do CO levels fluctuate with seasons?

The goal is to provide a clear, data-driven narrative on air quality to inform public awareness and potential policy decisions.

---

## 📁 Data Source

The data for this analysis is sourced from the **U.S. Environmental Protection Agency (EPA)**:
- **Dataset:** [Air Quality System (AQS)](https://www.epa.gov/outdoor-air-quality-data)
- **Specific Data:** Carbon Monoxide (CO) Daily Summary Data
- **Time Period:** 2019 - 2024
- **Metrics:** Sample measurements, site information (city, state, county), dates.

---

## 🛠️ Tools & Technologies

- **Data Warehousing:** Google BigQuery
- **Data Analysis:** SQL
- **Data Visualization:** Looker Studio
- **Version Control:** Git / GitHub

---

## 📊 Project Process

### 1. Data Preparation & Cleaning (in Google BigQuery)

The raw data required significant preparation to ensure quality and consistency for analysis.

- **Handled Missing Values:** Identified and addressed NULL values in critical fields like `sample_measurement`. For time-series analysis, used the `LAG()` function to impute missing values based on previous readings from the same site.
- **Joined Tables:** Combined the main measurements table with the site information table on a common key (`site_number`) to enrich the data with location details (city, state).
- **Aggregated Data:** Calculated daily average CO levels per monitoring site using the `AVG()` function to ensure consistent data points.
- **Filtered Data:** Removed any erroneous data points or outliers that were clear measurement errors (e.g., negative values).

### 2. Exploratory Data Analysis (EDA) with SQL

The cleaned data was analyzed using SQL to uncover trends and answer the core project questions.

**Analyzing Seasonal Trends:**
```sql
-- Calculate average CO level by month for the year 2024
SELECT
  EXTRACT(MONTH FROM date) AS month,
  ROUND(AVG(sample_measurement), 3) AS avg_co_level
FROM
  `your_project.air_quality.cleaned_measurements`
WHERE
  EXTRACT(YEAR FROM date) = 2024
GROUP BY
  month
ORDER BY
  month;

-- Find top 10 cities by average CO level (minimum 100 readings required)
WITH city_stats AS (
  SELECT
    city_name,
    COUNT(sample_measurement) AS reading_count,
    ROUND(AVG(sample_measurement), 3) AS avg_co_level
  FROM
    `your_project.air_quality.cleaned_measurements`
  GROUP BY
    city_name
)
SELECT
  city_name,
  avg_co_level,
  reading_count
FROM
  city_stats
WHERE
  reading_count > 100
ORDER BY
  avg_co_level DESC
LIMIT 10;
