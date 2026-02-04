# Informatica Data Warehouse ETL Project

## Overview
This project demonstrates an end-to-end Data Warehouse ETL implementation using
Informatica PowerCenter and SQL Server.

The solution covers:
- Staging, Dimension, and Fact layers
- SCD Type 1 and SCD Type 2
- Business-date–driven rerun strategy
- Audit logging and orchestration
- Failure-safe workflow design

## Architecture
- Tool: Informatica PowerCenter
- Database: SQL Server
- Layers: STG → DIM → FACT
- Orchestration: Master workflow with layer-based worklets

## Key Features
- BUSINESS_DATE parameter for controlled reruns
- Pre-SQL cleanup by business date
- SCD Type 2 using start_date / end_date
- Central AUDIT_TABLE with STARTED / SUCCESS / FAILED statuses
- Workflow failure propagation (no silent failures)

## Workflow Design
- WF_ETL_MASTER
  - WL_STG_ALL
  - WL_DIM_ALL
  - WL_FACT_ALL

## Audit Strategy
Each session updates an audit table before and after execution using a reusable script.

## Rerun Strategy
- Reruns are handled by deleting STG data by BUSINESS_DATE
- Dimensions and facts are re-derived safely



## Author
Habiba
