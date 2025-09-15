## What is CDC? 
Change Data Capture (CDC) is a technique that identifies and captures changes (inserts, updates, deletes) in a source system and makes them available downstream. 
Instead of reloading entire tables, CDC only moves the delta, making data pipelines faster and more efficient.

## How does it work?
Usually works in three ways:
- Log-based: Monitors database transaction logs for changes.
- Trigger-based: Uses database triggers to capture row-level changes
- Timestamp-based: Compares timestamps in tables to identify changes

## Why is the CDC important?
- Real-time analytics: Keep our data warehouse or lake up-to-date without waiting for nightly batch jobs.
- System integration: Sync changes across microservices or between on-prem and cloud systems.
- Efficiency: Minimise resource usage by only moving changed records.
- Scalability: Support event-driven architectures and streaming platforms like Kafka.

## CDC Tools 
- Debezium (open-source, Kafka-based CDC).
- AWS DMS, Azure Data Factory, Google Datastream.
- Databricks Auto Loader + Delta Live Tables.
- Enterprise tools like Fivetran or Informatica.

## Quick Example:
A customer updates their email in our Customers table.

Without CDC → our pipeline reloads the whole table. 
With CDC → only the single change event flows downstream to CRM, marketing, and analytics.

The result: real-time sync, less cost, happier customers.

If we’re building data platforms today, CDC is no longer optional—it’s a competitive advantage.