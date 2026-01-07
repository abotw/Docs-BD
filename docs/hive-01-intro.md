# Hive-01: Intro

*A Practical Introduction to Data Warehousing with Hive*

------

## 1. What Is Hive?

**Apache Hive** is a **data warehouse system built on top of Hadoop**.

It allows you to:

-   Store **large-scale data** in HDFS
-   Query data using **SQL-like language (HiveQL)**
-   Perform **batch analytics**, not real-time transactions

👉 Hive is designed for **analysis**, not for OLTP systems like MySQL.

------

## 2. Why Use Hive?

### 2.1 Problems Hive Solves

Before Hive:

-   Hadoop required writing **MapReduce code**
-   Very hard for SQL users
-   Development was slow

Hive:

-   Uses **SQL instead of Java**
-   Automatically converts SQL into **distributed jobs**
-   Handles **TB–PB scale data**

------

## 3. Where Hive Fits in Big Data Architecture

Typical architecture:

```
Data Sources
   ↓
HDFS / Object Storage
   ↓
Hive (SQL Layer)
   ↓
BI / Reports / Applications
```

Hive sits between **raw data storage** and **data analysis tools**.

------

## 4. Core Components of Hive

### 4.1 Hive Metastore

The **Metastore** stores:

-   Table names
-   Column definitions
-   Partition information
-   Storage locations

⚠️ **Important:**
Hive tables store **metadata only**, not the actual data.

------

### 4.2 HiveQL

Hive uses **HiveQL**, which looks very similar to SQL:

```sql
SELECT name, age
FROM users
WHERE age > 18;
```

But internally:

-   SQL → Execution plan
-   Execution plan → Distributed jobs

------

### 4.3 Execution Engine

Hive converts queries into:

-   MapReduce (older)
-   Tez
-   Spark (most common today)

You don’t need to write distributed code yourself.

------

## 5. Hive vs Traditional Databases

| Feature       | Hive              | MySQL           |
| ------------- | ----------------- | --------------- |
| Use case      | Analytics         | Transactions    |
| Data size     | TB / PB           | GB              |
| Query latency | Seconds / minutes | Milliseconds    |
| Updates       | Limited           | Frequent        |
| Schema        | Schema-on-read    | Schema-on-write |

------

## 6. Hive Data Model

### 6.1 Database

A **database** is just a namespace.

```sql
CREATE DATABASE analytics;
USE analytics;
```

------

### 6.2 Table

A **table** maps to a directory in HDFS.

```sql
CREATE TABLE users (
    id BIGINT,
    name STRING,
    age INT
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ',';
```

------

### 6.3 Internal vs External Tables

#### Internal Table

-   Hive manages the data
-   Dropping table deletes data

```sql
CREATE TABLE t1 (...);
```

#### External Table

-   Data managed outside Hive
-   Dropping table keeps data

```sql
CREATE EXTERNAL TABLE t2 (...)
LOCATION '/data/users';
```

✅ External tables are **recommended for beginners**.

------

## 7. Loading Data into Hive

### 7.1 Load from Local File

```sql
LOAD DATA LOCAL INPATH '/tmp/users.csv'
INTO TABLE users;
```

------

### 7.2 Insert from Another Table

```sql
INSERT INTO TABLE users
SELECT id, name, age
FROM users_raw;
```

------

## 8. Partitions and Buckets

### 8.1 Partition

Partitions split data by directory.

```sql
CREATE TABLE logs (
    user_id BIGINT,
    action STRING
)
PARTITIONED BY (dt STRING);
```

Directory structure:

```
logs/dt=2026-01-07/
logs/dt=2026-01-08/
```

Benefits:

-   Faster queries
-   Less data scanned

------

### 8.2 Bucket

Buckets split data into files using hash.

```sql
CLUSTERED BY (user_id) INTO 8 BUCKETS;
```

Used mainly for:

-   Joins
-   Sampling

------

## 9. Common HiveQL Operations

### 9.1 SELECT

```sql
SELECT *
FROM users
LIMIT 10;
```

------

### 9.2 WHERE

```sql
SELECT *
FROM users
WHERE age >= 18;
```

------

### 9.3 GROUP BY

```sql
SELECT age, COUNT(*)
FROM users
GROUP BY age;
```

------

### 9.4 JOIN

```sql
SELECT u.name, o.amount
FROM users u
JOIN orders o
ON u.id = o.user_id;
```

------

## 10. File Formats in Hive

| Format   | Use Case         |
| -------- | ---------------- |
| TEXTFILE | Simple, readable |
| ORC      | Best for Hive    |
| PARQUET  | Best for Spark   |
| AVRO     | Schema evolution |

👉 **ORC or PARQUET is recommended**.

------

## 11. Hive Is Not a Replacement for MySQL

❌ Do not use Hive for:

-   Online transactions
-   Frequent updates
-   Row-level deletes

✅ Use Hive for:

-   Reports
-   Batch analytics
-   Data warehousing

------

## 12. Hive Best Practices for Beginners

1.  Always use **external tables**
2.  Always use **partitions**
3.  Prefer **ORC / PARQUET**
4.  Keep business logic out of raw data
5.  Design tables for **query patterns**

------

## 13. How Hive Is Used in Real Projects

Typical flow:

1.  Ingest data into HDFS
2.  Create ODS tables in Hive
3.  Clean data into DWD
4.  Aggregate into DWS
5.  Serve results via ADS

(Exactly the ODS–DWD–DWS–ADS model you just learned)

------

## 14. Learning Path Recommendation

Beginner → Advanced:

1.  Hive basics & HiveQL
2.  Partitions & file formats
3.  Data warehouse layering
4.  Performance tuning
5.  Integration with Spark / Airflow

------

## 15. Final Summary

-   Hive = **SQL on Big Data**
-   Hive works on **HDFS**
-   Hive is for **analytics, not OLTP**
-   Layered design makes Hive powerful

If you master Hive, you can easily move to:

-   Spark SQL
-   Presto / Trino
-   Flink SQL