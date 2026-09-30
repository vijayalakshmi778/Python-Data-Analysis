# Pandas for Data Analytics — Day 3
## Data Transformation & Business Analysis — Google Colab Lab

> **Prerequisite:** Pandas Day 1 — Basics + Day 2 — Data Cleaning.

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
PROJECT
Real-world Data Analysis
```

Day 2 covered missing values, duplicates, text/date cleaning, type conversion and calculated columns.

Today we move from **cleaning data** to **transforming data and answering business questions**.

---

# 2. Today's Learning Objectives

By the end of this session, students will be able to:

- Load a business sales dataset
- Create calculated columns
- Use `apply()`
- Use `lambda`
- Create conditional columns
- Use `np.where()`
- Use `map()`
- Use `replace()`
- Create business categories with `pd.cut()`
- Rank records using `rank()`
- Sort transformed data
- Perform advanced `groupby()`
- Use `agg()`
- Create summary tables
- Use `pivot_table()`
- Answer practical business questions
- Export an analysis-ready dataset

---

# 3. Today's Dataset

Dataset:

```text
pandas_day3_business_sales.csv
```

The dataset contains 300 sales transactions.

## Columns

| Column | Meaning |
|---|---|
| Order_ID | Unique order ID |
| Order_Date | Date of order |
| Customer_Name | Customer identifier |
| City | Customer city |
| Region | Sales region |
| Product | Product purchased |
| Category | Product category |
| Quantity | Units purchased |
| Unit_Price | Price per unit |
| Discount | Discount percentage |
| Sales | Net sales after discount |
| Cost | Product/order cost |
| Profit | Profit from order |
| Salesperson | Employee handling the sale |
| Payment_Mode | Payment method |
| Customer_Segment | Consumer / Small Business / Enterprise |
| Customer_Rating | Customer rating |

---

# 4. Open Google Colab

Open:

```text
https://colab.research.google.com/
```

Create a new notebook.

Suggested name:

```text
Pandas_Day_3_Data_Transformation
```

Upload:

```text
pandas_day3_business_sales.csv
```

---

# 5. Import Libraries

```python
import pandas as pd
import numpy as np
```

---

# 6. Load Dataset

```python
df = pd.read_csv("pandas_day3_business_sales.csv")
```

Display:

```python
df.head()
```

---

# 7. First Inspection

```python
df.shape
```

```python
df.info()
```

```python
df.describe()
```

```python
df.columns
```

Check missing values:

```python
df.isnull().sum()
```

Check duplicates:

```python
df.duplicated().sum()
```

---

# 8. Business Question

Tell students:

> "A Data Analyst does not only clean data. The analyst transforms data into information that helps a business make decisions."

Today's project will answer questions such as:

- Which products generate the most sales?
- Which salesperson generates the most profit?
- Which customer segment has the highest sales?
- Which orders are high-value?
- Which products need attention?
- Which cities generate the most revenue?
- What is the average order value?

---

# 9. Create a Gross Sales Column

Although `Sales` already represents net sales, calculate the original transaction value.

```python
df["Gross_Sales"] = df["Quantity"] * df["Unit_Price"]
```

Check:

```python
df[["Product", "Quantity", "Unit_Price", "Gross_Sales"]].head()
```

---

# 10. Calculate Discount Amount

Create:

```python
df["Discount_Amount"] = df["Gross_Sales"] * df["Discount"]
```

Check:

```python
df[
    [
        "Gross_Sales",
        "Discount",
        "Discount_Amount",
        "Sales"
    ]
].head()
```

---

# 11. Calculate Profit Margin

Profit Margin:

```text
Profit / Sales × 100
```

Python:

```python
df["Profit_Margin"] = (
    df["Profit"] / df["Sales"] * 100
).round(2)
```

Check:

```python
df[["Sales", "Profit", "Profit_Margin"]].head()
```

---

# 12. What is `lambda`?

A lambda function is a small anonymous function.

Example:

```python
square = lambda x: x * x
```

Test:

```python
square(5)
```

Output:

```text
25
```

General structure:

```python
lambda value: calculation
```

---

# 13. Using Lambda with a Column

Create a profit category.

```python
df["Profit_Type"] = df["Profit"].apply(
    lambda x: "High Profit" if x >= 10000 else "Normal Profit"
)
```

Check:

```python
df[["Profit", "Profit_Type"]].head(10)
```

---

# 14. Understanding `apply()`

`apply()` applies a function to each value.

Example:

```python
df["Sales"] = df["Sales"].apply(
    lambda x: round(x, 2)
)
```

Another example:

```python
df["Product_Label"] = df["Product"].apply(
    lambda x: x.upper()
)
```

---

# 15. Conditional Columns with `np.where()`

Suppose the business wants to identify high-value orders.

```python
df["Order_Type"] = np.where(
    df["Sales"] >= 30000,
    "High Value",
    "Regular"
)
```

Check:

```python
df["Order_Type"].value_counts()
```

---

# 16. Multiple Conditions with `np.select()`

Create a sales category:

```python
conditions = [
    df["Sales"] < 10000,
    (df["Sales"] >= 10000) & (df["Sales"] < 30000),
    df["Sales"] >= 30000
]

choices = [
    "Low Sales",
    "Medium Sales",
    "High Sales"
]

df["Sales_Category"] = np.select(
    conditions,
    choices,
    default="Unknown"
)
```

Check:

```python
df["Sales_Category"].value_counts()
```

---

# 17. Using `map()`

Map region names to manager names.

```python
region_manager = {
    "South": "Meena",
    "West": "Ravi",
    "North": "Amit"
}
```

Create:

```python
df["Regional_Manager"] = df["Region"].map(
    region_manager
)
```

Check:

```python
df[["Region", "Regional_Manager"]].drop_duplicates()
```

---

# 18. `replace()` vs `map()`

### `replace()`

Use when replacing existing values.

```python
df["Payment_Mode"] = df["Payment_Mode"].replace({
    "UPI": "Digital",
    "Credit Card": "Card",
    "Debit Card": "Card"
})
```

### `map()`

Use when creating a mapping from one value to another.

```python
segment_code = {
    "Consumer": "C",
    "Small Business": "SMB",
    "Enterprise": "ENT"
}

df["Segment_Code"] = df["Customer_Segment"].map(
    segment_code
)
```

---

# 19. Binning with `pd.cut()`

Binning converts numeric values into categories.

Example:

```text
0–10000       → Low
10000–30000   → Medium
30000+        → High
```

Use:

```python
df["Sales_Band"] = pd.cut(
    df["Sales"],
    bins=[0, 10000, 30000, float("inf")],
    labels=["Low", "Medium", "High"]
)
```

Check:

```python
df["Sales_Band"].value_counts()
```

---

# 20. Create Quantity Bands

```python
df["Quantity_Band"] = pd.cut(
    df["Quantity"],
    bins=[0, 2, 5, float("inf")],
    labels=["Small Order", "Medium Order", "Large Order"]
)
```

Check:

```python
df["Quantity_Band"].value_counts()
```

---

# 21. Ranking

Find the highest-profit orders.

```python
df["Profit_Rank"] = df["Profit"].rank(
    ascending=False,
    method="dense"
)
```

Display top orders:

```python
df.sort_values("Profit_Rank").head(10)
```

---

# 22. Rank Sales

```python
df["Sales_Rank"] = df["Sales"].rank(
    ascending=False,
    method="dense"
)
```

Top 10 sales:

```python
df.sort_values("Sales_Rank").head(10)
```

---

# 23. Sort Multiple Columns

Sort by Region and Profit:

```python
df.sort_values(
    ["Region", "Profit"],
    ascending=[True, False]
)
```

Meaning:

```text
Region → A to Z
Profit → Highest to Lowest
```

---

# 24. Basic GroupBy

Total sales by category:

```python
df.groupby("Category")["Sales"].sum()
```

Total profit by category:

```python
df.groupby("Category")["Profit"].sum()
```

---

# 25. Advanced `groupby()` + `agg()`

Business wants:

- Number of orders
- Total sales
- Average sales
- Total profit
- Average rating

Use:

```python
category_summary = df.groupby("Category").agg(
    Orders=("Order_ID", "count"),
    Total_Sales=("Sales", "sum"),
    Average_Sales=("Sales", "mean"),
    Total_Profit=("Profit", "sum"),
    Average_Rating=("Customer_Rating", "mean")
)
```

Display:

```python
category_summary
```

---

# 26. Round the Summary

```python
category_summary = category_summary.round(2)
```

---

# 27. Salesperson Performance

Create:

```python
salesperson_summary = df.groupby("Salesperson").agg(
    Orders=("Order_ID", "count"),
    Total_Sales=("Sales", "sum"),
    Total_Profit=("Profit", "sum"),
    Average_Rating=("Customer_Rating", "mean")
).round(2)
```

Display:

```python
salesperson_summary
```

---

# 28. Sort Salesperson Performance

Sort by total sales:

```python
salesperson_summary.sort_values(
    "Total_Sales",
    ascending=False
)
```

Do not just look at the first row.

Ask students:

> Which metrics should management examine together?

Possible metrics:

```text
Orders
Total Sales
Total Profit
Average Rating
```

---

# 29. City Performance

```python
city_summary = df.groupby("City").agg(
    Orders=("Order_ID", "count"),
    Total_Sales=("Sales", "sum"),
    Total_Profit=("Profit", "sum"),
    Average_Rating=("Customer_Rating", "mean")
).round(2)
```

Display:

```python
city_summary
```

---

# 30. Customer Segment Analysis

```python
segment_summary = df.groupby("Customer_Segment").agg(
    Orders=("Order_ID", "count"),
    Total_Sales=("Sales", "sum"),
    Total_Profit=("Profit", "sum"),
    Average_Rating=("Customer_Rating", "mean")
).round(2)
```

---

# 31. Product Analysis

```python
product_summary = df.groupby("Product").agg(
    Orders=("Order_ID", "count"),
    Units_Sold=("Quantity", "sum"),
    Total_Sales=("Sales", "sum"),
    Total_Profit=("Profit", "sum"),
    Average_Rating=("Customer_Rating", "mean")
).round(2)
```

---

# 32. Find Top Products

```python
product_summary.sort_values(
    "Total_Sales",
    ascending=False
)
```

---

# 33. Find Products by Profit

```python
product_summary.sort_values(
    "Total_Profit",
    ascending=False
)
```

---

# 34. Pivot Table

Pivot tables are very useful for business reporting.

Sales by Category and Region:

```python
pivot_sales = pd.pivot_table(
    df,
    values="Sales",
    index="Category",
    columns="Region",
    aggfunc="sum",
    fill_value=0
)
```

Display:

```python
pivot_sales
```

---

# 35. Pivot Table — Profit

```python
pivot_profit = pd.pivot_table(
    df,
    values="Profit",
    index="Category",
    columns="Region",
    aggfunc="sum",
    fill_value=0
)
```

---

# 36. Pivot Table — Multiple Metrics

```python
pivot_summary = pd.pivot_table(
    df,
    values=["Sales", "Profit"],
    index="Category",
    columns="Region",
    aggfunc="sum",
    fill_value=0
)
```

---

# 37. Monthly Sales Analysis

Convert Order_Date:

```python
df["Order_Date"] = pd.to_datetime(
    df["Order_Date"]
)
```

Create month:

```python
df["Month"] = df["Order_Date"].dt.month_name()
```

Create month number:

```python
df["Month_Number"] = df["Order_Date"].dt.month
```

---

# 38. Monthly Sales

```python
monthly_sales = df.groupby(
    ["Month_Number", "Month"]
)["Sales"].sum().reset_index()
```

Sort chronologically:

```python
monthly_sales = monthly_sales.sort_values(
    "Month_Number"
)
```

Display:

```python
monthly_sales
```

---

# 39. Monthly Profit

```python
monthly_profit = df.groupby(
    ["Month_Number", "Month"]
)["Profit"].sum().reset_index()

monthly_profit = monthly_profit.sort_values(
    "Month_Number"
)

monthly_profit
```

---

# 40. Average Order Value

Average Order Value:

```text
Total Sales / Number of Orders
```

Calculate:

```python
aov = df["Sales"].sum() / df["Order_ID"].nunique()

print("Average Order Value:", round(aov, 2))
```

---

# 41. Overall Business KPIs

```python
total_orders = df["Order_ID"].nunique()
total_sales = df["Sales"].sum()
total_profit = df["Profit"].sum()
average_rating = df["Customer_Rating"].mean()
total_units = df["Quantity"].sum()
```

Print:

```python
print("Total Orders:", total_orders)
print("Total Sales:", round(total_sales, 2))
print("Total Profit:", round(total_profit, 2))
print("Total Units Sold:", total_units)
print("Average Rating:", round(average_rating, 2))
print("Average Order Value:", round(aov, 2))
```

---

# 42. Business Analysis Challenge

Students should solve these without looking at the answers.

## Task 1

Find total sales by:

```text
Category
```

## Task 2

Find total profit by:

```text
Region
```

## Task 3

Find average customer rating by:

```text
City
```

## Task 4

Find the number of orders handled by each:

```text
Salesperson
```

## Task 5

Find the top 10 orders by:

```text
Profit
```

## Task 6

Create:

```text
High Value
Regular
```

using `np.where()`.

Condition:

```text
Sales >= 30000
```

## Task 7

Create:

```text
Low
Medium
High
```

using `pd.cut()`.

## Task 8

Create a product summary containing:

```text
Orders
Units Sold
Total Sales
Total Profit
Average Rating
```

## Task 9

Create a pivot table:

```text
Category × Region
```

using total sales.

## Task 10

Find monthly total sales.

---

# 43. Mini Business Project

## Project Title

**Retail Sales Performance Analysis using Pandas**

### Business Problem

A retail company has collected 300 sales transactions.

Management wants to understand:

1. Sales performance
2. Profit performance
3. Product performance
4. Regional performance
5. Salesperson performance
6. Customer segment performance
7. Monthly performance

---

# 44. Project Workflow

```text
Load Dataset
     ↓
Inspect Dataset
     ↓
Clean / Validate Data
     ↓
Create Calculated Columns
     ↓
Create Business Categories
     ↓
GroupBy Analysis
     ↓
Pivot Tables
     ↓
Business KPIs
     ↓
Find Patterns
     ↓
Export Summary Tables
```

---

# 45. Required Project Columns

Students should create at least:

```text
Gross_Sales
Discount_Amount
Profit_Margin
Profit_Type
Order_Type
Sales_Category
Sales_Band
Quantity_Band
Regional_Manager
Segment_Code
Profit_Rank
Sales_Rank
Month
Month_Number
```

---

# 46. Final Management Summary

Students must produce a final summary containing:

| KPI | Value |
|---|---:|
| Total Orders | |
| Total Sales | |
| Total Profit | |
| Total Units Sold | |
| Average Order Value | |
| Average Customer Rating | |

Then create summary tables for:

```text
1. Category
2. Product
3. Region
4. City
5. Salesperson
6. Customer Segment
7. Month
```

---

# 47. Export Project Results

Export the transformed dataset:

```python
df.to_csv(
    "pandas_day3_sales_transformed.csv",
    index=False
)
```

Export category summary:

```python
category_summary.to_csv(
    "category_summary.csv"
)
```

Export salesperson summary:

```python
salesperson_summary.to_csv(
    "salesperson_summary.csv"
)
```

Export product summary:

```python
product_summary.to_csv(
    "product_summary.csv"
)
```

---

# 48. Important Commands to Remember

```python
df.apply()
df.map()

lambda x: ...

np.where()
np.select()

pd.cut()

df.rank()

df.sort_values()

df.groupby()

df.agg()

pd.pivot_table()

df.to_csv()
```

---

# 49. SQL → Pandas Connection

| Business Operation | Pandas |
|---|---|
| Calculated column | `df["New"] = ...` |
| Conditional logic | `np.where()` |
| CASE WHEN | `np.select()` |
| Mapping values | `map()` |
| Group By | `groupby()` |
| Multiple aggregations | `agg()` |
| Pivot report | `pivot_table()` |
| Ranking | `rank()` |
| Sort | `sort_values()` |

---

# 50. Day 3 Learning Flow

```text
Clean Dataset
     ↓
Calculated Columns
     ↓
apply()
     ↓
lambda
     ↓
Conditional Columns
     ↓
np.where()
     ↓
np.select()
     ↓
map()
     ↓
pd.cut()
     ↓
Ranking
     ↓
Advanced GroupBy
     ↓
agg()
     ↓
Pivot Table
     ↓
Business KPIs
     ↓
Business Questions
     ↓
Mini Project
     ↓
Export Results
```

---

# 51. What Comes Next?

After Day 3, teach:

## DAY 4 — Merge, Join & Concatenate

Topics:

1. `pd.merge()`
2. Inner Join
3. Left Join
4. Right Join
5. Outer Join
6. Multiple-column joins
7. `pd.concat()`
8. Vertical concatenation
9. Horizontal concatenation
10. Combining customer + order + product datasets

Then move to:

```text
DAY 5
Advanced GroupBy + Pivot
        ↓
DAY 6
EDA + Matplotlib
        ↓
DAY 7
Business Dashboard / Reporting
        ↓
FINAL PROJECT
Complete Data Analytics Project
```

---

# 52. Instructor Tip

Do not teach every command as isolated syntax.

Use this pattern:

```text
Business Question
       ↓
What data do we need?
       ↓
Which Pandas operation?
       ↓
Write the code
       ↓
Read the result
       ↓
Explain the business meaning
```

For example:

```text
Question:
Which product generated the most profit?

        ↓

Pandas:
groupby() + sum()

        ↓

Code:
df.groupby("Product")["Profit"].sum()

        ↓

Sort:
.sort_values(ascending=False)

        ↓

Business interpretation:
Compare product-level profitability.
```

This helps students move from **Python syntax → Data Analyst thinking**.
