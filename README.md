# 📰 News API Analytics – ETL Pipeline with Snowflake and Airflow

This project implements an automated ETL pipeline that fetches real-time news articles from the News API, stages the data in **Google Cloud Storage (GCS)**, and loads it into **Snowflake** for analytical querying and dashboarding. The entire workflow is orchestrated using **Apache Airflow**.

---

## 📌 Project Overview

The goal of this project is to automate the extraction, transformation, and loading of large volumes of breaking news articles for trend analysis, author activity tracking, and category-level summaries. The pipeline runs on a schedule and populates multiple Snowflake tables for downstream use in BI tools like **Tableau** or **Power BI**.

---

## 🛠️ Tech Stack

| Tool/Service       | Purpose                                                       |
|--------------------|---------------------------------------------------------------|
| **News API**        | Source of real-time news articles in JSON                    |
| **Python**          | Extracts data and processes it for staging                   |
| **Google Cloud Storage (GCS)** | Temporary storage layer for raw JSON data            |
| **Snowflake**       | Data warehouse for storing curated news datasets             |
| **Apache Airflow**  | Orchestrates the pipeline (DAG: `newsapi_to_gcs`)            |
| **SnowflakeOperator** | Executes Snowflake DDL/DML via Airflow                      |

---

## 🔄 ETL Workflow

1. **Data Extraction (`newsapi_data_to_gcs`)**  
   - Fetches articles from the News API using filters (e.g., date range, language, keywords)  
   - Stores raw data in GCS as JSON

2. **Table Creation in Snowflake (`snowflake_create_table`)**  
   - Creates the required Snowflake tables if they don’t already exist

3. **Data Loading (`snowflake_copy_from_stage`)**  
   - Loads data from GCS to a Snowflake staging table using `COPY INTO`

4. **Data Transformation & Aggregation**
   - `create_or_replace_news_summary_tb`: Builds a summary table for trending headlines
   - `create_or_replace_author_activity_tb`: Tracks author activity and article volume

---

## 🧾 Fact Tables in Snowflake

### `news_summary_tb`
| Column            | Description                                      |
|------------------|--------------------------------------------------|
| `date`           | Publication date                                 |
| `category`       | Inferred category/topic based on keyword analysis|
| `top_headlines`  | Aggregated headlines by category                 |
| `source_count`   | Count of unique news sources per category        |

### `author_activity_tb`
| Column       | Description                             |
|--------------|-----------------------------------------|
| `author`     | Name of the author                      |
| `article_cnt`| Number of articles published            |
| `avg_length` | Average length of the article content   |
| `active_days`| Number of distinct publishing dates     |

---

## 🖼️ Airflow DAG – Pipeline Visualization

The DAG `newsapi_to_gcs` consists of the following tasks:

```text
[newsapi_data_to_gcs] 
        ↓
[snowflake_create_table] 
        ↓
[snowflake_copy_from_stage]
       ↓               ↓
[create_or_replace_author_activity_tb]  
[create_or_replace_news_summary_tb]
