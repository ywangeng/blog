Databricks is a unified data and AI platform built on top of Apache Spark.

It separates storage and compute, and adds a control plane to manage everything. 

## Components of Databricks Architecture:
### (1) Control Plane
In distributed/cloud systems, Control Plane = the layer that manages and orchestrates resources, but doesn’t actually 
process the data itself.
- In Databricks’ cloud account (Azure, AWS, GCP).
- Hosts the Databricks web UI, REST APIs, job scheduler, cluster manager, notebooks, workspace metadata.
- Not store our data.
- Manage cluster creation and orchestration. 

### (2) Data Plane
- The actual compute and storage live.
- Runs on our cloud subscription.
- Clusters that run Spark jobs.
- Databricks File System (DBFS): an abstraction layer over cloud storage (ADLS, GCS, S3).
- The data always remains in our cloud storage (Parquet, Delta Lake, etc)

### (3) Delta Lake
- Open-source storage format for reliable data lakes.
- Adds ACID transactions, schema enforcement, and time travel on top of cloud object storage.
- Ensures consistent reads/writes even in big distributed jobs.

### (4) Compute Layer (Clusters / SQL Warehouse)
- Clusters: run Spark jobs, ML training, ETL pipelines.
- SQL Warehouses: optimised clusters for BI dashboards and ad-hoc SQL.
- Auto-scaling + auto-termination saves cost.

### (5) ML / AI Layer
- MLflow (built-in) for experiment tracking, model registry, deployment.
- Integration with Databricks Runtime ML (preconfigured clusters with ML/DL libraries).

### (6) Security and Governance 
- Unity Catalog: centralised governance (table access, lineage, audit logs). 
- Fine-grained access control at user/group/table/column level.
- Data never leaves our cloud tenant.
