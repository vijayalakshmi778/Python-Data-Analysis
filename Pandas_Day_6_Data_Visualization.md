# Day 6 — Data Visualization with Python
## Pandas + Matplotlib + Seaborn 

## What Is Data Visualization?

Data visualization means representing data using visual elements such as:

- bars
- lines
- points
- slices
- distributions

Instead of looking at:

```text
Category       Total_Sales
Computers     4500000
Mobiles       2100000
Storage       1600000
Networking    1300000
```

we can create a chart so that the difference between categories is immediately visible.

### Important idea

```text
Table → Numbers
Chart → Pattern
Insight → Business Decision
```

A good analyst does not create a chart just because Python can create it.

The analyst first asks:

> **What question am I trying to answer?**

---

# 5. Chart Selection — Teach This Before Coding

| Business Question | Recommended Chart |
|---|---|
| Which category has higher sales? | Bar Chart |
| Which salesperson sold more? | Horizontal Bar Chart |
| How did sales change over time? | Line Chart |
| What percentage belongs to each payment mode? | Pie Chart |
| Is quantity related to sales? | Scatter Plot |
| How are customer ratings distributed? | Histogram |
| Compare sales across regions and categories | Grouped/Stacked Bar |
| Show top 5 products | Horizontal Bar Chart |

### Simple rule

```text
Comparison      → Bar
Trend           → Line
Part-to-whole  → Pie
Relationship    → Scatter
Distribution    → Histogram
```

---

# 6. Install / Import Libraries

Run this in Google Colab:

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```

Set a clean plotting style:

```python
sns.set_theme(style="whitegrid")
```

---

# 7. Load the Same Dataset

Use the same dataset from the previous sessions.

```python
sa = pd.read_csv("day4_sales_analysis_merged.csv")
```

Convert the date column:

```python
sa["Order_Date"] = pd.to_datetime(sa["Order_Date"])
```

Check:

```python
sa.head()
```

---

# 8. Visualization 1 — Category Sales Bar Chart

## Business Question

> Which product category generates the highest sales?

We already created `cp` in Day 5.

If the variable is not available, recreate it:

```python
cp = sa.groupby("Category").agg(
    Orders=("Order_ID", "count"),
    Total_Sales=("Net_Sales", "sum"),
    Average_Sales=("Net_Sales", "mean"),
    Total_Profit=("Profit", "sum"),
    Total_sold_quantity=("Quantity", "sum")
).reset_index()
```

Create the chart:

```python
plt.figure(figsize=(10, 6))

plt.bar(
    cp["Category"],
    cp["Total_Sales"]
)

plt.title("Total Sales by Category")
plt.xlabel("Category")
plt.ylabel("Total Sales")

plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

---

# 9. Explain the Bar Chart

Tell students:

- X-axis → Category
- Y-axis → Total Sales
- Each bar → one category
- Taller bar → higher sales

### Business interpretation

Students should answer:

```text
Which category has the highest sales?
Which category has the lowest sales?
Is the difference large or small?
```

### Important teaching point

Do not stop at:

> "This is a bar chart."

Ask:

> "What business decision can this chart support?"

Example:

```text
The category with the highest sales may deserve
more inventory, marketing attention or sales focus.
```

---

# 10. Add Data Labels

A professional analyst often displays the values directly.

```python
plt.figure(figsize=(10, 6))

bars = plt.bar(
    cp["Category"],
    cp["Total_Sales"]
)

plt.title("Total Sales by Category")
plt.xlabel("Category")
plt.ylabel("Total Sales")

plt.xticks(rotation=45)

for bar in bars:
    value = bar.get_height()

    plt.text(
        bar.get_x() + bar.get_width() / 2,
        value,
        f"{value:,.0f}",
        ha="center",
        va="bottom"
    )

plt.tight_layout()
plt.show()
```

---

# 11. Visualization 2 — Salesperson Performance

## Business Question

> Which salesperson generated the most sales?

Use the Day 5 `spp` table.

If needed:

```python
spp = sa.groupby("Salesperson").agg(
    Orders=("Order_ID", "count"),
    Total_Sales=("Net_Sales", "sum"),
    Average_Sales=("Net_Sales", "mean"),
    Total_Profit=("Profit", "sum"),
    Total_sold_quantity=("Quantity", "sum")
).reset_index()
```

Create a horizontal bar chart:

```python
plt.figure(figsize=(10, 6))

salesperson_sorted = spp.sort_values(
    "Total_Sales",
    ascending=True
)

plt.barh(
    salesperson_sorted["Salesperson"],
    salesperson_sorted["Total_Sales"]
)

plt.title("Sales Performance by Salesperson")
plt.xlabel("Total Sales")
plt.ylabel("Salesperson")

plt.tight_layout()
plt.show()
```

---

# 12. Why Horizontal Bar Charts?

Explain:

When category names are long, horizontal bars are easier to read.

```text
Normal Bar
Category → difficult when names are long

Horizontal Bar
Name
████████████
████████
████████████████
```

Use:

```python
plt.barh()
```

when readability is better horizontally.

---

# 13. Visualization 3 — Sales Trend Over Time

This is an important real-world analyst skill.

## Business Question

> How did sales change month by month?

First create a monthly summary:

```python
monthly_sales = (
    sa.groupby(
        sa["Order_Date"].dt.to_period("M")
    )["Net_Sales"]
    .sum()
    .reset_index()
)
```

Convert the period back to timestamp:

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

---

# 14. Teach Students How to Read a Line Chart

Ask:

1. Which month has the highest sales?
2. Which month has the lowest sales?
3. Is sales increasing?
4. Is sales decreasing?
5. Are there sudden peaks?
6. Are there sudden drops?

### Important concept

A line chart is useful when:

```text
ORDER MATTERS
```

For example:

```text
January → February → March → April
```

The sequence has meaning.

---

# 15. Visualization 4 — Sales by Region

## Business Question

> Which region contributes the most sales?

Create summary:

```python
region_sales = (
    sa.groupby("Region")["Net_Sales"]
    .sum()
    .reset_index()
)
```

Create bar chart:

```python
plt.figure(figsize=(8, 5))

plt.bar(
    region_sales["Region"],
    region_sales["Net_Sales"]
)

plt.title("Sales by Region")
plt.xlabel("Region")
plt.ylabel("Net Sales")

plt.tight_layout()
plt.show()
```

---

# 16. Visualization 5 — Payment Mode Distribution

## Business Question

> Which payment methods are most commonly used?

Create summary:

```python
payment_counts = (
    sa["Payment_Mode"]
    .value_counts()
    .reset_index()
)

payment_counts.columns = [
    "Payment_Mode",
    "Count"
]
```

Create pie chart:

```python
plt.figure(figsize=(8, 8))

plt.pie(
    payment_counts["Count"],
    labels=payment_counts["Payment_Mode"],
    autopct="%1.1f%%",
    startangle=90
)

plt.title("Payment Mode Distribution")

plt.show()
```

---

# 17. Important Pie Chart Rule

Tell students:

Pie charts should be used carefully.

Good use:

```text
UPI             35%
Credit Card     30%
Net Banking     20%
Cash             15%
```

Bad use:

```text
20+ categories
```

If there are too many categories, use a bar chart.

---

# 18. Visualization 6 — Quantity vs Sales

## Business Question

> Does selling more units generally produce higher sales?

This is a relationship question.

Use a scatter plot:

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

---

# 19. Explain Scatter Plot

Each dot represents one order.

```text
X-axis → Quantity
Y-axis → Net Sales
Dot    → One observation/order
```

Students should look for:

- positive relationship
- negative relationship
- weak relationship
- clusters
- unusual observations

---

# 20. Visualization 7 — Customer Rating Distribution

## Business Question

> How are customer ratings distributed?

Use a histogram:

```python
plt.figure(figsize=(10, 6))

plt.hist(
    sa["Customer_Rating"],
    bins=10
)

plt.title("Customer Rating Distribution")
plt.xlabel("Customer Rating")
plt.ylabel("Number of Customers/Orders")

plt.tight_layout()
plt.show()
```

---

# 21. Explain Histogram

A histogram shows the distribution of numerical data.

Difference:

```text
Bar Chart
→ compares categories

Histogram
→ shows distribution of numeric values
```

Example:

```text
Rating 1 → how many?
Rating 2 → how many?
Rating 3 → how many?
Rating 4 → how many?
Rating 5 → how many?
```

---

# 22. Visualization 8 — Profit by Category

## Business Question

> Which category generates the most profit?

```python
category_profit = (
    sa.groupby("Category")["Profit"]
    .sum()
    .sort_values(ascending=False)
)
```

Create chart:

```python
plt.figure(figsize=(10, 6))

category_profit.plot(
    kind="bar"
)

plt.title("Total Profit by Category")
plt.xlabel("Category")
plt.ylabel("Total Profit")

plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

---

# 23. Visualization 9 — Top 10 Products by Sales

Use the Day 5 product analysis.

```python
product_sales = (
    sa.groupby("Product")["Net_Sales"]
    .sum()
    .sort_values(ascending=False)
    .head(10)
)
```

Create horizontal bar chart:

```python
plt.figure(figsize=(10, 6))

product_sales.sort_values().plot(
    kind="barh"
)

plt.title("Top 10 Products by Net Sales")
plt.xlabel("Net Sales")
plt.ylabel("Product")

plt.tight_layout()
plt.show()
```

---

# 24. Visualization 10 — Sales by Region and Category

This connects directly to the Day 5 `rcp` analysis.

Create:

```python
region_category_sales = (
    sa.groupby(["Region", "Category"])["Net_Sales"]
    .sum()
    .unstack(fill_value=0)
)
```

Plot:

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

# 25. Explain Grouped Bar Chart

This chart answers:

> "For each region, how do categories compare?"

Students should understand:

```text
Region
 ├── Category A
 ├── Category B
 ├── Category C
 └── ...
```

This is more useful than looking at a large table.

---

# 26. Visualization 11 — Stacked Bar Chart

Use the same data:

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

Explain:

### Grouped bar

Good for:

> Compare categories.

### Stacked bar

Good for:

> Understand composition within each region.

---

# 27. Visualization 12 — Sales vs Profit

Create a category summary:

```python
category_summary = sa.groupby("Category").agg(
    Total_Sales=("Net_Sales", "sum"),
    Total_Profit=("Profit", "sum")
).reset_index()
```

Plot two metrics:

```python
x = np.arange(len(category_summary))
width = 0.35

plt.figure(figsize=(12, 6))

plt.bar(
    x - width/2,
    category_summary["Total_Sales"],
    width,
    label="Sales"
)

plt.bar(
    x + width/2,
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

# 28. Explain the Business Difference

A category can have:

```text
High Sales
+
Low Profit
```

or:

```text
Moderate Sales
+
High Profit
```

Therefore:

> Sales alone does not tell the complete business story.

This is an important Data Analyst concept.

---

# 29. Visualization 13 — Correlation Heatmap

Introduce this only after students understand the basic charts.

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

Create correlation matrix:

```python
corr = sa[numeric_cols].corr()
```

Plot:

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

---

# 30. Explain Correlation

Correlation tells us how two numerical variables move in relation to each other.

Range:

```text
+1  → strong positive relationship
 0  → little/no linear relationship
-1  → strong negative relationship
```

Important:

> Correlation does NOT automatically mean causation.

---

# 31. Seaborn Introduction

Explain:

```text
Matplotlib
→ basic plotting foundation

Seaborn
→ higher-level statistical visualization
```

Example:

```python
sns.barplot(
    data=sa,
    x="Category",
    y="Net_Sales",
    estimator="sum"
)
```

Then:

```python
plt.title("Sales by Category")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

---

# 32. Visualization with `hue`

A useful Seaborn concept is `hue`.

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

Explain:

```text
x      → main category
y      → numerical metric
hue    → second category
```

---

# 33. Chart Formatting

Teach students that a chart should be readable.

Basic formatting:

```python
plt.figure(figsize=(10, 6))

plt.title("Chart Title", fontsize=16)
plt.xlabel("X Axis", fontsize=12)
plt.ylabel("Y Axis", fontsize=12)

plt.xticks(rotation=45)

plt.grid(axis="y", alpha=0.3)

plt.tight_layout()

plt.show()
```

---

# 34. Important Visualization Principles

## Principle 1 — Have a question

Do not create random charts.

Bad:

```text
I have data → let me create 15 charts.
```

Good:

```text
Business question → choose chart → analyze result.
```

---

## Principle 2 — Choose the correct chart

```text
Comparison → Bar
Trend → Line
Distribution → Histogram
Relationship → Scatter
Composition → Pie / Stacked Bar
```

---

## Principle 3 — Keep charts simple

Avoid:

- unnecessary 3D charts
- too many colors
- excessive labels
- confusing legends
- very small text
- unnecessary decorations

---

## Principle 4 — Always explain the insight

Never finish with:

> "This is the chart."

Finish with:

> "This chart shows that..."

---

# 35. Business Insight Format

Teach students this simple format:

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
Computers have the highest total sales.

Insight:
The Computers category is a major revenue contributor.

Action:
The company can monitor inventory and promotional activity
for this category.
```

---

# 36. KPI Visualization

Day 5 created:

```python
kpi_summary
```

Display it:

```python
kpi_summary
```

The KPIs include:

```text
Total Orders
Total Sales
Total Profit
Total Units Sold
```

Explain:

KPIs are not ordinary charts.

They are high-level business indicators.

Example:

```text
TOTAL SALES
₹XX,XX,XXX

TOTAL PROFIT
₹X,XX,XXX

TOTAL ORDERS
XXX

TOTAL UNITS
XXXX
```

---

# 37. Mini Dashboard Concept

Now combine the most useful visualizations.

A simple analytical dashboard should answer:

```text
1. How much did we sell?
2. How much profit did we make?
3. Which category performed best?
4. Which region performed best?
5. What is the sales trend?
6. Which products are important?
```

---

# 38. Dashboard-Style Visualization

Create the monthly sales:

```python
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

Create category sales:

```python
category_sales = (
    sa.groupby("Category")["Net_Sales"]
    .sum()
    .sort_values(ascending=False)
)
```

Create region sales:

```python
region_sales = (
    sa.groupby("Region")["Net_Sales"]
    .sum()
    .sort_values(ascending=False)
)
```

---

# 39. Subplots

Use subplots to place multiple charts in one figure.

```python
fig, axes = plt.subplots(
    2,
    2,
    figsize=(16, 10)
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

### Chart 3 — Monthly Trend

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

Finish:

```python
plt.tight_layout()
plt.show()
```

---

# 40. Teach the Students to Present the Dashboard

Ask students to explain the dashboard in this order:

```text
1. Overall performance
2. Category performance
3. Regional performance
4. Trend
5. Relationship / deeper analysis
```

Example:

```text
Overall:
Sales and profit indicate the overall business performance.

Category:
One or more categories contribute a major portion of sales.

Region:
Some regions outperform others.

Trend:
Monthly sales show the direction of business performance.

Relationship:
Quantity and sales can be examined to understand order behavior.
```

---

# 41. Day 6 Practical Assignment

## Sales Visualization Challenge

Use the same `day4_sales_analysis_merged.csv`.

Students must create the following:

### Task 1

Create a bar chart:

> Total Sales by Category

---

### Task 2

Create a horizontal bar chart:

> Top 10 Products by Sales

---

### Task 3

Create a line chart:

> Monthly Net Sales Trend

---

### Task 4

Create a bar chart:

> Total Profit by Region

---

### Task 5

Create a pie chart:

> Payment Mode Distribution

---

### Task 6

Create a scatter plot:

> Quantity vs Net Sales

---

### Task 7

Create a histogram:

> Customer Rating Distribution

---

### Task 8

Create a grouped bar chart:

> Sales by Region and Category

---

### Task 9

Create a correlation heatmap using numerical columns.

---

### Task 10

Create one 2×2 visualization dashboard using subplots.

---

# 42. Student Must Write Insights

For every chart, students must write at least one insight.

Use this format:

```text
Chart:
Sales by Category

Observation:
_____________________________

Insight:
_____________________________

Business Action:
_____________________________
```

---

# 43. Interview Questions

### Q1. What is data visualization?

Expected answer:

> Data visualization is the graphical representation of data to identify patterns, trends, comparisons and relationships.

---

### Q2. When do you use a bar chart?

> To compare values across categories.

---

### Q3. When do you use a line chart?

> To show trends or changes over an ordered sequence, especially time.

---

### Q4. When do you use a scatter plot?

> To study the relationship between two numerical variables.

---

### Q5. What is a histogram?

> A histogram shows the distribution of numerical data.

---

### Q6. What is the difference between a bar chart and histogram?

> A bar chart compares categories; a histogram shows the distribution of numerical values.

---

### Q7. What is correlation?

> Correlation measures the strength and direction of a linear relationship between numerical variables.

---

### Q8. Does correlation mean causation?

> No. Correlation does not prove causation.

---

### Q9. Why is visualization important for a Data Analyst?

> It helps communicate patterns and business insights clearly so stakeholders can make decisions.

---

### Q10. What is the most important part of a visualization?

> The business question and insight, not the chart itself.

---

# 44. Common Beginner Mistakes

## Mistake 1

Creating a chart before understanding the data.

### Correct approach:

```text
Understand → Analyze → Visualize
```

---

## Mistake 2

Using pie charts for too many categories.

### Correct approach:

Use a bar chart.

---

## Mistake 3

Using a line chart for unrelated categories.

### Correct approach:

Use a bar chart.

---

## Mistake 4

Using too many colors.

### Correct approach:

Keep the visualization simple and consistent.

---

## Mistake 5

No title or axis labels.

### Correct approach:

Every important chart should clearly communicate:

```text
What?
By what?
Measured how?
```

---

## Mistake 6

Showing the chart without explaining it.

### Correct approach:

Always provide:

```text
Observation
Insight
Action
```

---

# 45. Final Day 6 Flow

Teach in this exact sequence:

```text
DAY 5 RECAP
      ↓
What is Data Visualization?
      ↓
Business Question First
      ↓
Chart Selection
      ↓
Matplotlib
      ↓
Bar Chart
      ↓
Horizontal Bar Chart
      ↓
Line Chart
      ↓
Pie Chart
      ↓
Scatter Plot
      ↓
Histogram
      ↓
Grouped Bar Chart
      ↓
Stacked Bar Chart
      ↓
Seaborn
      ↓
Correlation Heatmap
      ↓
Chart Formatting
      ↓
Business Insights
      ↓
KPI Visualization
      ↓
2×2 Dashboard
      ↓
Practical Assignment
      ↓
Interview Questions
```

---

# 46. Final Message for Students

The goal of Day 6 is **not** to memorize:

```python
plt.bar()
plt.plot()
plt.pie()
plt.scatter()
plt.hist()
```

The real Data Analyst workflow is:

```text
Business Question
       ↓
Understand Data
       ↓
Prepare Data
       ↓
Aggregate Data
       ↓
Choose Correct Visualization
       ↓
Create Chart
       ↓
Interpret Chart
       ↓
Generate Business Insight
       ↓
Recommend Action
```

That is how visualization is used in a real Data Analytics project.
