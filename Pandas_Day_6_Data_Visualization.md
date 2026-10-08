# Day 6 — Data Visualization with Python

## Pandas + Matplotlib + Seaborn

---

## 1. What is Data Visualization?

Data visualization means showing data using charts and graphs.

Instead of looking at many numbers in a table, we use charts to understand the data easily.

Common charts:

- Bar Chart → Compare categories
- Line Chart → Show trends
- Pie Chart → Show percentage/share
- Scatter Plot → Show relationship
- Histogram → Show distribution

### Simple idea

```text
Data → Chart → Understand → Insight
```

---

# 2. Import Libraries

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```

Set the chart style:

```python
sns.set_theme(style="whitegrid")
```

---

# 3. Load the Dataset

Use the same dataset from Day 5.

```python
sa = pd.read_csv("day4_sales_analysis_merged.csv")
```

Convert the date column:

```python
sa["Order_Date"] = pd.to_datetime(sa["Order_Date"])
```

Check the data:

```python
sa.head()
```

---

# 4. Choosing the Correct Chart

| What do you want to know? | Chart |
|---|---|
| Compare categories | Bar Chart |
| Compare long names | Horizontal Bar Chart |
| Show change over time | Line Chart |
| Show percentage/share | Pie Chart |
| Check relationship between numbers | Scatter Plot |
| Understand distribution | Histogram |

### Remember

```text
Comparison     → Bar
Trend          → Line
Percentage     → Pie
Relationship   → Scatter
Distribution   → Histogram
```

---

# 5. Bar Chart — Sales by Category

### Question

Which category has the highest sales?

First create the summary:

```python
category_sales = (
    sa.groupby("Category")["Net_Sales"]
    .sum()
    .sort_values(ascending=False)
)

category_sales
```

Create the bar chart:

```python
plt.figure(figsize=(10, 6))

category_sales.plot(kind="bar")

plt.title("Total Sales by Category")
plt.xlabel("Category")
plt.ylabel("Net Sales")

plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

### Add values to the bars

A simple way is to use `bar_label()`.

```python
ax = category_sales.plot(kind="bar")

ax.bar_label(ax.containers[0])

plt.title("Total Sales by Category")
plt.xlabel("Category")
plt.ylabel("Net Sales")

plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

---

# 6. Horizontal Bar Chart — Sales by Salesperson

Horizontal bars are useful when category names are long.

### Question

Which salesperson generated the highest sales?

Create the summary:

```python
salesperson_sales = (
    sa.groupby("Salesperson")["Net_Sales"]
    .sum()
    .sort_values()
)

salesperson_sales
```

Create the chart:

```python
plt.figure(figsize=(10, 6))

salesperson_sales.plot(kind="barh")

plt.title("Sales by Salesperson")
plt.xlabel("Net Sales")
plt.ylabel("Salesperson")

plt.tight_layout()
plt.show()
```

---

# 7. Line Chart — Monthly Sales Trend

A line chart is useful for showing change over time.

### Question

How did sales change month by month?

Create monthly sales:

```python
monthly_sales = (
    sa.groupby(
        sa["Order_Date"].dt.to_period("M")
    )["Net_Sales"]
    .sum()
    .reset_index()
)
```

Convert the date:

```python
monthly_sales["Order_Date"] = (
    monthly_sales["Order_Date"].dt.to_timestamp()
)
```

Create the line chart:

```python
plt.figure(figsize=(12, 6))

plt.plot(
    monthly_sales["Order_Date"],
    monthly_sales["Net_Sales"],
    marker="o"
)

plt.title("Monthly Sales Trend")
plt.xlabel("Month")
plt.ylabel("Net Sales")

plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

### Look for

- Highest month
- Lowest month
- Increasing trend
- Decreasing trend
- Sudden increase
- Sudden decrease

---

# 8. Bar Chart — Sales by Region

### Question

Which region has the highest sales?

Create the summary:

```python
region_sales = (
    sa.groupby("Region")["Net_Sales"]
    .sum()
    .sort_values(ascending=False)
)

region_sales
```

Create the chart:

```python
plt.figure(figsize=(8, 5))

region_sales.plot(kind="bar")

plt.title("Sales by Region")
plt.xlabel("Region")
plt.ylabel("Net Sales")

plt.tight_layout()
plt.show()
```

---

# 9. Pie Chart — Payment Mode

A pie chart shows how a total is divided into parts.

### Question

Which payment mode is most commonly used?

Create the summary:

```python
payment_counts = sa["Payment_Mode"].value_counts()

payment_counts
```

Create the pie chart:

```python
plt.figure(figsize=(8, 8))

plt.pie(
    payment_counts,
    labels=payment_counts.index,
    autopct="%1.1f%%"
)

plt.title("Payment Mode Distribution")

plt.show()
```

`autopct` displays the percentage.

---

# 10. Scatter Plot — Quantity vs Net Sales

A scatter plot helps us check the relationship between two numerical columns.

### Question

Does quantity have a relationship with sales?

```python
plt.figure(figsize=(10, 6))

plt.scatter(
    sa["Quantity"],
    sa["Net_Sales"],
    alpha=0.6
)

plt.title("Quantity vs Net Sales")
plt.xlabel("Quantity")
plt.ylabel("Net Sales")

plt.tight_layout()
plt.show()
```

### Understand the chart

```text
X-axis → Quantity
Y-axis → Net Sales
Each dot → One order
```

---

# 11. Histogram — Customer Rating

A histogram shows how numerical values are distributed.

### Question

How are customer ratings distributed?

```python
plt.figure(figsize=(10, 6))

plt.hist(
    sa["Customer_Rating"],
    bins=10
)

plt.title("Customer Rating Distribution")
plt.xlabel("Customer Rating")
plt.ylabel("Count")

plt.tight_layout()
plt.show()
```

### Difference

```text
Bar Chart  → Compare categories
Histogram  → Show distribution of numbers
```

---

# 12. Profit by Category

### Question

Which category generates the most profit?

Create the summary:

```python
category_profit = (
    sa.groupby("Category")["Profit"]
    .sum()
    .sort_values(ascending=False)
)

category_profit
```

Create the chart:

```python
plt.figure(figsize=(10, 6))

category_profit.plot(kind="bar")

plt.title("Total Profit by Category")
plt.xlabel("Category")
plt.ylabel("Profit")

plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

---

# 13. Top 10 Products by Sales

### Question

Which products have the highest sales?

Create the summary:

```python
top_products = (
    sa.groupby("Product")["Net_Sales"]
    .sum()
    .sort_values(ascending=False)
    .head(10)
)

top_products
```

Create the chart:

```python
plt.figure(figsize=(10, 6))

top_products.sort_values().plot(kind="barh")

plt.title("Top 10 Products by Sales")
plt.xlabel("Net Sales")
plt.ylabel("Product")

plt.tight_layout()
plt.show()
```

---

# 14. Sales by Region and Category

We can compare two categories of information using a grouped bar chart.

Create the summary:

```python
region_category_sales = (
    sa.groupby(["Region", "Category"])["Net_Sales"]
    .sum()
    .unstack(fill_value=0)
)

region_category_sales
```

Create the chart:

```python
region_category_sales.plot(
    kind="bar",
    figsize=(12, 6)
)

plt.title("Sales by Region and Category")
plt.xlabel("Region")
plt.ylabel("Net Sales")

plt.xticks(rotation=0)
plt.tight_layout()
plt.show()
```

---

# 15. Stacked Bar Chart

A stacked bar chart shows the composition of each category.

Use the same `region_category_sales` table.

```python
region_category_sales.plot(
    kind="bar",
    stacked=True,
    figsize=(12, 6)
)

plt.title("Sales Composition by Region")
plt.xlabel("Region")
plt.ylabel("Net Sales")

plt.xticks(rotation=0)
plt.tight_layout()
plt.show()
```

### Difference

```text
Grouped Bar  → Compare categories

Stacked Bar  → See how categories make up the total
```

---

# 16. Sales vs Profit

Sales and profit are different.

A business can have high sales but lower profit.

Create the summary:

```python
category_summary = (
    sa.groupby("Category")
    .agg(
        Total_Sales=("Net_Sales", "sum"),
        Total_Profit=("Profit", "sum")
    )
    .reset_index()
)

category_summary
```

Create the chart:

```python
x = np.arange(len(category_summary))
width = 0.35

plt.figure(figsize=(12, 6))

plt.bar(
    x - width / 2,
    category_summary["Total_Sales"],
    width,
    label="Sales"
)

plt.bar(
    x + width / 2,
    category_summary["Total_Profit"],
    width,
    label="Profit"
)

plt.xticks(
    x,
    category_summary["Category"],
    rotation=45
)

plt.title("Sales vs Profit by Category")
plt.xlabel("Category")
plt.ylabel("Amount")
plt.legend()

plt.tight_layout()
plt.show()
```

---

# 17. Seaborn Bar Chart

Seaborn makes many charts easier to create.

```python
sns.barplot(
    data=sa,
    x="Category",
    y="Net_Sales",
    estimator="sum"
)

plt.title("Sales by Category")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

---

# 18. Adding `hue`

`hue` adds another category to the chart.

Example:

```python
plt.figure(figsize=(12, 6))

sns.barplot(
    data=sa,
    x="Region",
    y="Net_Sales",
    hue="Customer_Segment",
    estimator="sum"
)

plt.title("Sales by Region and Customer Segment")
plt.tight_layout()
plt.show()
```

Here:

```text
x     → Region
y     → Net Sales
hue   → Customer Segment
```

---

# 19. Correlation Heatmap

A heatmap can show the correlation between numerical columns.

Select numerical columns:

```python
numeric_cols = [
    "Quantity",
    "Discount",
    "Customer_Rating",
    "Unit_Price",
    "Gross_Sales",
    "Discount_Amount",
    "Net_Sales",
    "Cost",
    "Profit",
    "Profit_Margin"
]
```

Create the correlation matrix:

```python
corr = sa[numeric_cols].corr()

corr
```

Create the heatmap:

```python
plt.figure(figsize=(12, 8))

sns.heatmap(
    corr,
    annot=True,
    fmt=".2f",
    cmap="coolwarm"
)

plt.title("Correlation Heatmap")

plt.tight_layout()
plt.show()
```

### Correlation values

```text
+1  → Positive relationship
 0  → Little or no linear relationship
-1  → Negative relationship
```

---

# 20. Basic Chart Formatting

Use these commands to make charts easier to read.

```python
plt.figure(figsize=(10, 6))

plt.title("Chart Title")
plt.xlabel("X Axis")
plt.ylabel("Y Axis")

plt.xticks(rotation=45)

plt.tight_layout()
plt.show()
```

### Useful options

```python
plt.xticks(rotation=45)
```

Rotates X-axis labels.

```python
plt.tight_layout()
```

Adjusts the spacing.

```python
plt.show()
```

Displays the chart.

---

# 21. Writing a Business Insight

After creating a chart, look at the result and write a simple conclusion.

Use:

```text
Observation:
What do I see?

Insight:
What does it mean?

Action:
What could the business do?
```

Example:

```text
Observation:
One category has the highest sales.

Insight:
That category is an important contributor to revenue.

Action:
The business can monitor its inventory and sales performance.
```

---

# 22. Simple KPI Summary

You can also display important business numbers.

```python
total_orders = sa["Order_ID"].nunique()
total_sales = sa["Net_Sales"].sum()
total_profit = sa["Profit"].sum()
total_quantity = sa["Quantity"].sum()

print("Total Orders   :", total_orders)
print("Total Sales    :", total_sales)
print("Total Profit   :", total_profit)
print("Total Quantity :", total_quantity)
```

---

# 23. Simple Dashboard

We can place multiple charts in one figure.

First create the summaries:

```python
category_sales = (
    sa.groupby("Category")["Net_Sales"]
    .sum()
    .sort_values(ascending=False)
)

region_sales = (
    sa.groupby("Region")["Net_Sales"]
    .sum()
    .sort_values(ascending=False)
)

monthly_sales = (
    sa.groupby(
        sa["Order_Date"].dt.to_period("M")
    )["Net_Sales"]
    .sum()
    .reset_index()
)

monthly_sales["Order_Date"] = (
    monthly_sales["Order_Date"].dt.to_timestamp()
)
```

Create the dashboard:

```python
fig, axes = plt.subplots(
    2,
    2,
    figsize=(14, 9)
)
```

### Chart 1 — Category Sales

```python
category_sales.plot(
    kind="bar",
    ax=axes[0, 0]
)

axes[0, 0].set_title("Sales by Category")
axes[0, 0].tick_params(axis="x", rotation=45)
```

### Chart 2 — Region Sales

```python
region_sales.plot(
    kind="bar",
    ax=axes[0, 1]
)

axes[0, 1].set_title("Sales by Region")
```

### Chart 3 — Monthly Sales

```python
axes[1, 0].plot(
    monthly_sales["Order_Date"],
    monthly_sales["Net_Sales"],
    marker="o"
)

axes[1, 0].set_title("Monthly Sales Trend")
axes[1, 0].tick_params(axis="x", rotation=45)
```

### Chart 4 — Quantity vs Sales

```python
axes[1, 1].scatter(
    sa["Quantity"],
    sa["Net_Sales"],
    alpha=0.5
)

axes[1, 1].set_title("Quantity vs Net Sales")
axes[1, 1].set_xlabel("Quantity")
axes[1, 1].set_ylabel("Net Sales")
```

Finish the dashboard:

```python
plt.tight_layout()
plt.show()
```

---

# 24. Practice

Use the same dataset and create these charts.

### Task 1

Create a bar chart:

```text
Total Sales by Category
```

### Task 2

Create a horizontal bar chart:

```text
Top 10 Products by Sales
```

### Task 3

Create a line chart:

```text
Monthly Net Sales
```

### Task 4

Create a bar chart:

```text
Total Profit by Region
```

### Task 5

Create a pie chart:

```text
Payment Mode Distribution
```

### Task 6

Create a scatter plot:

```text
Quantity vs Net Sales
```

### Task 7

Create a histogram:

```text
Customer Rating Distribution
```

### Task 8

Create a grouped bar chart:

```text
Sales by Region and Category
```

### Task 9

Create a correlation heatmap using numerical columns.

### Task 10

Create a 2 × 2 dashboard using four different charts.

---

# 25. Final Visualization Flow

```text
Load Data
    ↓
Understand Data
    ↓
Create Summary
    ↓
Choose Chart
    ↓
Create Chart
    ↓
Format Chart
    ↓
Read the Chart
    ↓
Find Insight
```

## Most Important Charts

```text
Bar Chart
    ↓
Compare Categories

Line Chart
    ↓
Show Trends

Pie Chart
    ↓
Show Share

Scatter Plot
    ↓
Find Relationships

Histogram
    ↓
Understand Distribution
```

# Day 6 Complete

You have now worked with:

- Matplotlib
- Seaborn
- Bar Chart
- Horizontal Bar Chart
- Line Chart
- Pie Chart
- Scatter Plot
- Histogram
- Grouped Bar Chart
- Stacked Bar Chart
- Heatmap
- Data Labels
- Basic Formatting
- Business Insights
- Simple Dashboard
