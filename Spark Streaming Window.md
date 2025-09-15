## Spark Steaming Window

Streaming windows are the foundation of the time-based aggregations in Spark Structured Streaming. 

### What is a streaming window?
A window groups events by event-time interval. 
Streaming data arrives continuously, sometimes late, Spark lets we define windows so we can compute aggregations 
like counts per minute.

### Types of Windows in Pyspark? 
#### (1) Tumbling Window (fixed, non-overlapping)
- Example: 10-minute tumbling window -> [09:00-09:10), [09:10-09:20)…
- Each event belongs to exactly one window.

```python 
from pyspark.sql.functions import window, col, count

agg = df.withWatermark("event_time", "10 minutes") \
    .groupBy(
        window(col("event_time"), "10 minutes"),  # tumbling
        col("event")
    ).agg(count(“*”).alias(“event_count"))

Time ----->

09:00     09:10       09:20      09:30
|---------------|-------------|-------------|
       [W1]        [W2]         [W3]

Event @09:07 → goes to W1 only  
Event @09:15 → goes to W2 only  
```

#### (2) Sliding Window (overlapping)
- Example: 15-minute window,  sliding every 5 mins -> [09:00-09:15), [09:05-09:20), [09:10-09:25)…
- An event belongs to multiple windows.

```python
from pyspark.sql.functions import window, col, count

agg = df.withWatermark("event_time", "20 minutes") \
    .groupBy(
        window(col("event_time"), "15 minutes”, “5 minutes”),  # sliding
        col("event")
    ).agg(count(“*”).alias(“event_count"))

Event @09:07 → belongs to W1 (09:00–09:15) and W2 (09:05–09:20)  
Event @09:12 → belongs to W1, W2, W3 (09:10–09:25)
```

#### (3) Session Window (dynamic, activity-based)
- Groups events into sessions separated by gaps of inactivity.
- Example: session gap 10 mins -> if a user’s events are within 10 min of each other, they belong to the same session. 
Windows expand until inactivity gap > 10 min

```python
from pyspark.sql.functions import session_window, col, count

agg = df.withWatermark("event_time", "20 minutes") \
    .groupBy(
        session_window(col("event_time"), "10 minutes”),  # session 
        col(“user_id")
    ).agg(count(“*”).alias(“event_in_session"))

Time ----->

User’s events:   E1     E2   (gap 4 min)   E3     (gap 12 min)   E4

Sessions:      [---- Session 1 ----]                                                  [--- Session 2 ---]

E1 @09:00  
E2 @09:04 (within 10-min gap → same session)  
E3 @09:08 (still within gap → same session)  
E4 @09:20 (gap > 10 → new session)  
```

### How to deduplicate duplicates of the same event in sliding window? 
Sine sliding windows overlap, so the same event legitimately belongs to multiple windows. Usually we want to reduplicate 
duplicates of the same event, without stopping the event from appearing in multiple valid windows. 

#### (1) Global dedup by event_id (across all windows)
Only keeps the first occurrence of each event_id within the watermark horizon, then windows/aggregates.

```python
from pyspark.sql.functions import col, window

deduped = (
  df 
  .withWatermark("event_time", "20 minutes")   # must set watermark for dropDuplicates in streaming
  .dropDuplicates(["event_id"])                            # global by event_id
)

agg = (
  deduped
  .groupBy(
    window(col("event_time"), "15 minutes", "5 minutes"),
    col("key")
  )
  .count()
)
```

#### (2) Per-window dedup (same event_id counted once per window)
Do an event appear in multiple overlapping windows, but not double-count replays inside the same window.
```python
from pyspark.sql.functions import col, window

with_win = df.select(
    window(col("event_time"), "15 minutes", "5 minutes").alias("win"),
    col("event_time"),
    col("event_id"),
    col("key")
)

deduped_per_window = (
  with_win
  .withWatermark("event_time", "20 minutes")
  .dropDuplicates(["win", "event_id", "key"])  # unique per (window, event_id, key)
)

agg = deduped_per_window.groupBy("win", "key").count()
```

#### (3) countDistinct(event_id) inside the aggregation
Dedups within each group/window automatically.
```python
from pyspark.sql.functions import col, window, countDistinct

agg = (
  Df
  .withWatermark("event_time", "20 minutes")
  .groupBy(
    window(col("event_time"), "15 minutes", "5 minutes"),
    col("key")
  )
  .agg(countDistinct("event_id").alias("events"))
)
```
