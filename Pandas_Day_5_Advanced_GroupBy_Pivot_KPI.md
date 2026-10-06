# Pandas for Data Analytics — Day 5
## Advanced GroupBy, Aggregation, Pivot & Business KPI Analysis — Google Colab Lab

> **Prerequisite:** Pandas Day 1 — Basics, Day 2 — Data Cleaning, Day 3 — Data Transformation & Business Analysis, Day 4 — Merge / Join / Concatenate.

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
Advanced GroupBy + Pivot + KPI Analysis
    ↓
DAY 6
EDA + Visualization
    ↓
DAY 7
Business Dashboard / Reporting
    ↓
FINAL PROJECT
Complete Data Analytics Project
```

Day 4 focused on bringing information together from multiple tables.

Today we take the merged dataset and perform **deeper business analysis**.

The progression is:

```text
Merged Dataset
      ↓
Advanced GroupBy
      ↓
Multiple Aggregations
      ↓
Named Aggregation
      ↓
MultiIndex
      ↓
reset_index()
      ↓
Pivot Tables
      ↓
Multiple Metrics
      ↓
Cross-tab Analysis
      ↓
Group-wise Ranking
      ↓
KPI Tables
      ↓
Business Insights
```

---

# 2. Today's Learning Objectives

By the end of this session, students will be able to:

- Understand advanced `groupby()` analysis
- Group data using multiple columns
- Apply multiple aggregation functions
- Use `agg()`
- Use named aggregation
- Understand `MultiIndex`
- Use `reset_index()`
- Sort grouped results
- Create calculated KPIs from grouped data
- Create advanced `pivot_table()`
- Use multiple values in a pivot table
- Use multiple indexes and columns
- Use `margins=True`
- Use `fill_value`
- Use `pd.crosstab()`
- Calculate percentages from grouped results
- Rank records within groups
- Find top products within each category
- Compare business performance across dimensions
- Build management-ready KPI summary tables
- Answer real-world business questions

---

# 3. Today's Dataset

Use the **Day 4 merged dataset** created from:

```text
day4_customers.csv
day4_products.csv
day4_orders.csv
```

The final analysis table should contain information such as:

```text
Order_ID
Order_Date
Customer_ID
Customer_Name
Customer_Segment
City
Region
Product_ID
Product
Category
Unit_Price
Quantity
Discount
Salesperson
Payment_Mode
Gross_Sales
Discount_Amount
Net_Sales
Cost
Profit
Profit_Margin
```

If you already completed Day 4, continue with:

```python
sales_analysis
```

If the merged dataset was exported, load:

```python
sales_analysis = pd.read_csv(
    "day4_sales_analysis_merged.csv"
)
```

---

# 4. Open Google Colab

Open:

```text
https://colab.research.google.com/
```

Create a new notebook.

Suggested name:

```text
Pandas_Day_5_Advanced_GroupBy_Pivot_KPI
```

Upload:

```text
day4_sales_analysis_merged.csv
```

---

# 5. Import Libraries

```python
import pandas as pd
import numpy as np
```

---

# 6. Load the Dataset

```python
sales_analysis = pd.read_csv(
    "day4_sales_analysis_merged.csv"
)
```

Display:

```python
sales_analysis.head()
```

Check:

```python
sales_analysis.shape
```

```python
sales_analysis.info()
```

---

# 7. Quick Data Validation

Check missing values:

```python
sales_analysis.isnull().sum()
```

Check duplicates:

```python
sales_analysis.duplicated().sum()
```

Check columns:

```python
sales_analysis.columns
```

Convert date:

```python
sales_analysis["Order_Date"] = pd.to_datetime(
    sales_analysis["Order_Date"]
)
```

---

# 8. Day 4 Revision — Basic GroupBy

Day 4 already introduced basic `groupby()`.

Example:

```python
sales_analysis.groupby(
    "Category"
)["Net_Sales"].sum()
```

Today we go further.

Instead of asking:

```text
What are total sales by category?
```

we ask:

```text
How many orders?
How many units?
Total sales?
Average sales?
Total profit?
Average profit?
Average rating?
```

all at the same time.

---

# 9. GroupBy Multiple Columns

Group by:

```text
Region + Category
```

```python
region_category = sales_analysis.groupby(
    ["Region", "Category"]
)["Net_Sales"].sum()
```

Display:

```python
region_category
```

This produces a hierarchical result.

---

# 10. Understanding MultiIndex

The result contains:

```text
Region
   +
Category
```

This is called a:

```text
MultiIndex
```

Example:

```text
Region   Category
South    Computers
South    Furniture
South    Mobiles
West     Computers
West     Furniture
...
```

MultiIndex is useful for grouped analysis but can be inconvenient for reporting.

---

# 11. Convert MultiIndex to Normal Columns

Use:

```python
region_category = region_category.reset_index()
```

Now:

```python
region_category
```

becomes a normal DataFrame.

Remember:

```text
groupby()
    ↓
MultiIndex
    ↓
reset_index()
    ↓
Normal DataFrame
```

---

# 12. GroupBy Three Dimensions

Analyze:

```text
Region
Category
Customer_Segment
```

```python
three_level = sales_analysis.groupby(
    ["Region", "Category", "Customer_Segment"]
)["Net_Sales"].sum()

three_level
```

Convert:

```python
three_level = three_level.reset_index()
```

---

# 13. Multiple Aggregations

Management wants:

```text
Orders
Units Sold
Total Sales
Average Sales
Total Profit
Average Profit
Average Rating
```

Use:

```python
category_performance = sales_analysis.groupby(
    "Category"
).agg(
    Orders=("Order_ID", "count"),
    Units_Sold=("Quantity", "sum"),
    Total_Sales=("Net_Sales", "sum"),
    Average_Sales=("Net_Sales", "mean"),
    Total_Profit=("Profit", "sum"),
    Average_Profit=("Profit", "mean"),
    Average_Rating=("Customer_Rating", "mean")
)
```

Display:

```python
category_performance
```

---

# 14. Named Aggregation

Named aggregation gives clean business-friendly column names.

Example:

```python
salesperson_performance = sales_analysis.groupby(
    "Salesperson"
).agg(
    Total_Orders=("Order_ID", "count"),
    Total_Units=("Quantity", "sum"),
    Revenue=("Net_Sales", "sum"),
    Profit=("Profit", "sum"),
    Avg_Rating=("Customer_Rating", "mean")
)
```

This is much easier to use in reports.

---

# 15. Round the Results

```python
salesperson_performance = (
    salesperson_performance.round(2)
)
```

---

# 16. Sort the Business Summary

Sort by revenue:

```python
salesperson_performance.sort_values(
    "Revenue",
    ascending=False
)
```

Sort by profit:

```python
salesperson_performance.sort_values(
    "Profit",
    ascending=False
)
```

Important:

> The highest revenue person is not automatically the highest profit person.

---

# 17. Create a Profit Margin KPI

For a grouped table:

```python
salesperson_performance["Profit_Margin"] = (
    salesperson_performance["Profit"] /
    salesperson_performance["Revenue"] * 100
).round(2)
```

Display:

```python
salesperson_performance
```

Now management can compare:

```text
Revenue
Profit
Profit Margin
```

---

# 18. GroupBy with Different Aggregations

One column can use one aggregation and another column can use another.

```python
product_performance = sales_analysis.groupby(
    "Product"
).agg(
    Orders=("Order_ID", "count"),
    Units_Sold=("Quantity", "sum"),
    Revenue=("Net_Sales", "sum"),
    Profit=("Profit", "sum"),
    Avg_Discount=("Discount", "mean")
)
```

---

# 19. Find Top Products

```python
product_performance.sort_values(
    "Revenue",
    ascending=False
).head(10)
```

Top profit products:

```python
product_performance.sort_values(
    "Profit",
    ascending=False
).head(10)
```

Top units sold:

```python
product_performance.sort_values(
    "Units_Sold",
    ascending=False
).head(10)
```

---

# 20. GroupBy Region + Category

```python
region_category_summary = sales_analysis.groupby(
    ["Region", "Category"]
).agg(
    Orders=("Order_ID", "count"),
    Units=("Quantity", "sum"),
    Sales=("Net_Sales", "sum"),
    Profit=("Profit", "sum")
).round(2)
```

Convert:

```python
region_category_summary = (
    region_category_summary.reset_index()
)
```

---

# 21. Sort Region + Category Results

```python
region_category_summary.sort_values(
    ["Region", "Sales"],
    ascending=[True, False]
)
```

This allows us to see:

```text
South
   ↓
Best category
Second category
Third category

West
   ↓
Best category
Second category
...
```

---

# 22. GroupBy Region + Customer Segment

```python
region_segment = sales_analysis.groupby(
    ["Region", "Customer_Segment"]
).agg(
    Orders=("Order_ID", "count"),
    Sales=("Net_Sales", "sum"),
    Profit=("Profit", "sum")
).round(2).reset_index()
```

This answers:

> Which customer segment is strongest in each region?

---

# 23. Find Average Order Value by Segment

First calculate:

```python
segment_summary = sales_analysis.groupby(
    "Customer_Segment"
).agg(
    Orders=("Order_ID", "nunique"),
    Sales=("Net_Sales", "sum"),
    Profit=("Profit", "sum")
)
```

Then:

```python
segment_summary["Average_Order_Value"] = (
    segment_summary["Sales"] /
    segment_summary["Orders"]
).round(2)
```

Display:

```python
segment_summary
```

---

# 24. Percentage Contribution

Find each category's percentage of total sales.

```python
category_sales = sales_analysis.groupby(
    "Category"
)["Net_Sales"].sum()
```

Calculate:

```python
category_percentage = (
    category_sales /
    category_sales.sum() * 100
).round(2)
```

Display:

```python
category_percentage
```

Business meaning:

> What percentage of company revenue comes from each category?

---

# 25. Add Percentage to a Summary Table

```python
category_report = sales_analysis.groupby(
    "Category"
).agg(
    Sales=("Net_Sales", "sum"),
    Profit=("Profit", "sum"),
    Orders=("Order_ID", "count")
)
```

Add:

```python
category_report["Sales_Percentage"] = (
    category_report["Sales"] /
    category_report["Sales"].sum() * 100
).round(2)
```

---

# 26. Pivot Table Revision

Day 4 introduced:

```python
pd.pivot_table()
```

Today we build more advanced pivot reports.

Basic:

```python
pd.pivot_table(
    sales_analysis,
    values="Net_Sales",
    index="Category",
    columns="Region",
    aggfunc="sum",
    fill_value=0
)
```

---

# 27. Pivot Table with Multiple Metrics

```python
pivot_multi = pd.pivot_table(
    sales_analysis,
    values=["Net_Sales", "Profit"],
    index="Category",
    columns="Region",
    aggfunc="sum",
    fill_value=0
)
```

Display:

```python
pivot_multi
```

This compares:

```text
Category
   ×
Region
   ×
Sales + Profit
```

---

# 28. Pivot Table with Multiple Indexes

```python
pivot_segment = pd.pivot_table(
    sales_analysis,
    values="Net_Sales",
    index=["Region", "Category"],
    columns="Customer_Segment",
    aggfunc="sum",
    fill_value=0
)
```

This answers:

> How much revenue does each customer segment generate for each category in each region?

---

# 29. Pivot Table with Multiple Aggregations

```python
pivot_metrics = pd.pivot_table(
    sales_analysis,
    values="Net_Sales",
    index="Category",
    columns="Region",
    aggfunc=["sum", "mean"],
    fill_value=0
)
```

Now we have:

```text
Total Sales
Average Sales
```

for each:

```text
Category × Region
```

---

# 30. Pivot Table with `margins=True`

`margins=True` adds totals.

```python
pivot_total = pd.pivot_table(
    sales_analysis,
    values="Net_Sales",
    index="Category",
    columns="Region",
    aggfunc="sum",
    fill_value=0,
    margins=True
)
```

This gives:

```text
Category totals
Region totals
Grand total
```

Very useful for management reports.

---

# 31. Pivot Table with `margins_name`

Rename the total row/column:

```python
pivot_total = pd.pivot_table(
    sales_analysis,
    values="Net_Sales",
    index="Category",
    columns="Region",
    aggfunc="sum",
    fill_value=0,
    margins=True,
    margins_name="Grand Total"
)
```

---

# 32. Pivot by Salesperson and Region

```python
salesperson_region = pd.pivot_table(
    sales_analysis,
    values="Net_Sales",
    index="Salesperson",
    columns="Region",
    aggfunc="sum",
    fill_value=0,
    margins=True
)
```

Business question:

> Which salesperson performs best in each region?

---

# 33. Pivot by Product Category and Payment Mode

```python
payment_category = pd.pivot_table(
    sales_analysis,
    values="Net_Sales",
    index="Category",
    columns="Payment_Mode",
    aggfunc="sum",
    fill_value=0
)
```

Business question:

> Which payment methods contribute most to sales for each category?

---

# 34. What is `pd.crosstab()`?

`crosstab()` is useful for frequency or count analysis between categorical variables.

Example:

```python
pd.crosstab(
    sales_analysis["Region"],
    sales_analysis["Customer_Segment"]
)
```

This shows:

```text
Region × Customer Segment
```

as counts.

---

# 35. Crosstab with Percentages

```python
region_segment_pct = pd.crosstab(
    sales_analysis["Region"],
    sales_analysis["Customer_Segment"],
    normalize="index"
) * 100
```

Round:

```python
region_segment_pct.round(2)
```

Business meaning:

> Within each region, what percentage of orders come from each customer segment?

---

# 36. Crosstab with Multiple Dimensions

```python
pd.crosstab(
    sales_analysis["Category"],
    sales_analysis["Payment_Mode"]
)
```

This gives order counts by:

```text
Category × Payment Mode
```

---

# 37. Group-Wise Ranking

Now we move beyond overall ranking.

Question:

> What is the top product within each category?

First calculate product sales:

```python
product_category_sales = sales_analysis.groupby(
    ["Category", "Product"]
)["Net_Sales"].sum().reset_index()
```

Then rank inside each category:

```python
product_category_sales["Rank"] = (
    product_category_sales
    .groupby("Category")["Net_Sales"]
    .rank(
        ascending=False,
        method="dense"
    )
)
```

---

# 38. Find Top Product in Each Category

```python
top_products = product_category_sales[
    product_category_sales["Rank"] == 1
]
```

Display:

```python
top_products
```

Business meaning:

> Find the best-selling product inside every product category.

---

# 39. Top 3 Products Per Category

```python
top_3_products = product_category_sales[
    product_category_sales["Rank"] <= 3
]
```

Sort:

```python
top_3_products.sort_values(
    ["Category", "Rank"]
)
```

---

# 40. Group-Wise Profit Ranking

```python
category_product_profit = sales_analysis.groupby(
    ["Category", "Product"]
)["Profit"].sum().reset_index()
```

Rank:

```python
category_product_profit["Profit_Rank"] = (
    category_product_profit
    .groupby("Category")["Profit"]
    .rank(
        ascending=False,
        method="dense"
    )
)
```

Top profit product per category:

```python
category_product_profit[
    category_product_profit["Profit_Rank"] == 1
]
```

---

# 41. Business KPI Summary

Create overall KPIs.

```python
total_orders = sales_analysis["Order_ID"].nunique()
total_sales = sales_analysis["Net_Sales"].sum()
total_profit = sales_analysis["Profit"].sum()
total_units = sales_analysis["Quantity"].sum()
average_rating = sales_analysis["Customer_Rating"].mean()
```

Average order value:

```python
average_order_value = (
    total_sales / total_orders
)
```

Overall margin:

```python
profit_margin = (
    total_profit /
    total_sales * 100
)
```

---

# 42. Create a KPI DataFrame

```python
kpi_summary = pd.DataFrame({
    "KPI": [
        "Total Orders",
        "Total Sales",
        "Total Profit",
        "Total Units Sold",
        "Average Order Value",
        "Average Customer Rating",
        "Profit Margin"
    ],
    "Value": [
        total_orders,
        round(total_sales, 2),
        round(total_profit, 2),
        total_units,
        round(average_order_value, 2),
        round(average_rating, 2),
        round(profit_margin, 2)
    ]
})
```

Display:

```python
kpi_summary
```

---

# 43. Management KPI Table by Region

```python
region_kpi = sales_analysis.groupby(
    "Region"
).agg(
    Orders=("Order_ID", "nunique"),
    Units=("Quantity", "sum"),
    Sales=("Net_Sales", "sum"),
    Profit=("Profit", "sum")
).reset_index()
```

Calculate margin:

```python
region_kpi["Profit_Margin"] = (
    region_kpi["Profit"] /
    region_kpi["Sales"] * 100
).round(2)
```

---

# 44. Find Best and Weakest Regions

Best sales region:

```python
region_kpi.sort_values(
    "Sales",
    ascending=False
).head(1)
```

Best profit region:

```python
region_kpi.sort_values(
    "Profit",
    ascending=False
).head(1)
```

Lowest sales region:

```python
region_kpi.sort_values(
    "Sales"
).head(1)
```

---

# 45. Conditional Business Analysis

Find categories with profit above the average category profit.

First:

```python
category_profit = sales_analysis.groupby(
    "Category"
)["Profit"].sum()
```

Average:

```python
average_category_profit = (
    category_profit.mean()
)
```

Filter:

```python
category_profit[
    category_profit > average_category_profit
]
```

---

# 46. Find High-Performing Salespeople

```python
salesperson_kpi = sales_analysis.groupby(
    "Salesperson"
).agg(
    Orders=("Order_ID", "nunique"),
    Sales=("Net_Sales", "sum"),
    Profit=("Profit", "sum")
).reset_index()
```

Calculate:

```python
salesperson_kpi["Profit_Margin"] = (
    salesperson_kpi["Profit"] /
    salesperson_kpi["Sales"] * 100
).round(2)
```

Sort:

```python
salesperson_kpi.sort_values(
    "Profit",
    ascending=False
)
```

---

# 47. Compare Revenue vs Profit

Create:

```python
salesperson_kpi.sort_values(
    "Sales",
    ascending=False
)
```

Then:

```python
salesperson_kpi.sort_values(
    "Profit",
    ascending=False
)
```

Ask students:

> Is the salesperson with the highest revenue also the salesperson with the highest profit?

This introduces an important analytical concept:

```text
Revenue ≠ Profit
```

---

# 48. Business Question Challenge

Students should solve these without looking at previous answers.

## Task 1

Find:

```text
Total Sales
Total Profit
Total Orders
Average Order Value
```

## Task 2

Create a category performance table containing:

```text
Orders
Units
Sales
Profit
Average Sales
Average Profit
```

## Task 3

Find the top 5 categories by profit.

## Task 4

Find the top 10 products by revenue.

## Task 5

Find the top 3 products within each category.

## Task 6

Find total sales by:

```text
Region + Category
```

## Task 7

Find total profit by:

```text
Region + Customer Segment
```

## Task 8

Create a pivot table:

```text
Category × Region
```

using:

```text
Sales
```

## Task 9

Create a pivot table:

```text
Category × Region
```

using:

```text
Sales + Profit
```

## Task 10

Create a pivot table with:

```text
margins=True
```

and explain the Grand Total.

## Task 11

Create a crosstab:

```text
Region × Customer Segment
```

showing order counts.

## Task 12

Create the same crosstab using row percentages.

## Task 13

Find the best salesperson in each region.

## Task 14

Find the highest-profit product within each category.

## Task 15

Find the category contributing the highest percentage of total sales.

---

# 49. Mini Project

## Project Title

**Retail Sales Performance & KPI Analysis using Pandas**

### Business Problem

Management already has a merged retail sales dataset.

They now need a detailed performance report.

The analyst must answer:

```text
1. What is total company revenue?
2. What is total profit?
3. Which category performs best?
4. Which product performs best?
5. Which region performs best?
6. Which salesperson performs best?
7. Which customer segment generates the most revenue?
8. Which payment method is most frequently used?
9. What is the top product in each category?
10. Which categories contribute the most revenue?
```

---

# 50. Required Project Outputs

Students must produce:

## A. Overall KPI Table

```text
Total Orders
Total Sales
Total Profit
Total Units
Average Order Value
Average Rating
Profit Margin
```

## B. Category Performance

```text
Category
Orders
Units
Sales
Profit
Sales %
```

## C. Region Performance

```text
Region
Orders
Sales
Profit
Profit Margin
```

## D. Salesperson Performance

```text
Salesperson
Orders
Sales
Profit
Profit Margin
```

## E. Product Performance

```text
Product
Category
Units
Sales
Profit
Rank
```

## F. Category × Region Pivot

```text
Category
    ×
Region
    ↓
Sales
Profit
```

## G. Region × Customer Segment Crosstab

```text
Region
    ×
Customer Segment
```

---

# 51. Recommended Project Workflow

```text
Load Day 4 Dataset
       ↓
Validate Data
       ↓
Basic GroupBy Revision
       ↓
Multiple-Column GroupBy
       ↓
Multiple Aggregations
       ↓
Named Aggregation
       ↓
reset_index()
       ↓
Sort Results
       ↓
Calculate Percentages
       ↓
Advanced Pivot Tables
       ↓
Crosstab Analysis
       ↓
Group-Wise Ranking
       ↓
KPI Summary
       ↓
Business Questions
       ↓
Management Report
       ↓
Export Results
```

---

# 52. Export Project Results

Export KPI table:

```python
kpi_summary.to_csv(
    "day5_kpi_summary.csv",
    index=False
)
```

Export category report:

```python
category_report.to_csv(
    "day5_category_report.csv"
)
```

Export region KPI:

```python
region_kpi.to_csv(
    "day5_region_kpi.csv",
    index=False
)
```

Export salesperson KPI:

```python
salesperson_kpi.to_csv(
    "day5_salesperson_kpi.csv",
    index=False
)
```

Export pivot:

```python
pivot_total.to_csv(
    "day5_category_region_pivot.csv"
)
```

Export top products:

```python
top_3_products.to_csv(
    "day5_top_products_by_category.csv",
    index=False
)
```

---

# 53. Important Commands to Remember

```python
df.groupby()

df.groupby(
    ["Column1", "Column2"]
)

.agg()

reset_index()

sort_values()

pd.pivot_table()

fill_value=0

margins=True

margins_name="Grand Total"

pd.crosstab()

rank()

groupby().rank()

head()

nunique()

mean()

sum()

count()
```

---

# 54. SQL → Pandas Connection

| SQL / BI Concept | Pandas |
|---|---|
| GROUP BY | `groupby()` |
| Multiple GROUP BY columns | `groupby(["A", "B"])` |
| SUM | `sum()` |
| AVG | `mean()` |
| COUNT | `count()` |
| COUNT DISTINCT | `nunique()` |
| Multiple aggregations | `agg()` |
| Window-style ranking | `groupby().rank()` |
| Pivot report | `pivot_table()` |
| Frequency table | `crosstab()` |
| ORDER BY | `sort_values()` |
| Derived KPI | calculated column |

---

# 55. Day 5 Learning Flow

```text
Day 4 Merged Dataset
        ↓
Data Validation
        ↓
Basic GroupBy Revision
        ↓
Multiple-Column GroupBy
        ↓
MultiIndex
        ↓
reset_index()
        ↓
Multiple Aggregations
        ↓
Named Aggregation
        ↓
Business KPI Calculations
        ↓
Percentage Contribution
        ↓
Advanced Pivot Table
        ↓
Multiple Metrics
        ↓
Multiple Index / Columns
        ↓
margins=True
        ↓
Crosstab
        ↓
Row Percentage Analysis
        ↓
Group-Wise Ranking
        ↓
Top N Within Groups
        ↓
Management KPI Tables
        ↓
Business Questions
        ↓
Mini Project
        ↓
Export Reports
```

---

# 56. Instructor Teaching Pattern

Do not teach advanced `groupby()` and `pivot_table()` as isolated syntax.

Use this pattern:

```text
Business Question
       ↓
Which dimensions matter?
       ↓
Which metric should be measured?
       ↓
GroupBy or Pivot?
       ↓
Which aggregation?
       ↓
Build summary
       ↓
Sort / Rank
       ↓
Calculate KPI / Percentage
       ↓
Interpret the result
```

Example:

```text
Question:
Which product is best inside each category?

        ↓

Dimensions:
Category + Product

        ↓

Metric:
Net_Sales

        ↓

GroupBy:
Category + Product

        ↓

Rank:
Within each Category

        ↓

Filter:
Rank <= 3

        ↓

Business Insight:
Top 3 products per category
```

---

# 57. Day 5 Final Takeaway

Day 4 taught students:

```text
How to connect data
```

Day 5 teaches students:

```text
How to deeply analyze connected data
```

The important progression is:

```text
Multiple Tables
      ↓
Merged Dataset
      ↓
GroupBy
      ↓
Multiple Aggregations
      ↓
Pivot
      ↓
Crosstab
      ↓
Ranking
      ↓
KPI
      ↓
Business Insight
```

The goal is not just to create summary tables.

The goal is:

> **Turn a merged dataset into management-ready performance information.**

---

# 58. What Comes Next?

## DAY 6 — Exploratory Data Analysis + Visualization

Next concepts:

```text
EDA
 ↓
Understand Data Distribution
 ↓
Numerical Analysis
 ↓
Categorical Analysis
 ↓
Outlier Detection
 ↓
Correlation
 ↓
Matplotlib
 ↓
Line Chart
 ↓
Bar Chart
 ↓
Histogram
 ↓
Scatter Plot
 ↓
Business Visualization
```

Then:

```text
DAY 7
Dashboard / Reporting
        ↓
FINAL PROJECT
Complete Data Analytics Project
```
