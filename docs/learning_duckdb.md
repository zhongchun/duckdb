# Learning DuckDB: A Practical Roadmap

A comprehensive guide for learning DuckDB from beginner to advanced user.

## What is DuckDB?

DuckDB is a high-performance analytical database system designed to be:
- **Fast** - Optimized for analytical queries (OLAP)
- **Reliable** - ACID compliant with full SQL support
- **Portable** - Runs in-process, no server needed
- **Easy to use** - Query files directly without loading data

## Getting Started

### Installation

**macOS:**
```bash
brew install duckdb
```

**Other platforms:**
Download from [https://duckdb.org/docs/installation/](https://duckdb.org/docs/installation/)

**Python:**
```bash
pip install duckdb
```

**R:**
```r
install.packages("duckdb")
```

### Your First Query

Start the DuckDB CLI:
```bash
duckdb
```

Try some basic queries:
```sql
-- Simple calculation
SELECT 42;

-- Create a table
CREATE TABLE users (id INTEGER, name VARCHAR, age INTEGER);

-- Insert data
INSERT INTO users VALUES
    (1, 'Alice', 30),
    (2, 'Bob', 25),
    (3, 'Charlie', 35);

-- Query the table
SELECT * FROM users WHERE age > 26;
```

## DuckDB's Superpowers

### 1. Query Files Directly

No need to load data first - query CSV, Parquet, JSON files directly:

```sql
-- Query a CSV file
SELECT * FROM 'data.csv';

-- Query a Parquet file
SELECT * FROM 'data.parquet';

-- Query multiple files with wildcards
SELECT * FROM 'data/*.parquet';

-- Query with filters (pushed down to file reader)
SELECT * FROM 'large_file.parquet' WHERE date > '2024-01-01';
```

### 2. Rich SQL Dialect

DuckDB supports advanced SQL features:

```sql
-- Window functions
SELECT
    name,
    age,
    AVG(age) OVER (ORDER BY age ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) as moving_avg
FROM users;

-- Common Table Expressions (CTEs)
WITH young_users AS (
    SELECT * FROM users WHERE age < 30
)
SELECT * FROM young_users;

-- Nested correlated subqueries
SELECT name FROM users u1
WHERE age > (SELECT AVG(age) FROM users u2 WHERE u2.id < u1.id);
```

### 3. Complex Nested Types

Work with arrays, structs, and maps:

```sql
-- Arrays
SELECT [1, 2, 3] as numbers;
SELECT list_sum([1, 2, 3, 4, 5]);

-- Structs
SELECT {'name': 'Alice', 'age': 30} as person;

-- Maps
SELECT map(['a', 'b'], [1, 2]) as my_map;

-- Nested structures
CREATE TABLE events (
    user_id INTEGER,
    tags VARCHAR[],
    metadata STRUCT(source VARCHAR, timestamp TIMESTAMP)
);
```

### 4. Friendly SQL Extensions

DuckDB makes SQL easier to use:

```sql
-- Column name inference
SELECT * EXCLUDE (password) FROM users;
SELECT * REPLACE (UPPER(name) AS name) FROM users;

-- ASOF joins for time-series
SELECT * FROM prices
ASOF JOIN trades
ON prices.symbol = trades.symbol
AND prices.timestamp >= trades.timestamp;

-- Pivot tables
PIVOT cities ON year USING sum(population);
```

## Learning Path

### Week 1: Basic SQL and File Querying

**Goals:**
- Master basic SQL (SELECT, WHERE, JOIN, GROUP BY)
- Learn to query CSV and Parquet files
- Understand DuckDB's CLI

**Exercises:**

1. **Download sample data:**
```bash
# Download a sample CSV
curl -O https://raw.githubusercontent.com/duckdb/duckdb-web/main/data/weather.csv
```

2. **Query the file:**
```sql
-- Start DuckDB
duckdb

-- Query without loading
SELECT * FROM 'weather.csv' LIMIT 10;

-- Aggregate data
SELECT city, AVG(temp) as avg_temp
FROM 'weather.csv'
GROUP BY city;

-- Save results
COPY (
    SELECT city, AVG(temp) as avg_temp
    FROM 'weather.csv'
    GROUP BY city
) TO 'results.parquet';
```

3. **Create a persistent database:**
```bash
# Create a database file
duckdb mydata.db
```

```sql
-- Load data into a table
CREATE TABLE weather AS
SELECT * FROM 'weather.csv';

-- Now you can query the table
SELECT * FROM weather;
```

### Week 2: Advanced SQL Features

**Goals:**
- Master window functions
- Learn CTEs and subqueries
- Understand joins (including ASOF joins)

**Exercises:**

1. **Window functions:**
```sql
-- Ranking
SELECT
    name,
    age,
    RANK() OVER (ORDER BY age DESC) as age_rank,
    ROW_NUMBER() OVER (ORDER BY age DESC) as row_num
FROM users;

-- Running totals
SELECT
    date,
    amount,
    SUM(amount) OVER (ORDER BY date) as running_total
FROM transactions;

-- Partitioned windows
SELECT
    category,
    product,
    sales,
    AVG(sales) OVER (PARTITION BY category) as category_avg
FROM products;
```

2. **Complex joins:**
```sql
-- Multiple joins
SELECT
    u.name,
    o.order_id,
    p.product_name
FROM users u
JOIN orders o ON u.id = o.user_id
JOIN products p ON o.product_id = p.id;

-- ASOF join for time-series
SELECT
    t.timestamp,
    t.symbol,
    t.price as trade_price,
    q.bid,
    q.ask
FROM trades t
ASOF JOIN quotes q
ON t.symbol = q.symbol
AND t.timestamp >= q.timestamp;
```

### Week 3: Working with Nested Types

**Goals:**
- Understand arrays, structs, and maps
- Learn to unnest and manipulate nested data
- Work with JSON data

**Exercises:**

1. **Arrays:**
```sql
-- Create arrays
SELECT [1, 2, 3, 4, 5] as numbers;

-- Array functions
SELECT
    list_sum([1, 2, 3]) as total,
    list_avg([1, 2, 3]) as average,
    list_contains([1, 2, 3], 2) as has_two;

-- Unnest arrays
SELECT unnest([1, 2, 3, 4, 5]) as value;

-- Array aggregation
SELECT list_agg(name) as all_names FROM users;
```

2. **Structs:**
```sql
-- Create structs
SELECT {'name': 'Alice', 'age': 30, 'city': 'NYC'} as person;

-- Access struct fields
SELECT
    person.name,
    person.age
FROM (SELECT {'name': 'Alice', 'age': 30} as person);

-- Struct from columns
SELECT struct_pack(name, age) as person FROM users;
```

3. **JSON:**
```sql
-- Read JSON file
SELECT * FROM 'data.json';

-- Parse JSON string
SELECT json_extract('{"name": "Alice", "age": 30}', '$.name');

-- Work with nested JSON
CREATE TABLE events (data JSON);
INSERT INTO events VALUES
    ('{"user": "alice", "tags": ["a", "b"]}'),
    ('{"user": "bob", "tags": ["c", "d"]}');

SELECT
    json_extract_string(data, '$.user') as user,
    json_extract(data, '$.tags') as tags
FROM events;
```

### Week 4: Python/R Integration

**Goals:**
- Use DuckDB from Python/R
- Integrate with pandas/dplyr
- Understand zero-copy data transfer

**Python Examples:**

```python
import duckdb
import pandas as pd

# Connect to DuckDB
con = duckdb.connect('mydata.db')

# Query to DataFrame
df = con.execute("SELECT * FROM users").df()

# Query pandas DataFrame directly
df = pd.DataFrame({'a': [1, 2, 3], 'b': [4, 5, 6]})
result = con.execute("SELECT * FROM df WHERE a > 1").df()

# Register DataFrame as view
con.register('my_view', df)
con.execute("SELECT * FROM my_view")

# Use relation API
rel = con.table('users')
result = rel.filter('age > 25').project('name, age').df()

# Write results
con.execute("COPY (SELECT * FROM users) TO 'output.parquet'")
```

**R Examples:**

```r
library(duckdb)
library(dplyr)

# Connect to DuckDB
con <- dbConnect(duckdb::duckdb(), "mydata.db")

# Query to data frame
df <- dbGetQuery(con, "SELECT * FROM users")

# Use dplyr with DuckDB
users_tbl <- tbl(con, "users")
result <- users_tbl %>%
  filter(age > 25) %>%
  select(name, age) %>%
  collect()

# Register R data frame
dbWriteTable(con, "my_table", iris)

# Disconnect
dbDisconnect(con)
```

### Week 5+: Advanced Topics

**Topics to explore:**

1. **Performance optimization:**
   - Indexes and statistics
   - Query profiling
   - Parallel execution
   - Memory management

2. **Extensions:**
   - httpfs (read from S3/HTTP)
   - spatial (GIS data)
   - Full-text search
   - Custom extensions

3. **Advanced features:**
   - Prepared statements
   - Transactions
   - Views and macros
   - User-defined functions

## Practical Examples

### Example 1: Analyzing CSV Files

```sql
-- Analyze sales data
SELECT
    DATE_TRUNC('month', date) as month,
    category,
    SUM(amount) as total_sales,
    COUNT(*) as num_transactions,
    AVG(amount) as avg_transaction
FROM 'sales.csv'
WHERE date >= '2024-01-01'
GROUP BY month, category
ORDER BY month, total_sales DESC;
```

### Example 2: Combining Multiple Files

```sql
-- Combine multiple Parquet files
CREATE TABLE all_data AS
SELECT * FROM 'data/2024-*.parquet';

-- Or query directly
SELECT
    year,
    month,
    SUM(revenue) as total_revenue
FROM 'data/*.parquet'
GROUP BY year, month
ORDER BY year, month;
```

### Example 3: Data Transformation Pipeline

```sql
-- Extract, transform, load
CREATE TABLE clean_data AS
WITH raw AS (
    SELECT * FROM 'raw_data.csv'
),
cleaned AS (
    SELECT
        TRIM(name) as name,
        CAST(age AS INTEGER) as age,
        LOWER(email) as email,
        COALESCE(city, 'Unknown') as city
    FROM raw
    WHERE name IS NOT NULL
)
SELECT * FROM cleaned;

-- Export results
COPY clean_data TO 'clean_data.parquet' (FORMAT PARQUET);
```

### Example 4: Time-Series Analysis

```sql
-- Calculate moving averages
SELECT
    date,
    value,
    AVG(value) OVER (
        ORDER BY date
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ) as moving_avg_7day,
    value - LAG(value, 1) OVER (ORDER BY date) as daily_change
FROM time_series
ORDER BY date;
```

## Performance Tips

### 1. Use Appropriate File Formats

```sql
-- Parquet is much faster than CSV for large datasets
-- Convert CSV to Parquet:
COPY (SELECT * FROM 'large_file.csv')
TO 'large_file.parquet' (FORMAT PARQUET);
```

### 2. Filter Early

```sql
-- Good: Filter pushed down to file reader
SELECT * FROM 'large_file.parquet'
WHERE date > '2024-01-01';

-- Less efficient: Filter after loading
SELECT * FROM (SELECT * FROM 'large_file.parquet')
WHERE date > '2024-01-01';
```

### 3. Use Profiling

```sql
-- Enable profiling
PRAGMA enable_profiling;
PRAGMA profiling_output='profile.json';

-- Run your query
SELECT * FROM large_table WHERE condition;

-- View profile
PRAGMA profiling_output;
```

### 4. Leverage Parallelism

```sql
-- DuckDB automatically uses multiple threads
-- Control with:
SET threads TO 4;

-- Check current setting:
SELECT current_setting('threads');
```

## Common Patterns

### Pattern 1: Data Exploration

```sql
-- Quick data overview
DESCRIBE SELECT * FROM 'data.csv';

-- Summary statistics
SUMMARIZE SELECT * FROM 'data.csv';

-- Sample data
SELECT * FROM 'data.csv' USING SAMPLE 1000;
```

### Pattern 2: Data Cleaning

```sql
-- Remove duplicates
CREATE TABLE unique_users AS
SELECT DISTINCT * FROM users;

-- Handle nulls
SELECT
    COALESCE(name, 'Unknown') as name,
    COALESCE(age, 0) as age
FROM users;

-- Standardize text
SELECT
    TRIM(LOWER(email)) as email,
    REGEXP_REPLACE(phone, '[^0-9]', '') as phone
FROM contacts;
```

### Pattern 3: Aggregation and Reporting

```sql
-- Multi-level aggregation
SELECT
    region,
    category,
    COUNT(*) as count,
    SUM(amount) as total,
    AVG(amount) as average,
    MIN(amount) as min_amount,
    MAX(amount) as max_amount
FROM sales
GROUP BY region, category
ORDER BY region, total DESC;
```

## Resources

### Official Documentation
- [DuckDB Documentation](https://duckdb.org/docs/) - Comprehensive docs
- [SQL Reference](https://duckdb.org/docs/sql/introduction) - SQL syntax guide
- [Data Import](https://duckdb.org/docs/data/overview) - File formats and loading

### Learning Resources
- [DuckDB Blog](https://duckdb.org/news/) - Deep dives and announcements
- [Examples](https://duckdb.org/docs/guides/overview) - Practical guides
- [Discord Community](https://discord.gg/tcvwpjfnZx) - Ask questions

### Tools and Integrations
- [Python Client](https://duckdb.org/docs/api/python/overview)
- [R Client](https://duckdb.org/docs/api/r)
- [JDBC Driver](https://duckdb.org/docs/api/jdbc)
- [ODBC Driver](https://duckdb.org/docs/api/odbc/overview)

## Next Steps

1. **Install DuckDB** and try the basic examples
2. **Find a dataset** you're interested in (CSV or Parquet)
3. **Query it directly** without loading
4. **Try advanced features** like window functions and nested types
5. **Integrate with Python/R** for your workflow
6. **Join the community** on Discord to ask questions

Remember: DuckDB is designed to be easy to use. Start simple, experiment, and gradually explore more advanced features as you need them!
