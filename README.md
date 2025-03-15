# AWS-Glue-Pipeline-ETL

This repository demonstrates an **ETL (Extract, Transform, Load)** pipeline using **AWS Glue**, **Amazon S3**, **AWS Athena**, and **PySpark**. It shows how to:
1. Read source data (CSV files) from an S3 bucket.  
2. Crawl the data to automatically generate table schemas in the AWS Glue Data Catalog.  
3. Transform and join data using a PySpark job in AWS Glue.  
4. Write the transformed data (in Parquet format) to a target S3 bucket.  
5. (Optional) Crawl the target bucket to register the new schema and query it with Athena.

![Architecture Diagram](https://github.com/Playmaker3334/AWS-glue-pipeline-ETL/blob/main/architecture.png)

## Overview

- **Source S3 Bucket**: Contains the original CSV files (`sales.csv` and `products.csv`).  
- **AWS Glue Crawler**: Scans the source S3 bucket and creates the Glue Data Catalog tables.  
- **AWS Glue ETL Job**: Reads the data from the Data Catalog, transforms and joins it, and writes output to the target S3 bucket.  
- **Target S3 Bucket**: Stores the final Parquet files.  
- **AWS Glue Crawler (Target)**: An optional second crawler for the final data, allowing Athena queries on the transformed results.  
- **AWS Athena**: Used to preview, validate, and query both the original and transformed datasets.

## Project Contents

- **`sales.csv`** – Sample sales data (source).  
- **`products.csv`** – Sample products data (source).  
- **`result.csv`** – (Optional) Example of transformed or joined data.  
- **`architecture.png`** – Architecture diagram for the pipeline.  
- **`README.md`** – This file.

## Prerequisites

1. **AWS Account** with privileges to create S3 buckets, AWS Glue Crawlers, and Athena queries.  
2. **IAM Role** granting AWS Glue access to S3.  
3. **CSV Files** (`sales.csv` and `products.csv`) located in the source S3 bucket.

## Step-by-Step

1. **Create S3 Buckets**  
   - A *source* bucket for raw data (e.g., `cloud-source-origin`).  
   - A *target* bucket for storing transformed data (e.g., `cloud-target-origin`).  
   - (Optionally) another bucket that AWS Glue might use for assets/scripts.

2. **Upload CSVs to Source Bucket**  
   - Place `sales.csv` and `products.csv` in the *source* bucket.

3. **Create a Glue Crawler (Source)**  
   - Point the crawler to your *source* bucket.  
   - Run it to detect schema and create tables (e.g., `sales_csv`, `products_csv`) in the Glue Data Catalog (in a database like `db-origin`).

4. **Validate with Athena**  
   - Use Athena to preview the new tables and ensure the schema matches the CSV files.

5. **AWS Glue ETL Job**  
   - Write (or upload) your PySpark script to AWS Glue.  
   - This script should:  
     1. Read from the Data Catalog (`dyf_sales`, `dyf_products`).  
     2. Cast columns to correct data types.  
     3. Join the two tables on `product_id`.  
     4. Calculate additional fields, e.g. `total_amount = quantity * price`.  
     5. Write the results in **Parquet** to your *target* S3 bucket.  

6. **(Optional) Create a Glue Crawler (Target)**  
   - If you want to query the final dataset via Athena, create a second crawler to scan the newly generated Parquet files in the *target* bucket.  
   - This crawler will create a new table in the Glue Data Catalog for the transformed dataset.

7. **Query the Final Data**  
   - Use Athena to run queries against the new table to confirm your transformations.
