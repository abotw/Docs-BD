# Hadoop-01: Intro

*A Gentle Introduction to Apache Hadoop*

------

## 1. What Is Hadoop?

**Apache Hadoop** is an **open-source framework** designed to **store** and **process very large datasets** across **many computers** (clusters) in a **reliable and scalable** way.

In simple terms:

>   Hadoop lets you use **many cheap machines** to work together as **one powerful system** for big data.

------

## 2. Why Do We Need Hadoop?

Traditional systems struggle when data becomes:

-   Too **large** (TBs or PBs)
-   Too **fast** (logs, streams, events)
-   Too **complex** (semi-structured, unstructured)

### Problems with Traditional Databases

-   Scale **up** (bigger server) instead of **out**
-   Expensive hardware
-   Limited fault tolerance

### Hadoop’s Solution

-   Scale **out** using many machines
-   Built-in **fault tolerance**
-   Works well with **massive data**

------

## 3. Hadoop Core Ideas (Very Important)

Hadoop is built on three key ideas:

### 1️⃣ Distributed Storage

Data is split and stored across many machines.

### 2️⃣ Distributed Computing

Computation is sent **to where the data lives**, not the other way around.

### 3️⃣ Fault Tolerance

If a machine fails, Hadoop automatically recovers.

------

## 4. Hadoop Ecosystem Overview

Hadoop is **not a single tool**. It is an ecosystem.

```
Hadoop Ecosystem
│
├── HDFS       (Storage)
├── YARN       (Resource Management)
├── MapReduce  (Computation)
├── Hive       (SQL on Hadoop)
├── HBase      (NoSQL Database)
├── Spark      (Fast Processing Engine)
└── Others...
```

For beginners, focus on **three core components** first.

------

## 5. HDFS – Hadoop Distributed File System

### What Is HDFS?

**HDFS** is Hadoop’s **distributed file system**.

It stores files by:

-   Splitting them into **blocks**
-   Distributing blocks across **multiple machines**
-   Replicating blocks for safety

### Example

A 1GB file might be:

-   Split into 8 blocks
-   Stored on 8 different machines
-   Each block copied 3 times

------

### HDFS Architecture

#### Key Roles

| Role               | Description                              |
| ------------------ | ---------------------------------------- |
| NameNode           | Metadata manager (file names, locations) |
| DataNode           | Stores actual data blocks                |
| Secondary NameNode | Assists with metadata checkpoints        |

>   💡 **Data flows through DataNodes, not the NameNode**

------

### Why HDFS Is Special

-   Optimized for **large files**
-   High **throughput**, not low latency
-   Write once, read many times

------

## 6. YARN – Resource Management

### What Is YARN?

**YARN (Yet Another Resource Negotiator)** manages:

-   CPU
-   Memory
-   Task scheduling

Think of YARN as the **operating system of a Hadoop cluster**.

------

### YARN Components

| Component         | Role                           |
| ----------------- | ------------------------------ |
| ResourceManager   | Global resource controller     |
| NodeManager       | Manages resources on each node |
| ApplicationMaster | Manages a single application   |

------

## 7. MapReduce – Distributed Computing Model

### What Is MapReduce?

**MapReduce** is a programming model for processing large datasets in parallel.

It has **two phases**:

```
Input → Map → Shuffle → Reduce → Output
```

------

### Map Phase

-   Processes input data
-   Produces key-value pairs

Example:

```
Input: "hello hadoop hello"
Output:
hello → 1
hadoop → 1
hello → 1
```

------

### Reduce Phase

-   Aggregates values with the same key

```
hello → 2
hadoop → 1
```

------

### Key Characteristics

-   Highly scalable
-   Disk-based (slower than Spark)
-   Very stable and reliable

------

## 8. Hadoop Cluster Modes

### 1️⃣ Local Mode

-   Single machine
-   Used for learning and testing

### 2️⃣ Pseudo-Distributed Mode

-   One machine acts like a cluster
-   Common for beginners

### 3️⃣ Fully Distributed Mode

-   Multiple machines
-   Production environment

------

## 9. Hadoop vs Traditional Systems

| Feature         | Traditional DB | Hadoop         |
| --------------- | -------------- | -------------- |
| Data Size       | GBs            | TBs–PBs        |
| Schema          | Fixed          | Schema-on-read |
| Fault Tolerance | Limited        | Built-in       |
| Cost            | High           | Lower          |
| Scalability     | Vertical       | Horizontal     |

------

## 10. Hadoop vs Spark (Quick Note)

| Hadoop MapReduce | Spark             |
| ---------------- | ----------------- |
| Disk-based       | Memory-based      |
| Slower           | Faster            |
| Simple model     | Rich APIs         |
| Very stable      | Widely used today |

>   💡 In modern systems, **Hadoop + Spark + Hive** is very common.

------

## 11. Typical Hadoop Use Cases

-   Log analysis
-   Data warehousing
-   ETL pipelines
-   Clickstream analysis
-   Data lakes

------

## 12. Learning Path for Beginners

Recommended order:

1.  Linux basics
2.  HDFS commands
3.  Hadoop architecture
4.  YARN concepts
5.  MapReduce basics
6.  Hive (SQL on Hadoop)
7.  Spark (next step)

------

## 13. Common Beginner Misconceptions

❌ Hadoop is a database
✔ Hadoop is a **framework**

❌ Hadoop is outdated
✔ Hadoop is the **foundation** of modern big data

❌ Hadoop is only MapReduce
✔ Hadoop includes **HDFS + YARN + more**

------

## 14. Simple Mental Model

>   Hadoop =
>   **HDFS (storage)** + **YARN (resource manager)** + **Compute engine**

------

## 15. What Should You Learn Next?

If you want to continue, good next topics are:

-   HDFS command line usage
-   Installing Hadoop in pseudo-distributed mode
-   Hive SQL basics
-   Spark vs MapReduce

