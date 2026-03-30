# ETL Rerun-Safe Data Warehouse Architecture (Informatica)

## 📌 Overview

This project implements a robust ETL pipeline using Informatica PowerCenter with a **rerun-safe architecture** driven by a business date parameter.

The solution is designed to ensure:

* Data consistency across reruns
* Full recoverability of data
* Controlled reprocessing for any specific business date

---

## 🏗️ Architecture Design

The ETL pipeline follows a layered architecture:

* **STG (Staging Layer)** → Raw data ingestion
* **DIM (Dimension Layer)** → Processed dimensional data (SCD handling)
* **FACT (Fact Layer)** → Final analytical data

Execution is orchestrated using:

* Layer-based worklets
* A centralized master workflow
* Audit logging mechanism

---

## 🔁 Rerun-Safe Design

### 🔹 Business Date Parameter

A global parameter controls ETL execution:

```ini
$$BUSINESS_DATE = YYYY-MM-DD
```

This enables:

* Running ETL for any specific day
* Independence from system date
* Safe and repeatable reruns

---

## 🧱 Staging Layer (STG)

Before loading data, a cleanup step is applied:

```sql
DELETE FROM STG_TABLE
WHERE CAST(LOADDATE AS DATE) = CAST('$$BUSINESS_DATE' AS DATE)
AND LOADDATE < DATEADD(DAY, 1, CAST('$$BUSINESS_DATE' AS DATE));
```

### ✔️ Key Benefits:

* Prevents duplicate data
* Preserves historical data
* Guarantees rerun safety

---

## 🔄 Dimension Layer (SCD Type 1 & Type 2)

The project implements Slowly Changing Dimensions with rerun-safe logic.

### 🔹 SCD Type 1

* Overwrites existing records with latest values

### 🔹 SCD Type 2

* Preserves historical data by:

  * Removing newly inserted records during rerun
  * Reopening previously closed records

### ✔️ Result:

* Full data consistency
* Accurate historical tracking
* Safe reruns without corruption

---

## 📊 Fact Layer

Fact tables follow the same rerun-safe strategy:

```sql
DELETE FROM FACT_TABLE
WHERE CAST(LOADDATE AS DATE) = CAST('$$BUSINESS_DATE' AS DATE)
AND LOADDATE < DATEADD(DAY, 1, CAST('$$BUSINESS_DATE' AS DATE));
```

### ✔️ Key Benefits:

* Prevents duplicate fact records
* Ensures data integrity
* Facts are rebuilt from validated STG & DIM layers

📌 Fact loading executes only after successful completion of STG and DIM layers

---

## ⚙️ Worklet Design

Each layer (STG, DIM, FACT) follows a standardized structure:

1. Audit START task
2. Session execution
3. Audit SUCCESS task
4. Audit FAILED task

### 🚨 Failure Handling:

* Session failure → Worklet fails
* Worklet failure → Workflow stops

---

## 🔗 Workflow Orchestration

A master workflow controls execution:

```
STG → DIM → FACT
```

### Execution Rules:

* DIM runs only if STG succeeds
* FACT runs only if DIM succeeds
* No downstream execution after failure

### ✔️ Guarantees:

* Correct sequencing
* Strong data integrity

---

## 📋 Audit Table

The audit table provides:

* Execution tracking per session
* Status monitoring:

  * STARTED
  * SUCCESS
  * FAILED
* Last successful run date for rollback

---

## 🛠️ Tools & Technologies

* Informatica PowerCenter
* SQL
* Data Warehouse Modeling
* ETL Design Patterns



