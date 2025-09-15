## Data Warehouse
- Purpose: Designed for structured, cleaned data used in analytics and reporting.
- Schema: Schema-on-Write(define upfront).
- Data type: Structured (tables, schema-on-write).
- Use Cases: Reporting, BI.
- Performance: Optimised for fast SQL queries and dashboards.
- Examples: Snowflake, Google BigQuery, Amazon Redshift, Azure Synapse. 

## Data Lake
- Purpose: Centralised storage for all types of data at scale.
- Schema: Schema-on-Read (When a user queries the data, that’s when the schema is applied)
- Data type: Structured, semi-structured, unstructured (logs, JSON, video, IoT).
- Use Cases: ML, AI, real-time streaming.
- Performance: Flexible but requires more processing before analytics.
- Examples: Amazon S3 + Athena, Azure Data Lake, Google Cloud Storage

## LakeHouse
Combined scalability and flexibility of a Data Lake with the performance and reliability of a Data Warehouse. 
To handle all types of data (structured, semi-structured, unstructured) while supporting both BI analytics and AI/ML workloads. 
- Unified storage: Store raw, curated data in one system. 
- Schema enforcement and governance
- Support for BI and ML 
- ACID transactions 
- Cost efficiency: Cheaper than warehouse for storage, while retaining warehouse-level performance. 
- Example:
  - Databricks Delta Lake
  - Apache Iceberg 
  - Snowflake Arctic
  - Google Cloud BigLake
