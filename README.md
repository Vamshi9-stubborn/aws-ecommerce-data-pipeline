# aws-ecommerce-data-pipeline
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
