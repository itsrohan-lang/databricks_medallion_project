# Databricks Medallion Architecture Project

This project demonstrates an end-to-end data engineering pipeline using Databricks (Community Edition), PySpark, and Delta Lake. It implements the Medallion Architecture pattern to progressively process, clean, and aggregate e-commerce data.

## 🏗️ Architecture Overview

The pipeline processes data through three distinct Delta Lake layers:

1. **Landing Zone:** Simulated upstream system drops raw JSON transaction data.
2. **Bronze Layer (Raw):** Ingests raw JSON as an append-only Delta table, adding basic metadata (ingestion timestamp and source file path) for lineage.
3. **Silver Layer (Cleansed):** Cleans and conforms the data. This layer handles:
    * Dropping invalid records (e.g., null users, negative amounts).
    * Deduplicating records based on `user_id` and an ISO 8601 timestamp (`event_time`).
    * Incrementally updating the table using Delta Lake's `MERGE INTO` (Upsert) capabilities.
4. **Gold Layer (Aggregated):** Business-level aggregates (e.g., Customer Lifetime Value, transaction counts) optimized for BI and downstream analytics.

## 🚀 Technologies Used
* **Platform:** Databricks Community Edition (Free Tier)
* **Compute:** Single-node Databricks Runtime Cluster
* **Language:** Python / PySpark
* **Storage Format:** Delta Lake

## ⚙️ Setup Instructions

1. **Databricks Account:** Sign up for Databricks Community Edition.
2. **Git Integration:** 
   * Go to **User Settings** -> **Linked accounts** -> **Git integration**.
   * Link your GitHub/GitLab account using a Personal Access Token.
3. **Clone Repository:** 
   * Go to **Workspace** -> **Home** -> **Create** -> **Git folder**.
   * Paste this repository's URL.
4. **Create Cluster:** 
   * Navigate to **Compute** -> **Create Compute**.
   * Create a standard cluster (Note: Community Edition clusters terminate after 2 hours of inactivity).

## 🏃‍♂️ Running the Pipeline

Since Databricks Workflows are not available in the Community Edition, this pipeline is orchestrated via a master notebook. 

1. Attach your cluster to the `Master_Orchestrator` notebook.
2. Run the notebook to execute the layers sequentially using the `%run` magic command:
   * `config`
   * `landing_ingestion`
   * `bronze_layer`
   * `silver_layer`
   * `gold_layer`

## 💡 Key Features Implemented
* **Time Travel & ACID Transactions:** Leveraged via Delta Lake.
* **Window Functions:** Used PySpark `Window.partitionBy()` to extract the latest event per `user_id` prior to merging.
* **Timestamp Parsing:** Handled strict ISO 8601 timestamps with UTC offsets (e.g., `2026-09-19T08:00:06.006+00:00`).