# Hive-03: Layering

*A Beginner’s Guide to Data Warehouse*

## 1. Why Do We Need Data Warehouse Layers?

When building a data warehouse with **Hive**, raw data usually comes from many sources:

-   Application databases (MySQL, PostgreSQL)
-   Logs (web, app, server logs)
-   External systems (Kafka, APIs)

If we put all data into Hive without structure, we quickly face problems:

-   Data is messy and inconsistent
-   Queries become slow and complex
-   Business logic is duplicated everywhere
-   Hard to maintain and scale

👉 **Solution:** Use a **layered data warehouse architecture**.

The most common and practical layering model in Hive is:

```
ODS → DWD → DWS → ADS
```

Each layer has a **clear responsibility**.

------

## 2. Overview of ODS–DWD–DWS–ADS

| Layer | Full Name                | Purpose                         |
| ----- | ------------------------ | ------------------------------- |
| ODS   | Operational Data Store   | Store raw data                  |
| DWD   | Data Warehouse Detail    | Cleaned, detailed data          |
| DWS   | Data Warehouse Summary   | Aggregated, theme-based data    |
| ADS   | Application Data Service | Data for reports & applications |

Think of it as a **data refinement pipeline**:

```
Raw → Clean → Aggregate → Serve
```

------

## 3. ODS (Operational Data Store)

### 3.1 What Is ODS?

**ODS stores raw data** as close to the source as possible.

-   No or minimal processing
-   Keeps original structure
-   Used as a data backup and audit layer

### 3.2 Characteristics

-   One table ≈ one source table
-   Fields match source fields
-   Usually partitioned by date
-   Append-only (no updates)

### 3.3 Example

**Source:** MySQL `user` table
**ODS table:**

```sql
CREATE TABLE ods_user (
    id BIGINT,
    name STRING,
    email STRING,
    create_time STRING
)
PARTITIONED BY (dt STRING)
STORED AS PARQUET;
```

**Load data:**

```sql
INSERT OVERWRITE TABLE ods_user PARTITION (dt='2026-01-07')
SELECT id, name, email, create_time
FROM mysql_user;
```

### 3.4 What NOT to Do in ODS

❌ Do not:

-   Change field meanings
-   Apply business rules
-   Join tables

ODS = **“What the source gave me”**

------

## 4. DWD (Data Warehouse Detail)

### 4.1 What Is DWD?

**DWD is the cleaned and standardized detail layer.**

Here we:

-   Clean dirty data
-   Standardize formats
-   Apply basic business logic

### 4.2 Typical Operations

-   Remove duplicates
-   Handle null values
-   Convert data types
-   Normalize codes
-   Add derived fields

### 4.3 Example

From `ods_user` → `dwd_user`

```sql
CREATE TABLE dwd_user (
    user_id BIGINT,
    user_name STRING,
    email STRING,
    create_date DATE
)
PARTITIONED BY (dt STRING)
STORED AS PARQUET;
INSERT OVERWRITE TABLE dwd_user PARTITION (dt='2026-01-07')
SELECT
    id AS user_id,
    name AS user_name,
    email,
    TO_DATE(create_time) AS create_date
FROM ods_user
WHERE dt='2026-01-07'
  AND id IS NOT NULL;
```

### 4.4 Key Rule

DWD data should be:

-   **Clean**
-   **Consistent**
-   **Reusable**

DWD is often the **most important layer**.

------

## 5. DWS (Data Warehouse Summary)

### 5.1 What Is DWS?

**DWS is the aggregation layer**, organized by **business themes**.

Examples of themes:

-   User behavior
-   Orders
-   Revenue
-   Traffic

### 5.2 Purpose

-   Reduce repeated aggregation
-   Improve query performance
-   Provide unified metrics

### 5.3 Example

**User daily summary**

```sql
CREATE TABLE dws_user_day (
    dt STRING,
    new_user_count BIGINT,
    total_user_count BIGINT
)
STORED AS PARQUET;
INSERT OVERWRITE TABLE dws_user_day
SELECT
    dt,
    COUNT(user_id) AS new_user_count,
    SUM(COUNT(user_id)) OVER (ORDER BY dt) AS total_user_count
FROM dwd_user
GROUP BY dt;
```

### 5.4 Design Tips

-   Usually **wide tables**
-   One table serves many downstream queries
-   Metrics are stable and reusable

------

## 6. ADS (Application Data Service)

### 6.1 What Is ADS?

**ADS serves data directly to applications and reports.**

Consumers:

-   BI tools (Tableau, Power BI)
-   Dashboards
-   APIs
-   Management reports

### 6.2 Characteristics

-   Highly aggregated
-   Business-oriented
-   Small data volume
-   Fast query speed

### 6.3 Example

**Daily dashboard table**

```sql
CREATE TABLE ads_dashboard (
    dt STRING,
    new_users BIGINT,
    total_users BIGINT
)
STORED AS PARQUET;
INSERT OVERWRITE TABLE ads_dashboard
SELECT
    dt,
    new_user_count,
    total_user_count
FROM dws_user_day;
```

### 6.4 Key Rule

ADS = **“What the business wants to see”**

------

## 7. Data Flow Summary

```
ODS  →  DWD  →  DWS  →  ADS
Raw     Clean    Aggregate   Serve
```

| Layer | Changes Allowed            |
| ----- | -------------------------- |
| ODS   | Almost none                |
| DWD   | Cleaning & standardization |
| DWS   | Aggregation & metrics      |
| ADS   | Business presentation      |

------

## 8. Common Best Practices

### 8.1 Naming Conventions

-   Database: `ods`, `dwd`, `dws`, `ads`
-   Table prefix: same as layer name

Example:

```
ods_user
dwd_user
dws_user_day
ads_dashboard
```

### 8.2 Partition Strategy

-   Most tables partition by date (`dt`)
-   Improves query performance
-   Simplifies data lifecycle management

### 8.3 Avoid These Mistakes

❌ Putting business logic in ODS
❌ Re-aggregating data in ADS
❌ Skipping DWD and directly using ODS
❌ Making ADS too complex

------

## 9. How Beginners Should Practice

1.  Start with **one business process** (e.g., user registration)
2.  Build:
    -   1 ODS table
    -   1 DWD table
    -   1 DWS table
    -   1 ADS table
3.  Write SQL step by step
4.  Focus on **clarity**, not optimization first

------

## 10. Final Takeaway

-   **ODS**: raw, unchanged data
-   **DWD**: clean, standardized detail data
-   **DWS**: aggregated, theme-based data
-   **ADS**: business-facing result data

If you understand **why each layer exists**, Hive data warehouse design becomes **clear, scalable, and maintainable**.