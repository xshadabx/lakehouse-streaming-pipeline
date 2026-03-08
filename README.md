# 🏗️ End-to-End Modern Lakehouse Streaming Pipeline

> Production-grade streaming lakehouse built on Azure + Databricks — from raw event ingestion to governed Gold layer, orchestrated on a 2-minute cadence.

---

## 🧠 Architecture Overview

```
Event Hub (Streaming Source)
        ↓
Azure Data Factory (Metadata-Driven ELT)
        ↓
Databricks (Bronze → Silver OBT → Gold)
        ↓
Lakeflow Jobs (Orchestration — 2min cadence)
```

This pipeline combines **real-time streaming** with **initial load**, unified into a single Silver One Big Table (OBT) using CDC-aware processing — no separate batch and streaming stacks.

---

## ⚙️ Stack

| Layer | Technology |
|---|---|
| Event Streaming | Azure Event Hub |
| Ingestion & ELT | Azure Data Factory (metadata-driven, HTTPS/API parameterization) |
| Processing Engine | Azure Databricks (PySpark + Spark Structured Streaming) |
| Storage Format | Delta Lake |
| Orchestration | Databricks Lakeflow Jobs |
| Templating | Jinja2 (metadata-driven SQL generation) |
| CDC | Auto CDC via Databricks streaming tables |

---

## 📐 Pipeline Design

### 1. Event Generation
- Custom Python function simulates real-time event stream
- Events published to **Azure Event Hub** continuously

### 2. Ingestion via ADF
- Azure Data Factory pulls data via **HTTPS / REST API** — treating Event Hub as a parameterized API source
- **Metadata-driven parameterization** — pipeline configuration stored externally, zero hardcoding
- Supports both **streaming ingestion** and **initial load** from GitHub repository

### 3. Bronze Layer (Raw)
- Raw events landed into Databricks **Bronze Delta tables**
- Data loaded using **SAS token authentication** via pandas → Spark DataFrame conversion
- Schema preserved as-is from source

### 4. Silver Layer — One Big Table (OBT)
This is the core transformation layer. Key design decisions:

- **Streaming + Initial Load unified** — both sources merged into a single Silver OBT
- **Mapping tables joined** — reference/lookup data applied during transformation
- **Jinja2-templated SQL** — transformation logic generated dynamically from metadata, enabling schema-agnostic processing
- **Materialized Views** — incremental processing using Databricks materialized views
- **Auto CDC** — Change Data Capture handled automatically via Databricks streaming tables
- Result: a single, clean, governed Silver OBT ready for Gold consumption

### 5. Gold Layer
- **Dimension tables** — extracted by selecting all dimensional columns, grouped and deduplicated
- **Fact table** — core metrics and measures with foreign key relationships to dims
- Designed for direct consumption by BI tools, APIs, and downstream AI systems

### 6. Orchestration
- **Databricks Lakeflow Jobs** orchestrate the full pipeline
- Pre-processing notebooks handle Bronze ingestion
- ETL notebooks handle Silver → Gold transformation
- **2-minute cadence** — near real-time data freshness end-to-end

---

## 🔑 Key Design Principles

### Metadata-Driven Architecture
All pipeline configuration — source mappings, transformation logic, table definitions — stored as metadata. Adding a new data source requires **zero code changes**. Only metadata updates.

### Zero Hardcoding
Jinja2 templating generates SQL dynamically from metadata at runtime. The pipeline is a **reusable engine**, not a collection of one-off scripts.

### Unified Streaming + Batch
No separate streaming and batch pipelines. A single Silver OBT combines real-time events with historical initial load data — simplifying operations and reducing latency.

### CDC-Aware Processing
Auto CDC ensures that **inserts, updates, and deletes** are all handled correctly without manual merge logic. Data always reflects the latest state of the source.

---

## 📊 Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                     DATA SOURCES                            │
│  Python Event Generator ──→ Azure Event Hub                 │
│  GitHub Repository (Initial Load + Mapping Tables)          │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          ↓
┌─────────────────────────────────────────────────────────────┐
│              AZURE DATA FACTORY (ELT)                       │
│  Metadata-driven HTTPS/API parameterization                 │
│  Dynamic pipeline config — zero hardcoding                  │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          ↓
┌─────────────────────────────────────────────────────────────┐
│                  DATABRICKS LAKEHOUSE                       │
│                                                             │
│  BRONZE                                                     │
│  └── Raw Delta tables (pandas → Spark, SAS auth)            │
│                                                             │
│  SILVER OBT                                                 │
│  ├── Streaming tables (real-time events)                    │
│  ├── Initial load (historical data)                         │
│  ├── Mapping table joins                                    │
│  ├── Jinja2 metadata-driven SQL transformations             │
│  ├── Materialized views (incremental)                       │
│  └── Auto CDC (insert/update/delete handling)               │
│                                                             │
│  GOLD                                                       │
│  ├── Dimension tables (grouped, deduplicated)               │
│  └── Fact table (metrics + FK relationships)                │
└─────────────────────────┬───────────────────────────────────┘
                          │
                          ↓
┌─────────────────────────────────────────────────────────────┐
│              LAKEFLOW JOBS (ORCHESTRATION)                  │
│  Bronze preprocessing → Silver ETL → Gold ETL               │
│  2-minute cadence — near real-time end-to-end               │
└─────────────────────────────────────────────────────────────┘
```

---

## 🚀 Why This Architecture

This pipeline represents the **ceiling of Databricks Structured Streaming** — combining every major capability of the modern lakehouse stack into a single coherent system:

- ✅ Real-time streaming via Event Hub
- ✅ Metadata-driven ELT via ADF
- ✅ Unified streaming + batch in Silver OBT
- ✅ Jinja2 templated SQL for zero-hardcoding transformations
- ✅ Auto CDC for complete change tracking
- ✅ Materialized views for incremental efficiency
- ✅ Governed Gold layer for downstream consumption
- ✅ Lakeflow orchestration for production reliability

---

## 🛠️ Technologies Used

- **Azure Event Hub** — Real-time event streaming
- **Azure Data Factory** — Metadata-driven pipeline orchestration
- **Azure Databricks** — Unified analytics and AI platform
- **Delta Lake** — ACID-compliant storage layer
- **PySpark** — Distributed data processing
- **Spark Structured Streaming** — Real-time stream processing
- **Jinja2** — SQL template engine for metadata-driven transformations
- **Databricks Lakeflow Jobs** — Production pipeline orchestration
- **Python** — Event simulation and preprocessing

---

## 📈 Production Readiness

| Feature | Status |
|---|---|
| Real-time streaming | ✅ |
| Initial load support | ✅ |
| CDC handling | ✅ |
| Metadata-driven config | ✅ |
| Incremental processing | ✅ |
| Orchestrated scheduling | ✅ |
| Gold layer for consumption | ✅ |

---

*Built as a portfolio demonstration of end-to-end modern lakehouse architecture on Azure + Databricks.*
