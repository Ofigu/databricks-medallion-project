# data-with-baraa-databricks-bootcamp

![Databricks](https://img.shields.io/badge/Databricks-EA2D2E?style=for-the-badge&logo=databricks&logoColor=white)
![Delta Lake](https://img.shields.io/badge/Delta%20Lake-0A0A0A?style=for-the-badge&logo=delta&logoColor=white)
![PySpark](https://img.shields.io/badge/PySpark-E25A1C?style=for-the-badge&logo=apache-spark&logoColor=white)

---

## Project Overview

This project demonstrates the implementation of a modern **Data Lakehouse** using the **Medallion Architecture (Bronze, Silver, and Gold layers)** on Databricks.

It integrates disparate data sources from **CRM** and **ERP** systems to build a centralized, analytics-ready data warehouse for a retail bike business.

### The pipeline handles:
- Data ingestion  
- Complex transformations  
- Data quality enforcement  
- Dimensional modeling  

---

## Technical Stack

- **Platform:** Databricks  
- **Language:** PySpark (Python), Spark SQL  
- **Storage:** Delta Lake  
- **Orchestration:** Databricks Notebook Workflows (`dbutils.notebook.run`)  

---

## Medallion Architecture Implementation

### 1. Bronze Layer (Raw Ingestion)

**Source Data**  
Ingests raw CSV files from two primary sources:

**CRM**
- Customer info  
- Product info  
- Sales details  

**ERP**
- Customer birthday data (AZ12)  
- Location data (A101)  
- Product category metadata (G1V2)  

**Process**  
Performs a **1:1 ingestion** from raw sources into Delta tables using **overwrite mode** to maintain a fresh copy of the source landing zone.

---

### 2. Silver Layer (Transformation & Quality)

The Silver layer focuses on cleaning, standardizing, and unifying data schemas.

**Standardization**
- Trimming whitespace from all string columns  

**Normalization**
- Converting gender (M/F) and marital status (S/M) abbreviations into descriptive labels  
- Standardizing country codes (e.g., `DE`, `US`) to full country names  
- Converting maintenance flags to boolean values  

**Data Integrity**
- Parsing complex business keys (e.g., extracting category IDs from product keys)  
- Filtering out rows with null primary identifiers  
- Validating birth dates against the current date to ensure historical accuracy  

**Financial Logic**
- Correcting sales price relationships by recalculating prices where values are missing or invalid based on sales amount and quantity  

---

### 3. Gold Layer (Dimensional Modeling)

The Gold layer organizes data into a **Star Schema** optimized for BI and reporting.

- **Dim_Customers**  
  - Unifies CRM customer profiles with ERP location and birth date data  
  - Implements surrogate keys for optimized join performance  

- **Dim_Products**  
  - Joins product specifications with category metadata  

- **Fact_Sales**  
  - Aggregates transaction details  
  - Links them to the validated dimensions via surrogate keys  

---

## Project Structure and Orchestration

The project is organized into **modular notebooks** to ensure maintainability:

- **bronze_script** – Handles the initial load  
- **silver_orchestration** – Sequentially runs cleaning scripts for all CRM and ERP tables  
- **gold_orchestration** – Builds the final analytical tables  
