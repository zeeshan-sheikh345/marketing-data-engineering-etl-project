# Marketing Data Engineering ETL Project

## 📌 Overview

In marketing analytics, business teams often evaluate performance using high-level metrics such as total orders and revenue. However, this approach does not provide a complete picture of **true business performance**, as it ignores critical factors like returns, customer quality, and actual profitability.

This project addresses that gap by building a unified data pipeline that combines:

* Advertising data (spend, clicks, impressions)
* Web analytics data (GA4 events)
* CRM data (orders, returns, customer-level insights)

By integrating these sources, the pipeline enables:

* Accurate calculation of **net revenue**
* Visibility into **returns and their impact on performance**
* Identification of **high-performing vs low-performing campaigns**

The final output provides a **holistic, analytics-ready dataset** that helps stakeholders understand not just revenue, but **true marketing effectiveness and profitability**.

---

## 🎯 Objective

To demonstrate hands-on experience with:

* ETL pipeline design
* Data transformation using SQL
* Handling both SQL and NoSQL data sources
* Data modeling and pipeline reliability

---

## 🏗️ Architecture

The pipeline follows a **multi-layered architecture** inspired by modern data platforms:

```id="arch1"
Raw Layer (BigQuery / MongoDB Extract)
        ↓
Silver Layer (Data Cleaning & Standardization)
        ↓
Gold Layer (Business Logic & Joins)
        ↓
Diamond Layer (Final Aggregated Reporting Tables)
```

### 🔹 Layer Mapping (as used in repository)

| Layer Name | Purpose                       | Folder Mapping      |
| ---------- | ----------------------------- | ------------------- |
| Raw        | Source data (GA4, CRM, Ads)   | BigQuery raw tables |
| Silver     | Cleaned & standardized data   | `silver/`           |
| Gold       | Joined & transformed datasets | `gold/`             |
| Diamond    | Final reporting tables        | `diamond/`          |

---

## 🔄 ETL Pipeline

### 1. Ingestion

* GA4 data ingested from BigQuery export
* CRM data ingested from MongoDB (NoSQL source)
* Ads data ingested from structured tables
* Incremental loading implemented using date-based filtering

### 2. Transformation

* Date format standardization (CRM fix applied)
* Null and invalid record handling using SAFE_CAST
* Data normalization across sources
* Business logic applied through joins and aggregations

### 3. Load

* Final datasets stored in BigQuery (analytics layer)
* CRM data originates from NoSQL but is transformed into relational format for analysis

---

## 🧠 Data Modeling

### Relational Model (BigQuery)

* Fact Table: campaign_performance
* Dimensions:

  * campaign_id
  * date

Primary Key:

* (campaign_id, date)

---

### NoSQL Model (CRM – MongoDB)

* Document-based structure
* Flexible schema for customer and lead data

---

### SQL vs NoSQL Justification

| Dataset | Storage Type    | Reason                                         |
| ------- | --------------- | ---------------------------------------------- |
| CRM     | NoSQL (MongoDB) | Flexible schema, semi-structured customer data |
| GA4     | SQL (BigQuery)  | Structured event export                        |
| Ads     | SQL             | Aggregation and reporting use cases            |

---

## 🧾 SQL Features Used

* Joins (LEFT JOIN, INNER JOIN)
* Aggregations (SUM, COUNT)
* Window functions (ROW_NUMBER for deduplication)
* SAFE_CAST for handling invalid data
* Incremental filtering logic

---

## ✅ Data Quality & Reliability

* Null handling and validation checks
* Duplicate removal using window functions
* Date format standardization (CRM fix)
* Incremental pipeline design
* Idempotent transformations

---

## 📁 Project Structure

```id="struct1"
definitions/

  silver/
    ga4.sqlx
    crm.sqlx
    ads.sqlx

  gold/
    int_campaign_performance.sqlx

  diamond/
    fct_campaign_performance.sqlx

data_samples/
docs/
README.md
```

---

## 🚀 Tools & Technologies

* Google BigQuery
* Dataform (SQL-based transformation tool)
* MongoDB (CRM source)
* SQL (Standard SQL)
* GitHub (version control)

---

## 📊 Output

Final dataset includes:

* Campaign performance metrics
* Spend, clicks, impressions
* Conversions and revenue
* Returns and net revenue insights
* Daily aggregated performance

---

## 📌 Notes

This project uses Dataform for managing SQL-based transformations and dependency management using `ref()`.

The architecture follows a **Silver → Gold → Diamond layering approach**, commonly used in modern data engineering systems for scalability and clarity.

---
