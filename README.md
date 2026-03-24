# ds-2002-project-1

# AdventureWorks Retail Sales Data Mart: ETL Pipeline

## Project Overview
This project demonstrates the design and implementation of an end-to-end ETL (Extract, Transform, Load) pipeline. The goal is to model a "Retail Sales" business process by extracting data from disparate OLTP (Online Transaction Processing) source systems, transforming it, and loading it into a centralized OLAP (Online Analytical Processing) Star Schema Data Mart optimized for post-hoc diagnostic analysis. 

This project was completed individually in accordance with the UVA Honor Policy.

## Architecture & Source Systems
To satisfy the multi-source requirement, data was extracted from three distinct environments:
1. **Relational Database (MySQL):** Core transactional data (`fact_sales_orders_vw`) and product catalogs (`dim_products_vw`) were extracted from the highly normalized `adventureworks` MySQL database.
2. **NoSQL Database (MongoDB Atlas):** Customer demographic profiles were exported to JSON and staged in a cloud-hosted MongoDB Atlas cluster, serving as our document-based NoSQL source.
3. **Local File System (CSV):** Temporal data was generated and stored locally as `dim_date.csv` to act as the standard Date Dimension required for time-series analysis.

## Destination System & Deployment Strategy
* **Data Mart:** The destination system is a newly initialized relational database (`adventureworks_dw`) hosted on a local MySQL server.
* **Schema Design:** The data mart was modeled as a Star Schema, featuring one central Fact table (`fact_sales`) surrounded by three Dimension tables (`dim_customers`, `dim_products`, `dim_date`).
* **Deployment:** Python (via Jupyter Notebooks) was deployed as the primary ETL orchestration tool, utilizing `SQLAlchemy` for MySQL connections, `PyMongo` for MongoDB Atlas connections, and `Pandas` for all in-memory transformations.

## The ETL Process (Code Execution)
The `Project 1.ipynb` script executes the following pipeline:
1. **Extract:** Data is queried and pulled from MySQL, MongoDB Atlas, and the local CSV file into separate Pandas DataFrames.
2. **Transform:** * Source system IDs (Business Keys) are renamed to standardize the schema.
    * Duplicates are purged and heavily null/irrelevant columns are dropped from the dimensions to optimize the schema width.
    * Dimension DataFrames are pushed to the new Data Mart, automatically generating auto-incrementing integer Primary Keys (Surrogate Keys).
    * The raw Sales Fact DataFrame is merged with the generated Surrogate Keys via inner joins.
    * Unnecessary descriptive columns are dropped from the Fact table so it solely contains quantitative metrics (e.g., `OrderQty`, `LineTotal`) and foreign keys.
3. **Load:** The finalized Fact DataFrame is written into the center of the Star Schema.

## Validation 
To confirm the integrity of the Data Mart, multiple SQL queries are included at the end of the notebook. These queries successfully aggregate metrics (Revenue and Total Quantity) across the newly established dimensions (grouping by Product Name, Customer City, and Year).

## Repository Contents
* `Project 1.ipynb`: The primary Python ETL script handling all extraction, transformation, and loading.
* `adventureworks_customers.json`: The source data hosted on the MongoDB Atlas cluster.
* `dim_date.csv`: The temporal dimension data sourced from the local file system.
* `README.md`: Project documentation and architecture summary.
