In data warehousing, a Slowly Changing Dimension (SCD) is a way to manage and track how attribute value in a dimension 
table change over time. 

Unlike CDC which captures immediate row-level changes, SCD focuses on how we preserve history. 

Example:
We have a Customer Dimension:

| CustomerID | Name | City |
|------------|------|------|
| 101        | ABC  | A    |

Now ABC moved to new place. 
How to we handle this the dimension table? 

### SCD Types
#### (1) Type 1 - Overwrite
Update the record, no history kept 
Table becomes:

| CustomerID | Name | City |
|------------|------|------|
| 101        | ABC  | C    |

#### (2) Type 2 - Keep History
Insert a new record with validity dates.
Table would be:

| CustomerID | Name | City | ValidFrom  | ValidTo     |
|------------|------|------|------------|-------------|
| 101        | ABC  | A    | 2017-10-31 | 2024-02-10  |
| 101        | ABC  | C    | 2024-02-11 | 9999-12-31  |

Preserves full history.

#### (3) Type 3 - Track History
Add a column for previous value 
Table would be:


| CustomerID | Name | CurrentCity | PreviousCity |
|------------|------|-------------|--------------|
| 101        | ABC  | C           | A            |

This is useful when only the last change matters. 

SCD decides how to keep them, CDC captures the changes. 

