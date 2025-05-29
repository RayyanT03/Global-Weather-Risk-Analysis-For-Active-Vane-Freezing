See Powerpoint for Tableau Dashboards and Insights gathered

# 🌍 Worldwide Weather Station Data (2005–2025)

This project supports an investigation into vehicle vane malfunctions under varying weather conditions. The hypothesis is that environmental factors—such as temperature, precipitation, fog, dew, and wind—may contribute to vane performance issues. The objective is to extract, clean, and aggregate global weather data from 2005 to 2025 for visualization and analysis in Tableau. The user will then select relevant features to filter and will get a proportion of days where the weather condition occured.

---

## 🧠 Project Goals

- Identify weather patterns that may affect vehicle vane performance.
- Aggregate weather data at country, continent, and global levels.
- Ensure data quality for reliable downstream analytics and dashboards.

---

## 🛠️ SQL Pipeline Breakdown

### 1. 🇬🇧 Country-Level Weather Data (UK Example)

**Source**: NOAA GSOD dataset via Google BigQuery  
**Years Covered**: 2005–2025

#### Steps:
- **CTE (`yearly_data`)**: Combines yearly datasets using `UNION ALL`.
- **Join**: Merges station metadata with daily weather observations.
- **Transformations**: Converts temperature fields from Fahrenheit to Celsius.
- **Filtering**: Limits data to UK stations.
- **Output**: Sorted dataset by station and date.

```sql
WITH yearly_data AS (
  SELECT ...
  FROM `bigquery-public-data.noaa_gsod.gsod2005` AS g
  JOIN `bigquery-public-data.noaa_gsod.stations` AS s
  ON s.usaf = g.stn
  WHERE s.country = 'UK'
  UNION ALL
  ...
)
SELECT *
FROM yearly_data
ORDER BY usaf, year, mo, da;

2. 🌍 Continent-Level Aggregation (Europe Example)
Goal: Merge pre-processed country-level tables into a single continent-wide dataset.

CREATE TABLE `Weather_station.2 Europe 2005-2025` AS
SELECT * FROM `Weather_station.2 Austria AU 2005-2025`
UNION ALL
SELECT * FROM `Weather_station.2 Belgium BE 2005-2025`
...
SELECT * FROM `Weather_station.2 United Kingdom UK 2005-2025`;

3. 🌐 Global Weather Dataset
Goal: Combine all continent-level tables into a single global dataset and apply data quality filters.
CREATE TABLE `Weather_station.2 All 2005-2025` AS
SELECT *
FROM (
  SELECT * FROM `Weather_station.2 South America 2005-2025`
  UNION ALL
  SELECT * FROM `Weather_station.2 Africa 2005-2025`
  ...
  SELECT * FROM `Weather_station.2 North America 2005-2025`
)
WHERE max_celsius < 60
  AND min_celsius < 60
  AND dewp_celsius BETWEEN -60 AND 60;


📁 Data Sources
NOAA GSOD Dataset
Google BigQuery Public Datasets
