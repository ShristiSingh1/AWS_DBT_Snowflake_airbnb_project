# 🏠 Airbnb Data Engineering Pipeline

An end-to-end data engineering project that transforms Airbnb booking, host, and listing data into analytics-ready datasets using **AWS S3, Snowflake, dbt, SQL, Python, and Git**.

The project follows a **Bronze → Silver → Gold** medallion architecture and demonstrates incremental transformations, data quality testing, SCD Type 2 snapshots, reusable dbt macros, and dimensional modeling.

---

## 📌 Project Overview

The pipeline takes raw Airbnb CSV data through multiple stages:

```text
Airbnb CSV Data
      │
      ▼
   AWS S3
      │
      ▼
Snowflake STAGING
      │
      ▼
   🥉 BRONZE
  Raw / lightly
  transformed data
      │
      ▼
   🥈 SILVER
 Cleaned and
 standardized data
      │
      ▼
    🥇 GOLD
 Analytics-ready
 business models
```

The final Gold layer combines bookings, listings, and host information to support downstream analytics and reporting.

---

## 🎯 Project Objectives

* Build an end-to-end cloud data pipeline
* Load raw Airbnb data into Snowflake
* Organize transformations using a medallion architecture
* Develop reusable dbt models and macros
* Implement incremental data processing
* Track historical changes using SCD Type 2
* Add data quality tests
* Create analytics-ready datasets
* Manage the project using Git and GitHub

---

## 🛠️ Technology Stack

| Technology     | Purpose                          |
| -------------- | -------------------------------- |
| **AWS S3**     | Cloud storage for source data    |
| **Snowflake**  | Cloud data warehouse             |
| **dbt**        | Data transformation and testing  |
| **SQL**        | Data transformation and modeling |
| **Python**     | Supporting project workflows     |
| **Git/GitHub** | Version control                  |
| **Jinja**      | Dynamic SQL generation           |

---

# 🏗️ Architecture

The project uses three main transformation layers.

### 🥉 Bronze Layer

The Bronze layer contains data loaded from the staging layer with minimal transformation.

Main models:

* `bronze_bookings`
* `bronze_hosts`
* `bronze_listings`

Purpose:

* Preserve source information
* Apply basic transformations
* Provide a consistent starting point for downstream models

---

### 🥈 Silver Layer

The Silver layer cleans and standardizes the Bronze data.

Main models:

* `silver_bookings`
* `silver_hosts`
* `silver_listings`

Transformations include:

* Data cleaning
* Standardization
* Validation
* Derived fields
* Business classifications

---

### 🥇 Gold Layer

The Gold layer contains analytics-ready models.

Main models:

* `fact`
* `obt` — One Big Table
* Supporting ephemeral models

The Gold layer combines information from bookings, listings, and hosts to make the data easier to consume for analytics and reporting.

---

# 📊 Data Model

The project works with three primary datasets:

### Bookings

Contains information about Airbnb bookings and booking-related attributes.

### Listings

Contains property-level information such as listing details and pricing.

### Hosts

Contains host-level information and attributes.

These datasets are transformed through the Bronze and Silver layers before being combined in the Gold layer.

---

# 🔄 Incremental Processing

The project uses dbt incremental models to avoid rebuilding the entire dataset when new data is added.

Example:

```sql
{{ config(materialized='incremental') }}

{% if is_incremental() %}

WHERE CREATED_AT >
(
    SELECT COALESCE(
        MAX(CREATED_AT),
        '1900-01-01'
    )
    FROM {{ this }}
)

{% endif %}
```

This allows the pipeline to process only the required new or changed records instead of processing the complete dataset every time.

---

# 🕒 SCD Type 2 Snapshots

dbt snapshots are used to preserve historical changes to important datasets.

Snapshots are implemented for:

* `dim_bookings`
* `dim_hosts`
* `dim_listings`

This allows historical versions of records to be maintained instead of overwriting previous values.

For example:

```text
Listing
   │
   ├── Version 1 → Old price
   │
   └── Version 2 → Updated price
```

This makes it possible to analyze how data changed over time.

---

# 🧩 dbt Macros

The project contains reusable dbt macros to reduce repetitive SQL logic.

Examples include:

* `tag.sql` — price categorization
* `multiply.sql` — reusable mathematical logic
* `trimmer.sql` — string transformation
* `generate_schema_name.sql` — schema organization

Example:

```sql
{{ tag('CAST(PRICE_PER_NIGHT AS INT)') }}
```

Macros allow common logic to be reused across multiple models.

---

# 🧪 Data Quality & Testing

Data quality checks are included to validate the transformed datasets.

The testing approach includes:

* Unique key validation
* NULL checks
* Referential integrity
* Source data validation
* Business rule validation

dbt tests help identify problems before incorrect data reaches downstream analytics.

---

# 📁 Project Structure

```text
AWS_DBT_Snowflake_airbnb_project/
│
├── README.md
├── pyproject.toml
├── uv.lock
├── main.py
│
├── SourceData/
│   ├── bookings.csv
│   ├── hosts.csv
│   └── listings.csv
│
├── DDL/
│   ├── ddl.sql
│   └── resources.sql
│
└── aws_dbt_snowflake_project/
    │
    ├── dbt_project.yml
    │
    ├── models/
    │   ├── sources/
    │   │   └── sources.yml
    │   │
    │   ├── bronze/
    │   │   ├── bronze_bookings.sql
    │   │   ├── bronze_hosts.sql
    │   │   └── bronze_listings.sql
    │   │
    │   ├── silver/
    │   │   ├── silver_bookings.sql
    │   │   ├── silver_hosts.sql
    │   │   └── silver_listings.sql
    │   │
    │   └── gold/
    │       ├── fact.sql
    │       ├── obt.sql
    │       └── ephemeral/
    │
    ├── macros/
    │   ├── generate_schema_name.sql
    │   ├── multiply.sql
    │   ├── tag.sql
    │   └── trimmer.sql
    │
    ├── snapshots/
    │   ├── dim_bookings.yml
    │   ├── dim_hosts.yml
    │   └── dim_listings.yml
    │
    ├── tests/
    │   └── source_tests.sql
    │
    ├── analyses/
    │   ├── explore.sql
    │   ├── if_else.sql
    │   └── loop.sql
    │
    └── seeds/
```

---

# 🚀 Setup

## Prerequisites

* Python 3.12+
* Snowflake account
* AWS account
* dbt Core
* dbt Snowflake adapter
* Git

---

## 1. Clone the Repository

```bash
git clone https://github.com/ShristiSingh1/AWS_DBT_Snowflake_airbnb_project.git

cd AWS_DBT_Snowflake_airbnb_project
```

---

## 2. Create a Virtual Environment

### Windows

```bash
python -m venv .venv

.venv\Scripts\activate
```

### Linux / macOS

```bash
python -m venv .venv

source .venv/bin/activate
```

---

## 3. Install Dependencies

```bash
pip install -e .
```

---

## 4. Configure Snowflake

Create a dbt profile in:

```text
~/.dbt/profiles.yml
```

Example structure:

```yaml
aws_dbt_snowflake_project:

  outputs:

    dev:

      type: snowflake
      account: <your-account>
      user: <your-user>
      password: <your-password>

      role: ACCOUNTADMIN
      database: AIRBNB
      schema: DBT_SCHEMA
      warehouse: COMPUTE_WH

      threads: 4

  target: dev
```

**Do not commit credentials or passwords to GitHub.**

---

# ▶️ Running the Project

Navigate to the dbt project:

```bash
cd aws_dbt_snowflake_project
```

### Test the Snowflake connection

```bash
dbt debug
```

### Run models

```bash
dbt run
```

### Run only Bronze models

```bash
dbt run --select bronze.*
```

### Run only Silver models

```bash
dbt run --select silver.*
```

### Run only Gold models

```bash
dbt run --select gold.*
```

### Run tests

```bash
dbt test
```

### Run snapshots

```bash
dbt snapshot
```

### Build the project

```bash
dbt build
```

---

# 📈 Analytics Layer

The Gold layer provides datasets that can be used for downstream analytics and visualization.

Potential use cases include:

* Booking analysis
* Listing performance
* Host analysis
* Pricing analysis
* Revenue analysis
* Property-level comparisons

---

# 🔐 Security Practices

The project follows basic data engineering security practices:

* Credentials are not stored in source code
* Snowflake connection details are separated from dbt models
* Environment variables can be used for sensitive values
* `.gitignore` is used to prevent accidental commits of local configuration and credentials

---

# 📚 What I Learned

Through this project, I worked with:

* AWS S3
* Snowflake data warehousing
* dbt models
* dbt sources
* dbt snapshots
* Incremental models
* SCD Type 2
* Jinja templating
* dbt macros
* SQL transformations
* Data quality testing
* Medallion architecture
* Git/GitHub

---

# 👤 Author

**Shristi Singh**

Data Engineering Portfolio Project

**Technologies:**
AWS S3 · Snowflake · dbt · SQL · Python · Git

