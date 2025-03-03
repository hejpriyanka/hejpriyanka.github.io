---
title: "Data Transformation Strategies for Clean and Consistent Data"
date: 2025-03-01
author: "Priyanka Yelluru"
summary: "Learn essential data transformation techniques for cleaning, normalizing, and harmonizing data in ETL pipelines."
tags: ["ETL", "Data Engineering", "Data Cleaning", "Normalization", "SQL"]
---

## Introduction
In data processing, raw data from source systems is often inconsistent, messy, or lacks standardization. Data transformation techniques help to clean, normalize, and harmonize this data before it is used for analysis or loaded into structured schemas like a data warehouse. This blog explores multiple techniques used in SQL-based data transformation to convert raw data into clean, meaningful information.

## 1. Removing Unnecessary Whitespace
**Purpose:** Trim leading and trailing whitespace to maintain consistency in textual fields.

### Example:
#### Data Before:
| customer_id | first_name  | last_name   |
|-------------|-------------|-------------|
| 101         | " John "    | " Doe  "    |
| 102         | " Alice  "  | " Brown "   |

#### SQL Transformation:
```sql
SELECT customer_id, 
       TRIM(first_name) AS first_name, 
       TRIM(last_name) AS last_name
FROM customers;
```

#### Data After:
| customer_id | first_name | last_name |
|-------------|------------|-----------|
| 101         | "John"     | "Doe"     |
| 102         | "Alice"    | "Brown"   |

## 2. Handling Missing or Invalid Data
**Purpose:** Replace null or incorrect values with default values or meaningful alternatives.

### Example:
#### Data Before:
| customer_id | email           |
|-------------|-----------------|
| 101         | "john@abc.com"  |
| 102         | NULL            |

#### SQL Transformation:
```sql
SELECT customer_id, 
       COALESCE(email, 'unknown@xyz.com') AS email
FROM customers;
```

#### Data After:
| customer_id | email             |
|-------------|-------------------|
| 101         | "john@abc.com"    |
| 102         | "unknown@xyz.com" |

## 3. Deduplication
**Purpose:** Identify and remove duplicate records while retaining the most recent or relevant entry.

### Example:
#### Data Before:
| customer_id | first_name | last_name | created_at        |
|-------------|------------|-----------|-------------------|
| 101         | John       | Doe       | 2024-01-10 10:00  |
| 101         | John       | Doe       | 2024-01-12 12:30  |

#### SQL Transformation:
```sql
WITH RankedCustomers AS (
    SELECT *, 
           ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY created_at DESC) AS rn
    FROM customers
)
SELECT customer_id, first_name, last_name, created_at
FROM RankedCustomers
WHERE rn = 1;
```

#### Data After:
| customer_id | first_name | last_name | created_at        |
|-------------|------------|-----------|-------------------|
| 101         | John       | Doe       | 2024-01-12 12:30  |

## 4. Normalization and Harmonization
**Purpose:** Standardize categorical values to ensure consistency and accuracy.

### Example:
#### Data Before:
| marital_status | gender  | product_line | country |
|----------------|---------|--------------|---------|
| "S"            | "F"     | "M"          | "DE"    |
| "M"            | "M"     | "R"          | "US"    |
| "Unknown"      | "U"     | "X"          | "USA"   |
| NULL           | ""      | "T"          | ""      |

#### SQL Transformation:
```sql
SELECT 
    CASE marital_status 
        WHEN 'S' THEN 'Single' 
        WHEN 'M' THEN 'Married' 
        ELSE 'n/a' END AS marital_status,
    CASE gender 
        WHEN 'F' THEN 'Female' 
        WHEN 'M' THEN 'Male' 
        ELSE 'n/a' END AS gender,
    CASE product_line 
        WHEN 'M' THEN 'Mountain' 
        WHEN 'R' THEN 'Road' 
        WHEN 'S' THEN 'Other Sales' 
        WHEN 'T' THEN 'Touring' 
        ELSE 'n/a' END AS product_line,
    CASE country 
        WHEN 'DE' THEN 'Germany' 
        WHEN 'US' THEN 'United States' 
        WHEN 'USA' THEN 'United States' 
        ELSE 'n/a' END AS country
FROM products;
```

#### Data After:
| marital_status | gender   | product_line | country         |
|----------------|----------|--------------|-----------------|
| "Single"       | "Female" | "Mountain"   | "Germany"       |
| "Married"      | "Male"   | "Road"       | "United States" |
| "n/a"          | "n/a"    | "n/a"        | "United States" |
| "n/a"          | "n/a"    | "Touring"    | "n/a"           |

## 5. Deriving Values
**Purpose:** Compute missing or dependent values based on existing fields.

### Example:
#### Data Before:
| order_id | quantity | unit_price | total_price |
|----------|----------|------------|-------------|
| 5001     | 3        | NULL       | 60.00       |
| 5002     | 2        | 20.00      | NULL        |
| 5003     | 4        | NULL       | NULL        |
| 5004     | 1        | 15.00      | 15.00       |

#### SQL Transformation:
```sql
SELECT order_id, 
       quantity, 
       COALESCE(unit_price, total_price / quantity) AS unit_price, 
       COALESCE(total_price, quantity * unit_price) AS total_price
FROM orders;
```

#### Data After:
| order_id | quantity | unit_price | total_price |
|----------|----------|------------|-------------|
| 5001     | 3        | 20.00      | 60.00       |
| 5002     | 2        | 20.00      | 40.00       |
| 5003     | 4        | NULL       | NULL        |
| 5004     | 1        | 15.00      | 15.00       |

## 6. Conditional Mapping Using CASE WHEN

### 6a. Standardizing Categorical Data
**Purpose:** Convert inconsistent category formats to a standard format.

#### Data Before:
| product_code | category_code |
|--------------|---------------|
| "P100"       | "A-200"       |
| "P101"       | "B-300"       |
| "P102"       | "C400"        |

#### SQL Transformation:
```sql
SELECT product_code, 
       REPLACE(category_code, '-', '_') AS category_code
FROM products;
```

#### Data After:
| product_code | category_code |
|--------------|---------------|
| "P100"       | "A_200"       |
| "P101"       | "B_300"       |
| "P102"       | "C400"        |

### 6b. Handling Date Transformations
**Purpose:** Format dates from a compact string to a standard date format.

#### Data Before:
| order_date  |
|-------------|
| "20240101"  |
| "20231231"  |
| "20230715"  |

#### SQL Transformation:
```sql
SELECT order_date, 
       TO_DATE(order_date, 'YYYYMMDD') AS formatted_order_date
FROM orders;
```

#### Data After:
| order_date  | formatted_order_date |
|-------------|----------------------|
| "20240101"  | "2024-01-01"         |
| "20231231"  | "2023-12-31"         |
| "20230715"  | "2023-07-15"         |

### 6c. Calculating Order Status
**Purpose:** Derive the shipment status based on shipment and delivery dates.

#### Data Before:
| shipped_date | delivered_date |
|--------------|----------------|
| NULL         | "2024-02-10"   |
| "2024-02-05" | "2024-02-07"   |
| "2024-02-08" | NULL           |
| "2024-02-09" | NULL           |
| NULL         | NULL           |

#### SQL Transformation:
```sql
SELECT shipped_date, delivered_date, 
       CASE 
           WHEN shipped_date IS NULL THEN 'Pending Shipment' 
           WHEN delivered_date IS NOT NULL THEN 'Delivered' 
           ELSE 'In Transit' 
       END AS status
FROM shipments;
```

#### Data After:
| shipped_date | delivered_date | status             |
|--------------|----------------|--------------------|
| NULL         | "2024-02-10"   | "Pending Shipment" |
| "2024-02-05" | "2024-02-07"   | "Delivered"        |
| "2024-02-08" | NULL           | "In Transit"       |
| "2024-02-09" | NULL           | "In Transit"       |
| NULL         | NULL           | "Pending Shipment" |

## 7. Extracting Year and Month from Dates
**Purpose:** Isolate year and month components from a date for time-series analysis.

#### Data Before:
| order_date   |
|--------------|
| "2024-05-15" |
| "2023-11-30" |
| "2024-01-01" |
| "2024-12-25" |

#### SQL Transformation:
```sql
SELECT order_date, 
       EXTRACT(YEAR FROM order_date) AS order_year, 
       EXTRACT(MONTH FROM order_date) AS order_month
FROM orders;
```

#### Data After:
| order_date   | order_year | order_month |
|--------------|------------|-------------|
| "2024-05-15" | 2024       | 5           |
| "2023-11-30" | 2023       | 11          |
| "2024-01-01" | 2024       | 1           |
| "2024-12-25" | 2024       | 12          |


## 8. Aggregating Data
**Purpose:** Summarize data by grouping and applying aggregate functions like SUM, AVG, COUNT, etc.

#### Data Before:
| product_id | quantity |
|------------|----------|
| 101        | 5        |
| 102        | 3        |
| 101        | 2        |
| 103        | 4        |

#### SQL Transformation:
```sql
SELECT product_id, 
       SUM(quantity) AS total_quantity
FROM orders
GROUP BY product_id;
```

#### Data After:
| product_id | total_quantity |
|------------|----------------|
| 101        | 7              |
| 102        | 3              |
| 103        | 4              |

## 9. Pivoting Data
**Purpose:** Transform rows into columns to create a pivot table.

#### Data Before:
| product_id | month | sales |
|------------|-------|-------|
| 101        | Jan   | 100   |
| 101        | Feb   | 150   |
| 102        | Jan   | 200   |
| 102        | Feb   | 250   |

#### SQL Transformation:
```sql
SELECT product_id,
       SUM(CASE WHEN month = 'Jan' THEN sales ELSE 0 END) AS Jan_sales,
       SUM(CASE WHEN month = 'Feb' THEN sales ELSE 0 END) AS Feb_sales
FROM sales
GROUP BY product_id;
```

#### Data After:
| product_id | Jan_sales | Feb_sales |
|------------|-----------|-----------|
| 101        | 100       | 150       |
| 102        | 200       | 250       |

## 10. Unpivoting Data
**Purpose:** Transform columns into rows to normalize a pivot table.

#### Data Before:
| product_id | Jan_sales | Feb_sales |
|------------|-----------|-----------|
| 101        | 100       | 150       |
| 102        | 200       | 250       |

#### SQL Transformation:
```sql
SELECT product_id, 
       'Jan' AS month, Jan_sales AS sales
FROM sales
UNION ALL
SELECT product_id, 
       'Feb' AS month, Feb_sales AS sales
FROM sales;
```

#### Data After:
| product_id | month | sales |
|------------|-------|-------|
| 101        | Jan   | 100   |
| 101        | Feb   | 150   |
| 102        | Jan   | 200   |
| 102        | Feb   | 250   |

## 11. Joining Tables
**Purpose:** Combine data from multiple tables based on a related column.

#### Data Before:
**Orders Table:**
| order_id | customer_id |
|----------|-------------|
| 1        | 101         |
| 2        | 102         |

**Customers Table:**
| customer_id | customer_name |
|-------------|---------------|
| 101         | John Doe      |
| 102         | Alice Brown   |

#### SQL Transformation:
```sql
SELECT orders.order_id, 
       customers.customer_name
FROM orders
JOIN customers ON orders.customer_id = customers.customer_id;
```

#### Data After:
| order_id | customer_name |
|----------|---------------|
| 1        | John Doe      |
| 2        | Alice Brown   |

## 12. Filtering Data
**Purpose:** Extract specific rows based on a condition.

#### Data Before:
| order_id | order_date   | total_amount |
|----------|--------------|--------------|
| 1        | 2024-01-15   | 100.00       |
| 2        | 2024-02-20   | 200.00       |
| 3        | 2024-03-10   | 150.00       |

#### SQL Transformation:
```sql
SELECT order_id, order_date, total_amount
FROM orders
WHERE total_amount > 150.00;
```

#### Data After:
| order_id | order_date   | total_amount |
|----------|--------------|--------------|
| 2        | 2024-02-20   | 200.00       |

## 13. Calculating Running Totals
**Purpose:** Compute cumulative totals over a specified order.

#### Data Before:
| order_id | order_date   | total_amount |
|----------|--------------|--------------|
| 1        | 2024-01-15   | 100.00       |
| 2        | 2024-02-20   | 200.00       |
| 3        | 2024-03-10   | 150.00       |

#### SQL Transformation:
```sql
SELECT order_id, order_date, total_amount,
       SUM(total_amount) OVER (ORDER BY order_date) AS running_total
FROM orders;
```

#### Data After:
| order_id | order_date   | total_amount | running_total |
|----------|--------------|--------------|---------------|
| 1        | 2024-01-15   | 100.00       | 100.00        |
| 2        | 2024-02-20   | 200.00       | 300.00        |
| 3        | 2024-03-10   | 150.00       | 450.00        |

## 14. Calculating Moving Averages
**Purpose:** Compute the average of a specified number of preceding rows.

#### Data Before:
| order_id | order_date   | total_amount |
|----------|--------------|--------------|
| 1        | 2024-01-15   | 100.00       |
| 2        | 2024-02-20   | 200.00       |
| 3        | 2024-03-10   | 150.00       |
| 4        | 2024-04-05   | 300.00       |

#### SQL Transformation:
```sql
SELECT order_id, order_date, total_amount,
       AVG(total_amount) OVER (ORDER BY order_date ROWS BETWEEN 1 PRECEDING AND CURRENT ROW) AS moving_avg
FROM orders;
```

#### Data After:
| order_id | order_date   | total_amount | moving_avg |
|----------|--------------|--------------|------------|
| 1        | 2024-01-15   | 100.00       | 100.00     |
| 2        | 2024-02-20   | 200.00       | 150.00     |
| 3        | 2024-03-10   | 150.00       | 175.00     |
| 4        | 2024-04-05   | 300.00       | 225.00     |

## 15. Data Type Conversion
**Purpose:** Convert data from one type to another for consistency and compatibility.

#### Data Before:
| order_id | order_date   | total_amount |
|----------|--------------|--------------|
| 1        | "2024-01-15" | "100.00"     |
| 2        | "2024-02-20" | "200.00"     |

#### SQL Transformation:
```sql
SELECT order_id, 
       TO_DATE(order_date, 'YYYY-MM-DD') AS order_date, 
       CAST(total_amount AS DECIMAL(10, 2)) AS total_amount
FROM orders;
```

#### Data After:
| order_id | order_date | total_amount |
|----------|------------|--------------|
| 1        | 2024-01-15 | 100.00       |
| 2        | 2024-02-20 | 200.00       |

## 16. Removing Duplicates
**Purpose:** Eliminate duplicate rows from a dataset to ensure data integrity.

#### Data Before:
| customer_id | email           |
|-------------|-----------------|
| 101         | "john@abc.com"  |
| 102         | "alice@xyz.com" |
| 101         | "john@abc.com"  |

#### SQL Transformation:
```sql
SELECT DISTINCT customer_id, email
FROM customers;
```

#### Data After:
| customer_id | email           |
|-------------|-----------------|
| 101         | "john@abc.com"  |
| 102         | "alice@xyz.com" |

## 17. Calculating Percentages
**Purpose:** Compute the percentage of a value relative to a total.

#### Data Before:
| product_id | sales |
|------------|-------|
| 101        | 100   |
| 102        | 200   |
| 103        | 300   |

#### SQL Transformation:
```sql
SELECT product_id, 
       sales, 
       (sales / SUM(sales) OVER ()) * 100 AS sales_percentage
FROM products;
```

#### Data After:
| product_id | sales | sales_percentage |
|------------|-------|------------------|
| 101        | 100   | 16.67            |
| 102        | 200   | 33.33            |
| 103        | 300   | 50.00            |

## 18. String Concatenation
**Purpose:** Combine multiple string fields into a single field.

#### Data Before:
| first_name | last_name |
|------------|-----------|
| John       | Doe       |
| Alice      | Brown     |

#### SQL Transformation:
```sql
SELECT first_name, 
       last_name, 
       CONCAT(first_name, ' ', last_name) AS full_name
FROM customers;
```

#### Data After:
| first_name | last_name | full_name   |
|------------|-----------|-------------|
| John       | Doe       | John Doe    |
| Alice      | Brown     | Alice Brown |

## 19. Conditional Aggregation
**Purpose:** Aggregate data based on a condition.

#### Data Before:
| product_id | sales | region |
|------------|-------|--------|
| 101        | 100   | North  |
| 102        | 200   | South  |
| 101        | 150   | North  |
| 102        | 250   | South  |

#### SQL Transformation:
```sql
SELECT product_id, 
       SUM(CASE WHEN region = 'North' THEN sales ELSE 0 END) AS north_sales,
       SUM(CASE WHEN region = 'South' THEN sales ELSE 0 END) AS south_sales
FROM sales
GROUP BY product_id;
```

#### Data After:
| product_id | north_sales | south_sales |
|------------|-------------|-------------|
| 101        | 250         | 0           |
| 102        | 0           | 450         |

## 20. Data Masking
**Purpose:** Protect sensitive data by masking it.

#### Data Before:
| customer_id | email           |
|-------------|-----------------|
| 101         | "john@abc.com"  |
| 102         | "alice@xyz.com" |

#### SQL Transformation:
```sql
SELECT customer_id, 
       CONCAT(SUBSTRING(email, 1, 2), '*****', SUBSTRING(email, CHARINDEX('@', email), LEN(email))) AS masked_email
FROM customers;
```

#### Data After:
| customer_id | masked_email    |
|-------------|-----------------|
| 101         | "jo*****@abc.com" |
| 102         | "al*****@xyz.com" |

## 21. Calculating Differences
**Purpose:** Compute the difference between values in consecutive rows.

#### Data Before:
| order_id | order_date   | total_amount |
|----------|--------------|--------------|
| 1        | 2024-01-15   | 100.00       |
| 2        | 2024-02-20   | 200.00       |
| 3        | 2024-03-10   | 150.00       |

#### SQL Transformation:
```sql
SELECT order_id, 
       order_date, 
       total_amount, 
       total_amount - LAG(total_amount) OVER (ORDER BY order_date) AS amount_difference
FROM orders;
```

#### Data After:
| order_id | order_date   | total_amount | amount_difference |
|----------|--------------|--------------|-------------------|
| 1        | 2024-01-15   | 100.00       | NULL              |
| 2        | 2024-02-20   | 200.00       | 100.00            |
| 3        | 2024-03-10   | 150.00       | -50.00            |

## 22. Data Binning
**Purpose:** Group continuous data into bins or intervals.

#### Data Before:
| order_id | total_amount |
|----------|--------------|
| 1        | 100.00       |
| 2        | 200.00       |
| 3        | 150.00       |
| 4        | 300.00       |

#### SQL Transformation:
```sql
SELECT order_id, 
       total_amount, 
       CASE 
           WHEN total_amount < 150 THEN 'Low' 
           WHEN total_amount BETWEEN 150 AND 250 THEN 'Medium' 
           ELSE 'High' 
       END AS amount_bin
FROM orders;
```

#### Data After:
| order_id | total_amount | amount_bin |
|----------|--------------|------------|
| 1        | 100.00       | Low        |
| 2        | 200.00       | Medium     |
| 3        | 150.00       | Medium     |
| 4        | 300.00       | High       |

## 23. Handling Outliers
**Purpose:** Identify and handle outliers in the data.

#### Data Before:
| order_id | total_amount |
|----------|--------------|
| 1        | 100.00       |
| 2        | 200.00       |
| 3        | 150.00       |
| 4        | 1000.00      |

#### SQL Transformation:
```sql
SELECT order_id, 
       total_amount, 
       CASE 
           WHEN total_amount > 3 * (SELECT AVG(total_amount) FROM orders) THEN 'Outlier' 
           ELSE 'Normal' 
       END AS amount_status
FROM orders;
```

#### Data After:
| order_id | total_amount | amount_status |
|----------|--------------|---------------|
| 1        | 100.00       | Normal        |
| 2        | 200.00       | Normal        |
| 3        | 150.00       | Normal        |
| 4        | 1000.00      | Outlier       |

## Conclusion
By applying these transformation techniques, ETL pipelines can ensure that raw data is clean, consistent, and ready for analytics. Whether dealing with text normalization, handling missing values, deduplication, or deriving computed fields, these methods help maintain high-quality datasets essential for business insights.

