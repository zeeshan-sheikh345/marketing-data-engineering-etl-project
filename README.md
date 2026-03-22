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

Source Systems (CSV / JSON / NoSQL)
        ↓
GCS Landing Layer (Batch Files)
        ↓
Raw Layer (BigQuery External Ingestion)
        ↓
Silver Layer (Data Cleaning & Standardization)
        ↓
Gold Layer (Business Logic & Joins)
        ↓
Diamond Layer (Final Aggregated Reporting Tables)

```
📥 Batch Ingestion Design

The pipeline uses batch ingestion rather than streaming.
Data files are periodically exported from source systems and stored in Google Cloud Storage.
BigQuery load jobs are then used to ingest these files into raw tables.

Key characteristics:

Batch-based ingestion from GCS
Multiple source formats (CSV, JSON, AVRO)
Support for incremental processing in transformation layer
Raw data preserved for reprocessing
Separation of ingestion and transformation layers


### 🔹 Layer Mapping (as used in repository)

| Layer Name | Purpose                       | Folder Mapping      |
| ---------- | ----------------------------- | ------------------- |
| Raw        | Source data (GA4, CRM, Ads)   | BigQuery raw tables |
| Silver     | Cleaned & standardized data   | `silver/`           |
| Gold       | Joined & transformed datasets | `gold/`             |
| Diamond    | Final reporting tables        | `diamond/`          |

---

## 🔄 ETL Pipeline

### 1. Ingestion (Batch Pipeline)

This pipeline follows a batch ingestion architecture where data from multiple source types is first landed in Google Cloud Storage (GCS) and then loaded into BigQuery raw tables.

Source Types Used

Ads data — Structured CSV files
GA4 data — Semi-structured AVRO/JSON event logs
CRM data — NoSQL JSON documents (MongoDB export)

Ingestion Flow
```id="struct1"
Source Systems
   ├── Ads CSV files
   ├── GA4 event logs (JSON / AVRO)
   └── CRM MongoDB export (JSON)
            │
            ▼
Google Cloud Storage (GCS)
            │
            ▼
BigQuery Raw Tables (Batch Load)
            │
            ▼
Dataform Transformations (Silver → Gold → Diamond)
```
Raw Tables

gcp_raw_data.ads_raw
gcp_raw_data.ga4_raw
gcp_raw_data.crm_raw

Batch ingestion is performed using BigQuery load jobs from GCS.

Example:

bq load gcp_raw_data.ads_raw gs://bucket/ads.csv

This approach demonstrates ingestion from multiple data formats and storage types, satisfying both relational and NoSQL ingestion requirements.

### 2. Transformation

* Date format standardization (CRM fix applied)
* Null and invalid record handling using SAFE_CAST
* Data normalization across sources
* Business logic applied through joins and aggregations

### 3. Load

* Final datasets stored in BigQuery (analytics layer)
* CRM data originates from NoSQL but is transformed into relational format for analysis

  NoSQL Data Storage
  Raw CRM and GA4 data are stored in BigQuery using nested and repeated structures, preserving their document-style format. This acts as a NoSQL-style storage layer before transformation into relational tables.

Examples:

CRM products stored as repeated records
GA4 event_params stored as nested arrays
Raw data preserved without flattening

The data is later transformed into relational tables in the Silver layer.

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

🗄️ NoSQL Data Model Design
Selected NoSQL Model: Document-Based Structure

The raw GA4 and CRM datasets are stored using a document-style data model in BigQuery.
This approach preserves nested and repeated JSON structures before transformation into relational tables.

Example — CRM Document Model

Each order is stored as a document with nested products:
```id="struct1"
{
 "order_id": "O1",
 "user_id": "U1",
 "products": [
   { "product_id": "P1", "price": 100 },
   { "product_id": "P2", "price": 200 }
 ]
}
```
This structure is best suited for document-based NoSQL storage because:

Flexible schema
Nested arrays
Varying number of products per order
Semi-structured data

Example — GA4 Event Model

GA4 events are stored as nested event documents:
```id="struct1"
{
 "event_name": "purchase",
 "event_params": [
   {"key": "transaction_id", "value": "T1"},
   {"key": "campaign_id", "value": "C1"}
 ]
}
```
This structure fits document-based NoSQL modeling because:

dynamic event parameters
schema changes across events
nested attributes
log-style data

### SQL vs NoSQL Justification

| Dataset            | Storage Type | Data Model | Reason                                 |
| ------------------ | ------------ | ---------- | -------------------------------------- |
| CRM                | NoSQL        | Document   | Nested products array, flexible schema |
| GA4                | NoSQL        | Document   | Event logs with dynamic parameters     |
| Ads                | SQL          | Relational | Structured campaign metrics            |
| Silver/Gold tables | SQL          | Relational | Analytics & joins                      |

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
    int_transactions.sqlx
    int_orders.sqlx
    int_campaign_performance.sqlx

  diamond/
    fct_campaign_performance.sqlx

data_samples/
output/
docs/
README.md

```
<img width="1497" height="203" alt="image" src="https://github.com/user-attachments/assets/d7225f66-4ee9-40bc-9264-546cdf070b14" />


---

## 🚀 Tools & Technologies

* Google BigQuery
* Google cloud Storage
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
