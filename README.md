# Retail Data Pipeline — Apache Airflow (Production-Grade ETL System)

A scalable, production-ready data engineering pipeline built with Apache Airflow (Astro CLI) that orchestrates end-to-end data processing from multiple sources into a structured PostgreSQL data warehouse with incremental and idempotent loading capabilities.

---

# System Overview

This project implements a robust ETL orchestration framework designed to simulate real-world data engineering workflows in modern data platforms.

The pipeline integrates multiple data sources and processing stages:

* Batch ingestion from CSV files (Retail transactional data)
* API ingestion from REST endpoints (DummyJSON Products API)
* Data transformation and normalization layer
* Data validation and quality enforcement layer
* Incremental loading into PostgreSQL warehouse

The system is fully orchestrated using Apache Airflow DAGs with a modular, production-oriented architecture.

---

# Architecture Design

```id="faang_arch"
CSV Source ───────┐
                  │
                  ▼
           ┌──────────────┐
           │ Extraction   │  (API + CSV)
           └─────┬────────┘
                 ▼
           ┌──────────────┐
           │ Transformation│  (Cleaning + Structuring)
           └─────┬────────┘
                 ▼
           ┌──────────────┐
           │ Validation   │  (Data Quality Checks)
           └─────┬────────┘
                 ▼
           ┌────────────────────────┐
           │ Incremental Loader     │
           │ PostgreSQL (Upsert)    │
           └─────┬──────────────────┘
                 ▼
           ┌──────────────┐
           │ Observability │
           │ Logging Layer │
           └──────────────┘
```

---

# Core Engineering Features

## Multi-Source Data Ingestion

* Batch ingestion from structured CSV files
* REST API ingestion with resilient HTTP handling

## Fault-Tolerant API Layer

* Retry mechanism with exponential backoff
* Safe handling of unstable API responses
* Consistent request configuration using custom headers

## Data Quality Enforcement

* Schema validation before persistence
* Null, type, and integrity checks
* Prevention of corrupted data propagation downstream

## Incremental Processing Engine

* High-watermark strategy for change tracking
* Only new or updated records are processed per run
* Fully idempotent execution model

## Data Warehouse Design

* Dimensional modeling (fact and dimension schema)
* Upsert-based ingestion using ON CONFLICT
* Optimized structure for analytical workloads

## Orchestration Layer (Airflow)

* Modular DAG-based pipeline design
* Clear task dependencies
* Scalable and maintainable workflow structure

## Observability & Logging

* Structured logging per pipeline stage
* Row-level tracking across ETL stages
* Error tracing for debugging and monitoring

---

# Tech Stack

| Layer                  | Technology      |
| ---------------------- | --------------- |
| Workflow Orchestration | Apache Airflow  |
| Local Runtime          | Astro CLI       |
| Processing             | Python (Pandas) |
| Data Sources           | CSV + REST API  |
| Storage                | PostgreSQL      |
| Infrastructure         | Docker          |
| Observability          | Python Logging  |

---

# System Design Structure

```id="faang_structure"
dags/
  └── retail_etl_dag.py          # Airflow orchestration layer

include/
  ├── extract.py                 # Data ingestion (API + CSV)
  ├── transform.py               # Data processing layer
  ├── validate.py               # Data quality enforcement
  ├── load_postgre.py           # Warehouse loading logic
  └── data/
      └── retail_sales.csv

tests/
Dockerfile
requirements.txt
.astro/
```

---

# Data Processing Strategy

## Incremental Loading (High-Watermark Design)

The pipeline follows a stateful ingestion model:

* Maintains last processed watermark
* Extracts only new records on each run
* Prevents duplicate data ingestion
* Supports safe re-execution (idempotency)

---

# Data Warehouse Schema

## dim_products

Stores product metadata used for analytical queries.

* product_id (Primary Key)
* title
* category
* price
* quantity

---

## fact_sales

Stores transactional sales records.

* sale_id (Primary Key)
* product_id (Foreign Key)
* customer_id
* quantity
* price
* sale_date

---

# Observability Model

Each pipeline execution provides full operational traceability:

* Number of extracted records per source
* Transformation output metrics
* Load success and failure counts
* Watermark updates across runs
* Error logs with full stack traces
قولّي
