# Pandas for Data Analytics — Day 4
## Merge, Join & Concatenate — Google Colab Lab

> **Prerequisite:** Pandas Day 1 — Basics, Day 2 — Data Cleaning, Day 3 — Data Transformation & Business Analysis.

---

# 1. Where We Are in the Pandas Roadmap

```text
DAY 1
Pandas Basics
    ↓
DAY 2
Data Cleaning
    ↓
DAY 3
Data Transformation + Business Analysis
    ↓
DAY 4
Merge / Join / Concatenate
    ↓
DAY 5
Advanced GroupBy + Pivot
    ↓
DAY 6
EDA + Visualization
    ↓
DAY 7
Business Dashboard / Reporting
    ↓
FINAL PROJECT
```

Day 3 used one main sales table.

Today we move toward a **relational-data mindset**:

```text
Customers
    +
Orders
    +
Products
    ↓
Merged Business Dataset
    ↓
Business Analysis
```

---

# 2. Today's Learning Objectives

By the end of Day 4, students will be able to:

- Understand why businesses store data in multiple tables
- Identify primary keys and matching columns
- Load multiple CSV files
- Use `pd.merge()`
- Perform Inner Join
- Perform Left Join
- Perform Right Join
- Perform Outer Join
- Merge using different key names
- Merge using multiple columns
- Use `pd.concat()`
- Perform vertical concatenation
- Perform horizontal concatenation
- Validate row counts after merging
- Find unmatched records
- Build a complete customer + order + product dataset
- Answer business questions using merged data

---

# 3. Today's Dataset

You will use five CSV files:

```text
day4_customers.csv
day4_products.csv
day4_orders.csv
day4_orders_q4.csv
day4_product_updates.csv
```

Main tables:

```text
CUSTOMERS
100 customers

PRODUCTS
20 products

ORDERS
500 orders
```

Additional files are included specifically for `concat()` and merge practice.

---

# 4. Understand the Tables

## Customers

| Column | Meaning |
|---|---|
| Customer_ID | Unique customer identifier |
| Customer_Name | Customer name |
| City | Customer city |
| Customer_Segment | Customer type |
| Customer_Rating | Customer rating |
| Region | Sales region |

## Products

| Column | Meaning |
|---|---|
| Product_ID | Unique product identifier |
| Product | Product name |
| Category | Product category |
| Unit_Price | Product price |

## Orders

| Column | Meaning |
|---|---|
| Order_ID | Unique order identifier |
| Order_Date | Date of order |
| Customer_ID | Customer reference |
| Product_ID | Product reference |
| Quantity | Units purchased |
| Discount | Discount percentage |
| Salesperson | Employee handling sale |
| Payment_Mode | Payment method |

---

# 5. Why Do We Need Merge?

Imagine an order table:

```text
Order_ID | Customer_ID | Product_ID | Quantity
O0001    | C025        | P004       | 3
```

The order table does not contain:

```text
Customer Name
City
Region
Product Name
Category
Unit Price
```

Those details are stored in other tables.

So we need:

```text
Orders
   |
   +---- Customer_ID ----> Customers
   |
   +---- Product_ID -----> Products
```

This is the basic idea behind `merge()`.

---

# 6. Open Google Colab

Open:

```text
https://colab.research.google.com/
```

Create a new notebook.

Suggested name:

```text
Pandas_Day_4_Merge_Join_Concatenate
```

Upload all five CSV files.

---

# 7. Import Libraries

```python
import pandas as pd
import numpy as np
```

---

# 8. Load All Tables

```python
customers = pd.read_csv("day4_customers.csv")
products = pd.read_csv("day4_products.csv")
orders = pd.read_csv("day4_orders.csv")
orders_q4 = pd.read_csv("day4_orders_q4.csv")
product_updates = pd.read_csv("day4_product_updates.csv")
```

---

# 9. Inspect the Tables

```python
customers.head()
```

```python
products.head()
```

```python
orders.head()
```

Check shape:

```python
print("Customers:", customers.shape)
print("Products:", products.shape)
print("Orders:", orders.shape)
```

Expected structure:

```text
Customers → 100 rows
Products  → 20 rows
Orders    → 500 rows
```

---

# 10. Understand the Join Key

A join key is a column used to connect tables.

Customers:

```text
Customer_ID
```

Orders:

```text
Customer_ID
```

Products:

```text
Product_ID
```

Therefore:

```text
orders.Customer_ID → customers.Customer_ID

orders.Product_ID → products.Product_ID
```

---

# 11. What is `pd.merge()`?

General syntax:

```python
pd.merge(
    left,
    right,
    on="column_name",
    how="join_type"
)
```

Example:

```python
customer_orders = pd.merge(
    orders,
    customers,
    on="Customer_ID",
    how="inner"
)
```

---

# 12. Inner Join

Inner join returns only matching records.

```python
customer_orders = pd.merge(
    orders,
    customers,
    on="Customer_ID",
    how="inner"
)
```

Check:

```python
customer_orders.head()
```

Shape:

```python
customer_orders.shape
```

Concept:

```text
Orders          Customers
   |                |
   |---- MATCH ----|
          ↓
   Matching rows only
```

Business meaning:

> Show orders for customers that exist in the customer master table.

---

# 13. Left Join

A left join keeps **all rows from the left table**.

```python
customer_orders_left = pd.merge(
    orders,
    customers,
    on="Customer_ID",
    how="left"
)
```

Think:

```text
LEFT TABLE
    ↓
Keep everything
    +
Matching data from right
```

Business example:

> Keep every order and attach customer information whenever available.

---

# 14. Right Join

A right join keeps all rows from the right table.

```python
customer_orders_right = pd.merge(
    orders,
    customers,
    on="Customer_ID",
    how="right"
)
```

Business example:

> Keep every customer and attach their order information when available.

---

# 15. Outer Join

Outer join keeps everything from both tables.

```python
customer_orders_outer = pd.merge(
    orders,
    customers,
    on="Customer_ID",
    how="outer"
)
```

Concept:

```text
All Orders
    +
All Customers
    +
Matches
    ↓
Complete combined table
```

Unmatched values appear as:

```text
NaN
```

---

# 16. Compare Join Types

| Join | Keeps |
|---|---|
| `inner` | Matching records |
| `left` | All left + matching right |
| `right` | All right + matching left |
| `outer` | Everything from both |

Easy memory trick:

```text
INNER → MATCH
LEFT  → LEFT EVERYTHING
RIGHT → RIGHT EVERYTHING
OUTER → EVERYTHING
```

---

# 17. Find Unmatched Records

Use an outer merge with an indicator.

```python
check = pd.merge(
    orders,
    customers,
    on="Customer_ID",
    how="outer",
    indicator=True
)
```

Now:

```python
check["_merge"].value_counts()
```

Possible values:

```text
both
left_only
right_only
```

Find unmatched orders:

```python
check[check["_merge"] == "left_only"]
```

Find customers with no matching order:

```python
check[check["_merge"] == "right_only"]
```

---

# 18. Merge Orders with Products

```python
order_products = pd.merge(
    orders,
    products,
    on="Product_ID",
    how="left"
)
```

Check:

```python
order_products.head()
```

Now the order table contains:

```text
Order_ID
Product_ID
Product
Category
Unit_Price
Quantity
Discount
Salesperson
Payment_Mode
```

---

# 19. Build the Complete Business Dataset

First merge Orders + Customers:

```python
sales_analysis = pd.merge(
    orders,
    customers,
    on="Customer_ID",
    how="left"
)
```

Then merge Products:

```python
sales_analysis = pd.merge(
    sales_analysis,
    products,
    on="Product_ID",
    how="left"
)
```

Check:

```python
sales_analysis.head()
```

Check columns:

```python
sales_analysis.columns
```

Now we have:

```text
ORDERS
   ↓
CUSTOMERS
   ↓
PRODUCTS
   ↓
COMPLETE SALES DATASET
```

---

# 20. Create Sales

```python
sales_analysis["Gross_Sales"] = (
    sales_analysis["Quantity"] *
    sales_analysis["Unit_Price"]
)
```

Discount amount:

```python
sales_analysis["Discount_Amount"] = (
    sales_analysis["Gross_Sales"] *
    sales_analysis["Discount"]
)
```

Net sales:

```python
sales_analysis["Net_Sales"] = (
    sales_analysis["Gross_Sales"] -
    sales_analysis["Discount_Amount"]
)
```

---

# 21. Calculate Profit

For teaching purposes, assume cost is 75% of net sales.

```python
sales_analysis["Cost"] = (
    sales_analysis["Net_Sales"] * 0.75
)
```

Profit:

```python
sales_analysis["Profit"] = (
    sales_analysis["Net_Sales"] -
    sales_analysis["Cost"]
)
```

Profit margin:

```python
sales_analysis["Profit_Margin"] = (
    sales_analysis["Profit"] /
    sales_analysis["Net_Sales"] * 100
).round(2)
```

---

# 22. Business Analysis After Merge

Total sales by category:

```python
sales_analysis.groupby(
    "Category"
)["Net_Sales"].sum().sort_values(
    ascending=False
)
```

Profit by region:

```python
sales_analysis.groupby(
    "Region"
)["Profit"].sum().sort_values(
    ascending=False
)
```

Sales by salesperson:

```python
sales_analysis.groupby(
    "Salesperson"
)["Net_Sales"].sum().sort_values(
    ascending=False
)
```

Sales by customer segment:

```python
sales_analysis.groupby(
    "Customer_Segment"
)["Net_Sales"].sum().sort_values(
    ascending=False
)
```

---

# 23. Merge Using Different Column Names

Sometimes two tables have different key names.

Example:

```text
Product_ID
```

and:

```text
Product_Code
```

Use:

```python
pd.merge(
    left_table,
    right_table,
    left_on="Product_ID",
    right_on="Product_Code"
)
```

General pattern:

```python
pd.merge(
    df1,
    df2,
    left_on="column_from_df1",
    right_on="column_from_df2"
)
```

---

# 24. Merge Using Multiple Columns

Sometimes one column is not enough.

Example:

```python
pd.merge(
    df1,
    df2,
    on=["City", "Region"],
    how="inner"
)
```

Multiple keys:

```text
City + Region
```

Both values must match.

---

# 25. `suffixes`

Sometimes both tables have columns with the same name.

Example:

```text
Unit_Price
```

appears in both tables.

Use:

```python
pd.merge(
    products,
    product_updates,
    on="Product_ID",
    how="left",
    suffixes=("_old", "_update")
)
```

This prevents confusing column names.

---

# 26. Product Price Update Example

Load:

```python
product_updates.head()
```

Merge:

```python
price_check = pd.merge(
    products,
    product_updates,
    on="Product_ID",
    how="left"
)
```

Display:

```python
price_check
```

Compare:

```text
Old Unit Price
Updated Price
```

---

# 27. What is `pd.concat()`?

`concat()` combines DataFrames.

Two major patterns:

```text
VERTICAL
rows added
```

and:

```text
HORIZONTAL
columns added
```

---

# 28. Vertical Concatenation

Suppose we have two order batches:

```text
orders
orders_q4
```

Combine rows:

```python
all_orders = pd.concat(
    [orders, orders_q4],
    ignore_index=True
)
```

Check:

```python
all_orders.shape
```

Concept:

```text
ORDERS JAN–SEP
       ↓
ORDERS Q4
       ↓
    CONCAT
       ↓
ALL ORDERS
```

---

# 29. Why `ignore_index=True`?

Without it, the original indexes are retained.

```python
pd.concat(
    [orders, orders_q4]
)
```

With:

```python
pd.concat(
    [orders, orders_q4],
    ignore_index=True
)
```

the result gets a clean:

```text
0
1
2
3
...
```

index.

---

# 30. Horizontal Concatenation

Create two small DataFrames:

```python
left = customers[[
    "Customer_ID",
    "Customer_Name"
]].head(5).reset_index(drop=True)

right = customers[[
    "City",
    "Region"
]].head(5).reset_index(drop=True)
```

Concatenate columns:

```python
horizontal = pd.concat(
    [left, right],
    axis=1
)
```

Check:

```python
horizontal
```

Remember:

```text
axis=0 → rows
axis=1 → columns
```

---

# 31. `merge()` vs `concat()`

Very important distinction.

## `merge()`

Used when tables have a relationship.

```python
pd.merge(
    orders,
    customers,
    on="Customer_ID"
)
```

Think:

```text
JOIN USING KEY
```

## `concat()`

Used to stack or place DataFrames together.

```python
pd.concat(
    [orders, orders_q4],
    ignore_index=True
)
```

Think:

```text
COMBINE DATAFRAMES
```

---

# 32. Common Mistake — Row Explosion

Before merging, check key uniqueness.

For customers:

```python
customers["Customer_ID"].is_unique
```

For products:

```python
products["Product_ID"].is_unique
```

For orders:

```python
orders["Order_ID"].is_unique
```

A one-to-many relationship is normal:

```text
1 Customer
   ↓
Many Orders
```

But unexpected duplicates in a master table can create too many rows after a merge.

---

# 33. Validate Row Counts

Before:

```python
print("Orders:", len(orders))
```

After:

```python
print("Merged:", len(sales_analysis))
```

For a normal many-to-one order → customer merge, the number of rows should generally remain equal to the orders table when every order has one customer match.

---

# 34. Use `validate`

Pandas can check expected relationships.

Orders to customers:

```python
sales_check = pd.merge(
    orders,
    customers,
    on="Customer_ID",
    how="left",
    validate="many_to_one"
)
```

Meaning:

```text
Many orders
      ↓
One customer
```

Products:

```python
sales_check = pd.merge(
    orders,
    products,
    on="Product_ID",
    how="left",
    validate="many_to_one"
)
```

This is a useful professional habit.

---

# 35. Business Question 1

### Which product category generated the highest sales?

```python
sales_analysis.groupby(
    "Category"
)["Net_Sales"].sum().sort_values(
    ascending=False
)
```

---

# 36. Business Question 2

### Which region generated the highest profit?

```python
sales_analysis.groupby(
    "Region"
)["Profit"].sum().sort_values(
    ascending=False
)
```

---

# 37. Business Question 3

### Which customer segment generated the highest revenue?

```python
sales_analysis.groupby(
    "Customer_Segment"
)["Net_Sales"].sum().sort_values(
    ascending=False
)
```

---

# 38. Business Question 4

### Which salesperson generated the highest sales?

```python
sales_analysis.groupby(
    "Salesperson"
)["Net_Sales"].sum().sort_values(
    ascending=False
)
```

---

# 39. Business Question 5

### Which city has the highest sales?

```python
sales_analysis.groupby(
    "City"
)["Net_Sales"].sum().sort_values(
    ascending=False
)
```

---

# 40. Business Question 6

### Which product sold the most units?

```python
sales_analysis.groupby(
    "Product"
)["Quantity"].sum().sort_values(
    ascending=False
)
```

---

# 41. Business Question 7

### Find the top 10 customers by sales.

```python
customer_sales = sales_analysis.groupby(
    ["Customer_ID", "Customer_Name"]
)["Net_Sales"].sum().sort_values(
    ascending=False
)

customer_sales.head(10)
```

---

# 42. Business Question 8

### Find customers who have never placed an order.

First:

```python
customer_order_check = pd.merge(
    customers,
    orders[["Order_ID", "Customer_ID"]],
    on="Customer_ID",
    how="left",
    indicator=True
)
```

Then:

```python
customer_order_check[
    customer_order_check["_merge"] == "left_only"
]
```

This is an important real-world business use of a left join.

Business interpretation:

> These customers exist in the customer master but have no recorded order.

---

# 43. Business Question 9

### Find products that have never appeared in an order.

```python
product_order_check = pd.merge(
    products,
    orders[["Order_ID", "Product_ID"]],
    on="Product_ID",
    how="left",
    indicator=True
)
```

Then:

```python
product_order_check[
    product_order_check["_merge"] == "left_only"
]
```

Business interpretation:

> These products are available in the product master but have no recorded sales.

---

# 44. Day 4 Assignment

Students should solve these without copying the previous answers.

## Task 1

Load all five datasets.

## Task 2

Display shape and columns for every table.

## Task 3

Perform an inner join between Orders and Customers.

## Task 4

Perform a left join between Orders and Products.

## Task 5

Perform an outer join between Customers and Orders.

## Task 6

Create a complete sales dataset:

```text
Orders
+
Customers
+
Products
```

## Task 7

Create:

```text
Gross_Sales
Discount_Amount
Net_Sales
Cost
Profit
Profit_Margin
```

## Task 8

Use `groupby()` to find:

```text
Sales by Category
Profit by Region
Sales by Salesperson
Sales by Customer Segment
```

## Task 9

Use `pd.concat()` to combine:

```text
orders
orders_q4
```

## Task 10

Find customers with no orders.

## Task 11

Find products with no orders.

## Task 12

Use `validate="many_to_one"` in an order-to-customer merge.

---

# 45. Mini Project

## Project Title

**Retail Sales Data Integration using Pandas**

### Business Problem

A retail company stores information in separate systems:

```text
Customer System
       +
Order System
       +
Product System
```

Management wants one analysis-ready dataset.

Students must integrate all three tables and answer:

1. Which category has the highest sales?
2. Which product has the highest units sold?
3. Which region has the highest profit?
4. Which salesperson performs best?
5. Which customer segment generates the most revenue?
6. Which city generates the most sales?
7. Which customers have no orders?
8. Which products have no orders?

---

# 46. Recommended Project Workflow

```text
Load Customers
      ↓
Load Products
      ↓
Load Orders
      ↓
Inspect Keys
      ↓
Merge Orders + Customers
      ↓
Merge Products
      ↓
Create Sales Metrics
      ↓
Validate Data
      ↓
GroupBy Analysis
      ↓
Business Questions
      ↓
Export Final Dataset
```

---

# 47. Export Final Dataset

```python
sales_analysis.to_csv(
    "day4_sales_analysis_merged.csv",
    index=False
)
```

Export combined orders:

```python
all_orders.to_csv(
    "day4_all_orders.csv",
    index=False
)
```

---

# 48. Important Commands to Remember

```python
pd.merge()

how="inner"
how="left"
how="right"
how="outer"

left_on=
right_on=

on=["col1", "col2"]

suffixes=()

indicator=True

validate="many_to_one"

pd.concat()

axis=0
axis=1

ignore_index=True
```

---

# 49. SQL → Pandas Connection

| SQL | Pandas |
|---|---|
| INNER JOIN | `pd.merge(..., how="inner")` |
| LEFT JOIN | `pd.merge(..., how="left")` |
| RIGHT JOIN | `pd.merge(..., how="right")` |
| FULL OUTER JOIN | `pd.merge(..., how="outer")` |
| UNION ALL | `pd.concat()` |
| JOIN ON multiple columns | `on=["col1", "col2"]` |
| CASE / conditional analysis | `np.where()` / `np.select()` |

---

# 50. Day 4 Learning Flow

```text
Multiple Tables
      ↓
Primary / Matching Keys
      ↓
pd.merge()
      ↓
INNER JOIN
      ↓
LEFT JOIN
      ↓
RIGHT JOIN
      ↓
OUTER JOIN
      ↓
Multiple-Column Merge
      ↓
indicator=True
      ↓
validate=
      ↓
pd.concat()
      ↓
Vertical Concatenation
      ↓
Horizontal Concatenation
      ↓
Customer + Order + Product
      ↓
Business Analysis
      ↓
Mini Project
      ↓
Export
```

---

# 51. Instructor Teaching Pattern

Do not teach joins only as syntax.

Use:

```text
Business Question
      ↓
Which tables contain the answer?
      ↓
What is the matching key?
      ↓
Which join is required?
      ↓
Write merge()
      ↓
Check row count
      ↓
Check unmatched records
      ↓
Perform analysis
      ↓
Explain business meaning
```

Example:

```text
Question:
Which customers have never purchased?

        ↓

Tables:
Customers + Orders

        ↓

Key:
Customer_ID

        ↓

Join:
LEFT JOIN

        ↓

Filter:
_merge == "left_only"

        ↓

Business Meaning:
Potential inactive customers
```

This helps students move from:

```text
Pandas Syntax
      ↓
Data Integration
      ↓
Business Analysis
```

---

# 52. Day 5 Preview

After Day 4, move to:

## DAY 5 — Advanced GroupBy + Pivot

Topics:

1. Advanced `groupby()`
2. Multiple aggregations
3. Named aggregation
4. MultiIndex results
5. `reset_index()`
6. `pivot_table()`
7. Multiple metrics
8. Cross-tab analysis
9. Ranking within groups
10. Business KPI tables

Then:

```text
DAY 6 → EDA + Matplotlib
DAY 7 → Dashboard / Reporting
FINAL → Complete Data Analytics Project
```

---

# 53. Final Takeaway

Day 4 is about understanding that real company data rarely exists in one table.

A Data Analyst should be comfortable with:

```text
Customer Table
      +
Order Table
      +
Product Table
      ↓
MERGE
      ↓
Analysis-Ready Dataset
      ↓
Business Insights
```

The most important concepts today are:

```python
pd.merge()
pd.concat()
how="inner"
how="left"
how="right"
how="outer"
indicator=True
validate="many_to_one"
```

The goal is not just to combine tables.

The goal is:

> **Connect the right data, validate the connection, and then answer the business question.**
