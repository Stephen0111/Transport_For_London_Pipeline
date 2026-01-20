# TfL Passenger Flow & Revenue Optimization with Databricks & Delta Lake  
*A Real-Time Transport Analytics Lakehouse for Network Capacity Planning*

This project implements a **production-grade, real-time analytics pipeline** designed to optimise **passenger flow, congestion management, and fare revenue** for **Transport for London (TfL)**.

By combining **live transport data from the TfL Unified API** with **simulated Oyster card tap-in / tap-out events**, the system models passenger journeys across the London transport network and provides **actionable insights** for:
- Network capacity planning  
- Peak-time congestion monitoring  
- Revenue forecasting  
- Service disruption impact analysis  
- “What-if” transport scenarios (line closures, new stations)

The pipeline is built using a **modern Lakehouse architecture** on **Databricks**, leveraging **PySpark Structured Streaming**, **Delta Live Tables**, and **Databricks SQL Dashboards** for near real-time decision support.

---

## Architecture Overview
![TfL Architecture](Assets/tfl_architecture.png)


---

## Key Features

### Real-Time Transport Data Ingestion
- **Live streaming ingestion** of:
  - Tube & bus arrival predictions
  - Service disruption events
- Implemented using **PySpark Structured Streaming**
- Handles:
  - Event-time processing
  - Late-arriving data with **watermarks**
  - Deduplication of arrival messages
- Data stored in **Delta Bronze tables** for replayability and auditing

### Simulated Oyster Card Journey Generation
- Uses **Python Faker** to generate realistic:
  - Tap-in and tap-out events
  - Origin–destination patterns
  - Time-of-day demand curves
- Mimics real-world passenger behaviour:
  - Morning and evening peaks
  - Station popularity weighting
- Ingested daily as **batch Bronze data**

### Journey Sessionization & Validation (Silver Layer)
- Implemented using **Delta Live Tables (DLT)**
- Matches tap-in and tap-out events into complete journeys using:
  - Session windows
  - Timeout logic for incomplete journeys
- Performs:
  - Station code validation
  - Duplicate removal
  - Late-event reconciliation
- Produces clean, analytics-ready **Silver tables**

### Passenger Flow & Revenue Analytics (Gold Layer)
- PySpark aggregations compute:
  - Hourly passenger flow by station
  - Route-level fare revenue
  - Congestion scores based on volume vs capacity
- Uses:
  - Window functions
  - Time-based aggregations
  - Derived KPIs for operational monitoring
- Supports **what-if modelling**, including:
  - Line closures
  - New station openings
  - Service delay scenarios

### Real-Time Dashboards for Transport Planners
- Built with **Databricks SQL Dashboards**
- Near real-time refresh from Gold tables
- Visualisations include:
  - Station congestion heatmaps
  - Passenger flow trends
  - Revenue vs forecast comparisons
  - Delay impact analysis

---

## Tech Stack

| Layer | Technology | Purpose |
|------|-----------|---------|
| **Streaming** | PySpark Structured Streaming | Real-time arrival ingestion |
| **Batch Processing** | Python, PySpark | Oyster tap simulation |
| **Data Lake** | Delta Lake | ACID-compliant Lakehouse storage |
| **Transformations** | Delta Live Tables | Validated Silver & Gold pipelines |
| **Orchestration** | Databricks Workflows (Airflow optional) | Streaming & batch scheduling |
| **Analytics** | PySpark SQL | Windowed metrics & aggregations |
| **Visualization** | Databricks SQL | Live operational dashboards |
| **Governance** | Unity Catalog | Data lineage & access control |

---

## Data Model (Lakehouse)

### Bronze Layer (Raw)
**bronze_live_arrivals**
- Raw TfL arrival & disruption events
- Append-only streaming table

**bronze_simulated_taps**
- Raw simulated Oyster tap events
- Daily batch ingestion

---

### Silver Layer (Validated)

**silver_complete_journeys**
| Column | Description |
|------|-------------|
| journey_id | Unique journey identifier |
| tap_in_station | Origin station |
| tap_out_station | Destination station |
| start_time | Tap-in timestamp |
| end_time | Tap-out timestamp |
| journey_duration | Travel time in minutes |
| fare | Calculated fare |

**silver_validated_arrivals**
- Cleaned arrival predictions
- Deduplicated and time-aligned

---

### Gold Layer (Analytics)

**gold_station_hourly_flow**
| Column | Description |
|------|-------------|
| station_code | Station identifier |
| hour | Hour window |
| passenger_count | Total passengers |
| congestion_score | Volume vs capacity metric |

**gold_route_revenue**
| Column | Description |
|------|-------------|
| route | Origin–destination pair |
| total_revenue | Aggregated fare revenue |
| journey_count | Number of journeys |

**gold_congestion_alerts**
- Flags stations exceeding congestion thresholds
- Supports operational alerting

---

## Orchestration Strategy

- **Streaming job**:
  - Always-on Structured Streaming pipeline
  - Handles live TfL data
- **Batch job**:
  - Daily 02:00 execution for tap simulation & aggregation
- Implemented using **Databricks Workflows**
- **Apache Airflow** can be used for:
  - Cross-platform orchestration
  - Multi-cloud data dependencies

---

## Business Impact

This project demonstrates how modern data engineering can support:
- Smarter transport planning decisions
- Reduced congestion during peak travel
- Improved fare revenue forecasting
- Faster response to service disruptions
- Scalable analytics for city-scale infrastructure

---

## Why This Project Matters

This repository showcases:
- **Streaming + batch hybrid architecture**
- **Real-world public sector use case**
- **Advanced PySpark patterns** (watermarks, sessionization)
- **Lakehouse best practices**
- **Analytics engineering for operational decision-making**

It reflects the type of platform used by **transport authorities, smart cities, and large-scale mobility providers** to manage complex, high-volume event data in real time.
