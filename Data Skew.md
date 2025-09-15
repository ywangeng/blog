## What is Data Skew? 
Data skew, also known as data skewness, occurs when data isn't evenly distributed across partitions or nodes in a 
distributed processing system(like Spark, Databricks, Hadoop). 

In distributed computing systems like Apache Spark, data is divided into partitions to be processed in parallel across 
multiple nodes. Data skew occurs when some partitions receive disproportionately more data than others, creating 
"hotspots" that cause significant performance bottlenecks.

## Example of Data Skew:
Imagine we have a dataset of sales transactions:

| TransactionID | CustomerID | Amount |
|---------------|------------|--------|
| 1             | C1         | 200    |
| 2             | C2         | 100    |
| 3             | C1         | 650    |
| 4             | C1         | 350    |
| 5             | C1         | 159    |
| ...           | ...        | ...    |

Assure CustomerID = C1 makes up 90% of transactions, while other customers have only a few. 

Now if we run this in Spark:
```python 
df.groupBy("CustomerID").agg(sum("Amount"))
```
Spark shuffles data based on CustomerID. 

Partition handling C1 will get 90% of all rows → skewed partition. 

Other partitions finish quickly, but the job waits for the heavy one.

## How to identify Data Skew?
- Spark UI / Databricks UI 
  - Look at shuffle stage → if one task takes much longer than others, likely skew.
  - Skewed partition has much higher data volume.
- Data exploration
  - Check for highly skewed key distribution:
  ```python
  df.groupBy("CustomerID").count().orderBy("count", ascending=False).show()
  ```
  - If one or few keys dominate, skew exists.
- Symptoms
  - Long-tail tasks (few tasks take way longer than others).
  - Jobs stuck at 98-99% for a long time.


## How to fix Data Skew?
A couple of options we can choose depending on we use case:

### (1) Salting (Adding randomness to keys)
Break skewed keys into multiple sub-keys.
Example:
```python
from pyspark.sql import functions as F

# Add a random salt for skewed keys
df = df.withColumn("salt", F.floor(F.rand() * 10))  # 10 buckets
df_salted = df.withColumn("salted_key", F.concat(df.CustomerID, F.lit("_"), df.salt))

# Group by salted key
agg = df_salted.groupBy("salted_key").agg(F.sum("Amount"))

# Remove salt in the final aggregation
result = agg.withColumn("CustomerID", F.split("salted_key", "_")[0]) \
            .groupBy("CustomerID").agg(F.sum("sum(Amount)"))
```
This spreads the heavy key (C1) across multiple partitions.

### (2) Broadcast join (when one dataset is small)
If skew happens in a join, you can broadcast the smaller dataset:
```python 
df1.join(F.broadcast(df2), “CustomerID")
```
This avoids shuffle.

### (3) Skew hint in Spark SQL
When Spark detects or we know that a join key is skewed, we can explicitly tell Spark to handle skewed keys differently 
using a hint.
This helps Spark avoid putting all skewed key rows into one single shuffle partition. Instead, Spark splits them into 
multiple partitions so they can be processed in parallel.
```python
df1.hint("skew", "key").join(df2, "key")
```

### (4) Repartition 
Instead of ```df.repartition(“CustomerID”)```,
```python 
df.repartition(10)  # fixed number of partitions
```
to distribute. 