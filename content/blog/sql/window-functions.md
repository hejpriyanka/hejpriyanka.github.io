---
title: "SQL Window Functions"
date: 2025-02-18
description: "A comprehensive guide to SQL Window Functions, their use cases, and real-world applications."
draft: false
tags: ["SQL", "Window Functions", "Data Engineering", "ETL", "Data Quality"]
cover:

  image: "/images/window_functions.png"

  alt: "Window Functions"
---

## Introduction

SQL Window Functions are powerful tools that allow you to perform calculations across a set of table rows related to the current row without collapsing the result into a single group. Unlike aggregate functions, which summarize data into a single row per group, window functions retain individual rows while applying calculations over a "window" of rows.

This guide covers:
- Different types of window functions
- `PARTITION BY` and `ORDER BY`
- Real-world use cases

## What are Window Functions?

A window function performs a calculation across a subset (window) of rows related to the current row. It uses the `OVER()` clause, which defines how the window is partitioned and ordered.

### Syntax:
```sql
SELECT column_name, 
       window_function() OVER (
           PARTITION BY column_to_partition 
           ORDER BY column_to_order
       ) AS alias
FROM table_name;
```

## Types of Window Functions and When to Use Them

### 1. **ROW_NUMBER()** - Assigns a unique sequential number to each row.
#### **When to Use:**
- When you need a unique rank for each row in an ordered dataset.
- To remove duplicates while keeping the first occurrence.
#### **Example: Get the first purchase of each customer**
```sql
SELECT customer_id, order_id, order_date,
       ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date) AS order_rank
FROM orders;
```
---

### 2. **RANK()** - Assigns a rank to each row, with gaps when there are ties.
#### **When to Use:**
- When you want to rank items but allow tied values to share the same rank.
#### **Example: Rank products by total sales**
```sql
SELECT product_id, SUM(quantity) AS total_sold,
       RANK() OVER (ORDER BY SUM(quantity) DESC) AS sales_rank
FROM sales
GROUP BY product_id;
```
---

### 3. **DENSE_RANK()** - Similar to `RANK()`, but without gaps.
#### **When to Use:**
- When ranking should be sequential even if values are the same.
#### **Example: Rank employees by salary**
```sql
SELECT employee_id, department, salary,
       DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS salary_rank
FROM employees;
```
---

### 4. **NTILE(N)** - Divides rows into N equal buckets.
#### **When to Use:**
- When you need to split data into quartiles, deciles, or percentiles.
#### **Example: Divide students into quartiles based on marks**
```sql
SELECT student_id, marks,
       NTILE(4) OVER (ORDER BY marks DESC) AS quartile
FROM students;
```
---

### 5. **LAG()** - Fetches the previous row’s value.
#### **When to Use:**
- When you need to compare a value with the previous row’s value.
#### **Example: Compare this month's vs. last month's sales**
```sql
SELECT month, total_sales,
       LAG(total_sales, 1) OVER (ORDER BY month) AS previous_month_sales,
       total_sales - LAG(total_sales, 1) OVER (ORDER BY month) AS sales_change
FROM monthly_sales;
```
---

### 6. **LEAD()** - Fetches the next row’s value.
#### **When to Use:**
- When you want to compare a value with the next row’s value.
#### **Example: Find the difference between an employee’s salary and the next highest salary**
```sql
SELECT employee_id, salary,
       LEAD(salary) OVER (ORDER BY salary DESC) AS next_salary,
       LEAD(salary) OVER (ORDER BY salary DESC) - salary AS salary_difference
FROM employees;
```
---

### 7. **SUM() OVER() & AVG() OVER()** - Running totals and moving averages.
#### **When to Use:**
- `SUM() OVER()` for cumulative totals.
- `AVG() OVER()` for moving averages.
#### **Example: Calculate cumulative sales per region**
```sql
SELECT region, month, total_sales,
       SUM(total_sales) OVER (PARTITION BY region ORDER BY month) AS cumulative_sales
FROM regional_sales;
```
---

## Summary: Which Function to Use When?
| Use Case | Function to Use |
|----------|----------------|
| Assigning unique numbers | `ROW_NUMBER()` |
| Ranking with gaps | `RANK()` |
| Ranking without gaps | `DENSE_RANK()` |
| Dividing data into N groups | `NTILE(N)` |
| Comparing previous values | `LAG()` |
| Comparing next values | `LEAD()` |
| Cumulative total | `SUM() OVER()` |
| Moving average | `AVG() OVER()` |



*Image credit: [Vedant Mehta on LinkedIn](https://www.linkedin.com/pulse/window-functions-sql-vedant-mehta/)*

By using SQL Window Functions, you can efficiently analyze trends, rankings, and comparisons in datasets. Start experimenting with these functions to improve your SQL queries! 

