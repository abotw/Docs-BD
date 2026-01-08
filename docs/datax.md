# DataX

## 1. What Is DataX?

**DataX** is an **open-source data synchronization tool** developed by **Alibaba**.
It is mainly used to **move data between different data sources** efficiently and reliably.

Typical use cases include:

-   MySQL → HDFS
-   MySQL → Hive
-   Oracle → MySQL
-   PostgreSQL → HBase
-   CSV / TXT → Database
-   Database → Database

👉 In short: **DataX = batch data migration / synchronization tool**

------

## 2. Core Features

-   **Plugin-based architecture** (easy to extend)
-   Supports **many data sources**
-   High performance (parallel execution)
-   Simple **JSON configuration**
-   Suitable for **offline / batch data sync**

⚠️ Note:
DataX is **not** designed for real-time streaming (use Kafka / Flink / Debezium for that).

------

## 3. How DataX Works (High-Level)

DataX uses a **Reader → Channel → Writer** model.

```
[ Source ] --> Reader --> Channel --> Writer --> [ Target ]
```

### Components

1.  **Reader**
    -   Reads data from the source
    -   Example: MySQLReader, HDFSReader
2.  **Channel**
    -   Data transmission pipeline
    -   Controls concurrency and speed
3.  **Writer**
    -   Writes data to the target
    -   Example: MySQLWriter, HiveWriter

------

## 4. Supported Data Sources (Common Ones)

| Type          | Examples                              |
| ------------- | ------------------------------------- |
| Relational DB | MySQL, Oracle, PostgreSQL, SQL Server |
| Big Data      | HDFS, Hive, HBase                     |
| NoSQL         | MongoDB                               |
| File          | TXT, CSV                              |
| Others        | Elasticsearch                         |

Each data source is implemented as a **Reader** or **Writer plugin**.

------

## 5. Installation

### 5.1 Environment Requirements

-   Linux or macOS
-   Python 2.7 or Python 3.x
-   Java JDK 8+

Check Java:

```bash
java -version
```

------

### 5.2 Download DataX

```bash
wget https://datax-opensource.oss-cn-hangzhou.aliyuncs.com/datax.tar.gz
tar -zxvf datax.tar.gz
cd datax
```

Directory structure:

```
datax/
├── bin/
│   └── datax.py
├── conf/
├── job/
├── plugin/
└── lib/
```

------

## 6. Basic Usage

### Command Format

```bash
python bin/datax.py job/job.json
```

Where `job.json` is the **job configuration file**.

------

## 7. DataX Job Configuration Explained

A DataX job is defined using **JSON**.

### Basic Structure

```json
{
  "job": {
    "setting": {},
    "content": [
      {
        "reader": {},
        "writer": {}
      }
    ]
  }
}
```

------

## 8. Example: MySQL → MySQL

### 8.1 Job File: `mysql_to_mysql.json`

```json
{
  "job": {
    "setting": {
      "speed": {
        "channel": 3
      }
    },
    "content": [
      {
        "reader": {
          "name": "mysqlreader",
          "parameter": {
            "username": "root",
            "password": "123456",
            "column": ["id", "name", "age"],
            "connection": [
              {
                "table": ["user"],
                "jdbcUrl": [
                  "jdbc:mysql://localhost:3306/source_db"
                ]
              }
            ]
          }
        },
        "writer": {
          "name": "mysqlwriter",
          "parameter": {
            "username": "root",
            "password": "123456",
            "column": ["id", "name", "age"],
            "connection": [
              {
                "table": ["user_copy"],
                "jdbcUrl": "jdbc:mysql://localhost:3306/target_db"
              }
            ]
          }
        }
      }
    ]
  }
}
```

------

### 8.2 Run the Job

```bash
python bin/datax.py job/mysql_to_mysql.json
```

If successful, you will see:

```
Task start time...
Task end time...
Total records: xxxx
```

------

## 9. Important Configuration Options

### 9.1 Speed Control

Limit parallelism:

```json
"setting": {
  "speed": {
    "channel": 2
  }
}
```

Limit bytes per second:

```json
"speed": {
  "byte": 1048576
}
```

------

### 9.2 Column Selection

-   All columns:

```json
"column": ["*"]
```

-   Specific columns:

```json
"column": ["id", "name"]
```

------

### 9.3 Where Condition (MySQL Reader)

```json
"where": "id > 100"
```

------

## 10. Common Plugins

| Plugin      | Description          |
| ----------- | -------------------- |
| mysqlreader | Read data from MySQL |
| mysqlwriter | Write data to MySQL  |
| hdfsreader  | Read from HDFS       |
| hdfswriter  | Write to HDFS        |
| hivereader  | Read from Hive       |
| hivewriter  | Write to Hive        |

Plugins are located in:

```
plugin/reader/
plugin/writer/
```

------

## 11. Error Handling & Logs

### Log Location

```bash
log/
```

Common errors:

-   Database connection failure
-   Column mismatch
-   Permission issues
-   Character encoding problems

------

## 12. Performance Tuning Tips

-   Increase `channel` carefully
-   Avoid `select *` on large tables
-   Use `where` for incremental sync
-   Ensure indexes exist on source tables
-   Avoid running too many jobs at the same time

------

## 13. When Should You Use DataX?

✅ Good for:

-   Offline batch migration
-   Periodic data synchronization
-   One-time data transfer

❌ Not suitable for:

-   Real-time streaming
-   Low-latency CDC

------

## 14. Learning Path Recommendation

1.  Understand Reader / Writer model
2.  Practice MySQL → MySQL
3.  Try MySQL → HDFS / Hive
4.  Learn performance tuning
5.  Read plugin source code (advanced)

------

## 15. Summary

-   DataX is a **powerful batch data sync tool**
-   Uses **JSON-based configuration**
-   Reader + Channel + Writer architecture
-   Best for **offline data integration**