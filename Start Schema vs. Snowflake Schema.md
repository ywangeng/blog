Schema design doesn’t just affect our data warehouse — it shapes how fast, clear, and flexible our reports will be.

## ⭐ Star Schema
- Structure: A central fact table with denormalized dimensions.
- Example: A Sales Fact Table with dimensions like Customer, Product, Date, and Store all directly connected.
- Pros: \
  ✅ Simple for BI tools (drag & drop in Power BI, Tableau, Looker) \
  ✅ Faster queries → great for dashboards and KPIs
- Cons: \
  ❌ Redundant data (e.g., "City" repeated in every Store record)  \
  ❌ Less flexible for complex hierarchies

## ❄️ Snowflake Schema
- Structure: Dimensions are normalized into sub-dimensions.
- Example: Instead of "Store" holding everything, we’d split into Store → City → Region → Country tables. Reports join through these layers.
- Pros: \
  ✅ Less redundancy, more storage-efficient \
  ✅ Easier to model real-world hierarchies (helpful for drill-down reports, like Sales by Region → Country → City)
- Cons: \
  ❌ More joins = slower queries \
  ❌ Harder for business users to understand

## 💡 For Reporting:
- Use Star Schema when we want speed, simplicity, and self-service reporting.
- Use Snowflake Schema when we need accuracy in hierarchies and less redundancy.

## 🔹 Denormalized (Star-style)
- Meaning: Flattened dimension tables with all related attributes stored together, even if repeated.
- Sales Example:
  - Sales Fact → SalesID, ProductID, CustomerID, StoreID, DateID, Amount
  - Product → ProductID, ProductName, CategoryName (Category stored directly here)
  - Customer → CustomerID, CustomerName, CityName, RegionName, CountryName (all in one table)
  - Store → StoreID, StoreName, CityName, RegionName, CountryName

To run “Total Sales by Country,” we just join Fact → Customer, and CountryName is already there.

## 🔹 Normalized (Snowflake-style)
- Meaning: Split into multiple related tables to remove redundancy.
- Sales Example:
  - Sales Fact → SalesID, ProductID, CustomerID, StoreID, DateID, Amount
  - Product → ProductID, ProductName, CategoryID
  - Category → CategoryID, CategoryName
  - Customer → CustomerID, CustomerName, CityID
  - City → CityID, CityName, RegionID → RegionID, RegionName → CountryID, CountryName

To run a report like “Total Sales by Country,” we join Fact → Customer → City → Region → Country.
