# Serverless E-commerce Data Pipeline on AWS

An end-to-end automated data engineering pipeline that processes e-commerce order data using AWS S3, Glue, Athena, and Lambda built to simulate a real production data lake architecture.

## Architecture

```
CSV Upload (S3 raw bucket)
        ↓
S3 Event Notification
        ↓
AWS Lambda (auto-trigger)
        ↓
AWS Glue ETL Job (PySpark)
   - Converts CSV → Parquet
   - Partitions by category
        ↓
S3 processed bucket
        ↓
AWS Glue Crawler
   - Auto-detects schema
   - Registers in Glue Data Catalog
        ↓
Amazon Athena
   - Serverless SQL queries
   - Business analytics
```

## Problem it solves

Manually running ETL jobs every time new data arrives doesn't scale. This pipeline automates the entire flow — the moment a CSV file lands in S3, it triggers an event-driven chain that transforms, catalogs, and makes the data instantly queryable with zero human intervention.

## Tech stack

| Service | Purpose |
|---|---|
| Amazon S3 | Data lake storage (raw + processed layers) |
| AWS Lambda | Event-driven pipeline trigger |
| AWS Glue (PySpark) | ETL — CSV to Parquet conversion, partitioning |
| AWS Glue Crawler | Automatic schema detection and cataloging |
| Amazon Athena | Serverless SQL analytics |
| AWS IAM | Least-privilege security roles |

## Key results

- **90% cost reduction** on Athena queries by converting CSV to partitioned Parquet
- **Fully automated** — zero manual steps after initial S3 upload
- **Sub-5-minute** data freshness from upload to query-ready

## Files in this repo

| File | Description |
|---|---|
| `etl_orders.py` | Glue PySpark script — reads raw CSV, converts to Parquet, partitions by category |
| `lambda_trigger.py` | Lambda function — triggers Glue job automatically on S3 upload |
| `orders_2024_01.csv` | Sample order data used to test the pipeline |
| `sample_queries.sql` | Example Athena business queries (revenue by category, top cities, etc.) |

## How it works

1. A CSV file containing e-commerce orders is uploaded to the S3 raw bucket
2. S3 fires an `ObjectCreated` event notification
3. Lambda function (`lambda_trigger.py`) is triggered, reads the event, and starts the Glue job via `boto3`
4. Glue ETL job (`etl_orders.py`) reads the CSV, converts it to Parquet format, and writes it to the processed bucket partitioned by `category`
5. Glue Crawler scans the processed data and registers the schema in the Glue Data Catalog
6. Amazon Athena queries the cataloged data directly using standard SQL

## Sample business queries answered

- Total revenue by product category
- Top cities by order volume
- Pending orders requiring action
- Best-selling products by quantity

## Architecture decisions

**Why Parquet over CSV?** Columnar storage means Athena reads only the columns a query needs, instead of scanning entire rows — cutting query costs by up to 90% at scale.

**Why partition by category?** Partition pruning lets Athena skip irrelevant data folders entirely when filtering, dramatically reducing data scanned per query.

**Why event-driven instead of scheduled?** Real-time triggering means data is queryable within minutes of arrival instead of waiting for the next scheduled batch run.
