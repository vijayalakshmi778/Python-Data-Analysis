# Pandas Day 6 — Exploratory Data Analysis & Visualization with Python

## Data Analytics Training — Google Colab

### Prerequisite
Complete Day 1–5 Pandas sessions.

**Day 6 Focus:** Turn analysis into clear business visualizations using Python, Pandas, NumPy, and Matplotlib.

---

# 1. Day 6 Roadmap

1. Exploratory Data Analysis (EDA)
2. Understanding the dataset before visualization
3. Choosing the correct chart
4. Matplotlib fundamentals
5. Bar charts
6. Horizontal bar charts
7. Line charts
8. Histograms
9. Box plots
10. Scatter plots
11. Trend lines
12. Pie charts
13. Grouped bar charts
14. Subplots
15. Chart formatting
16. Business interpretation
17. Visualization challenge
18. Mini Project — Retail Sales EDA & Visualization

---

# 2. Learning Objectives

By the end of Day 6, students should be able to:

- Perform basic EDA using Pandas
- Identify numerical and categorical columns
- Select an appropriate chart for a business question
- Create professional charts using Matplotlib
- Visualize comparisons, trends, distributions, outliers, and relationships
- Add titles, labels, legends, grids, and annotations
- Interpret charts from a business perspective
- Save charts as high-quality image files
- Build a complete EDA visualization report

---

# 3. Tools Used

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```

Optional:

```python
plt.style.use("default")
```

---

# 4. Dataset

Use the Day 4 merged sales dataset:

```text
day4_sales_analysis_merged.csv
```

The dataset combines customer, product, and order information and is used throughout the Day 6 visualization exercises.

Upload the CSV file to Google Colab.

---

# 5. Open Google Colab

Open:

```text
https://colab.research.google.com/
```

Create a new Python notebook.

Upload:

```text
day4_sales_analysis_merged.csv
```

---

# 6. Import Libraries

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```

---

# 7. Load the Dataset

```python
df = pd.read_csv("day4_sales_analysis_merged.csv")

df.head()
```

Check the number of records:

```python
df.shape
```

Check column names:

```python
df.columns
```

Check data types:

```python
df.dtypes
```

---

# 8. Prepare Date Columns

If the dataset contains an order date column, convert it to datetime.

First inspect the columns:

```python
df.columns.tolist()
```

If the date column is named `OrderDate`:

```python
df["OrderDate"] = pd.to_datetime(df["OrderDate"])
```

Check:

```python
df["OrderDate"].dtype
```

If your dataset uses a different date-column name, use that column instead.

---

# 9. Exploratory Data Analysis — EDA

EDA means:

> Exploring the dataset before making conclusions.

EDA helps us understand:

- Data structure
- Missing values
- Duplicate records
- Numerical distributions
- Categories
- Outliers
- Trends
- Relationships

---

# 10. Basic EDA Checks

## First 5 rows

```python
df.head()
```

## Last 5 rows

```python
df.tail()
```

## Dataset size

```python
df.shape
```

## Column information

```python
df.info()
```

## Statistical summary

```python
df.describe()
```

## Missing values

```python
df.isnull().sum()
```

## Duplicate rows

```python
df.duplicated().sum()
```

---

# 11. Numerical and Categorical Columns

Numerical columns:

```python
df.select_dtypes(include=np.number).columns
```

Categorical columns:

```python
df.select_dtypes(include="object").columns
```

This is important because different data types require different visualization techniques.

---

# 12. Choosing the Correct Chart

| Business Question | Recommended Chart |
|---|---|
| Compare categories | Bar chart |
| Compare many categories | Horizontal bar chart |
| Show trend over time | Line chart |
| Show distribution | Histogram |
| Identify outliers | Box plot |
| Show relationship between two variables | Scatter plot |
| Show simple percentage share | Pie chart |
| Compare several related views | Subplots |

### Important Rule

Do not choose a chart only because it looks attractive.

Choose the chart that answers the business question clearly.

---

# 13. Matplotlib Fundamentals

Basic structure:

```python
plt.figure(figsize=(10, 6))

plt.plot(x, y)

plt.title("Chart Title")
plt.xlabel("X Axis")
plt.ylabel("Y Axis")

plt.show()
```

Important commands:

```python
plt.figure()
plt.title()
plt.xlabel()
plt.ylabel()
plt.legend()
plt.grid()
plt.xticks()
plt.yticks()
plt.tight_layout()
plt.show()
```

---

# 14. Visualization 1 — Sales by Category

### Business Question

Which product category generates the highest sales?

First calculate category sales:

```python
category_sales = (
    df.groupby("Category")["Sales"]
      .sum()
      .sort_values(ascending=False)
)

category_sales
```

Create the chart:

```python
plt.figure(figsize=(10, 6))

plt.bar(category_sales.index, category_sales.values)

plt.title("Sales by Category")
plt.xlabel("Category")
plt.ylabel("Total Sales")

plt.tight_layout()
plt.show()
```

### Business Interpretation

Look for:

- Highest-selling category
- Lowest-selling category
- Large differences between categories

---

# 15. Add Values to Bar Chart

```python
plt.figure(figsize=(10, 6))

bars = plt.bar(category_sales.index, category_sales.values)

plt.title("Sales by Category")
plt.xlabel("Category")
plt.ylabel("Total Sales")

for bar in bars:
    height = bar.get_height()
    plt.text(
        bar.get_x() + bar.get_width() / 2,
        height,
        f"{height:,.0f}",
        ha="center",
        va="bottom"
    )

plt.tight_layout()
plt.show()
```

This makes the chart easier to read.

---

# 16. Visualization 2 — Top 10 Products

### Business Question

Which products generate the highest sales?

```python
top_products = (
    df.groupby("ProductName")["Sales"]
      .sum()
      .sort_values(ascending=False)
      .head(10)
)

top_products
```

Use a horizontal bar chart:

```python
plt.figure(figsize=(10, 6))

plt.barh(top_products.index[::-1], top_products.values[::-1])

plt.title("Top 10 Products by Sales")
plt.xlabel("Total Sales")
plt.ylabel("Product")

plt.tight_layout()
plt.show()
```

### Why Horizontal Bar?

Product names can be long.

Horizontal bars make long category names easier to read.

---

# 17. Visualization 3 — Monthly Sales Trend

### Business Question

How are sales changing over time?

Create a month column:

```python
df["Month"] = df["OrderDate"].dt.to_period("M").astype(str)
```

Calculate monthly sales:

```python
monthly_sales = (
    df.groupby("Month")["Sales"]
      .sum()
      .sort_index()
)
```

Create the line chart:

```python
plt.figure(figsize=(12, 6))

plt.plot(
    monthly_sales.index,
    monthly_sales.values,
    marker="o"
)

plt.title("Monthly Sales Trend")
plt.xlabel("Month")
plt.ylabel("Total Sales")

plt.xticks(rotation=45)
plt.grid()
plt.tight_layout()
plt.show()
```

### Business Interpretation

Look for:

- Increasing sales
- Decreasing sales
- Peak months
- Weak months
- Seasonal patterns

---

# 18. Visualization 4 — Monthly Profit Trend

### Business Question

Is profit following the same trend as sales?

```python
monthly_profit = (
    df.groupby("Month")["Profit"]
      .sum()
      .sort_index()
)
```

```python
plt.figure(figsize=(12, 6))

plt.plot(
    monthly_profit.index,
    monthly_profit.values,
    marker="o"
)

plt.title("Monthly Profit Trend")
plt.xlabel("Month")
plt.ylabel("Total Profit")

plt.xticks(rotation=45)
plt.grid()
plt.tight_layout()
plt.show()
```

---

# 19. Visualization 5 — Sales vs Profit Trend

```python
plt.figure(figsize=(12, 6))

plt.plot(
    monthly_sales.index,
    monthly_sales.values,
    marker="o",
    label="Sales"
)

plt.plot(
    monthly_profit.index,
    monthly_profit.values,
    marker="o",
    label="Profit"
)

plt.title("Monthly Sales vs Profit")
plt.xlabel("Month")
plt.ylabel("Amount")

plt.xticks(rotation=45)
plt.legend()
plt.grid()

plt.tight_layout()
plt.show()
```

### Business Question

Does increasing sales always mean increasing profit?

This is an important business analytics question.

---

# 20. Visualization 6 — Sales Distribution

### Business Question

How are individual order sales distributed?

```python
plt.figure(figsize=(10, 6))

plt.hist(
    df["Sales"],
    bins=20
)

plt.title("Distribution of Sales")
plt.xlabel("Sales")
plt.ylabel("Number of Orders")

plt.tight_layout()
plt.show()
```

### Understand

A histogram shows:

- Frequency
- Concentration
- Spread
- Skewness
- Possible unusual values

---

# 21. Visualization 7 — Profit Distribution

```python
plt.figure(figsize=(10, 6))

plt.hist(
    df["Profit"],
    bins=20
)

plt.title("Distribution of Profit")
plt.xlabel("Profit")
plt.ylabel("Number of Orders")

plt.tight_layout()
plt.show()
```

### Business Questions

- Are most orders profitable?
- Are there negative-profit orders?
- Is profit concentrated around a particular range?

---

# 22. Visualization 8 — Box Plot for Sales

### Business Question

Are there unusual sales values?

```python
plt.figure(figsize=(8, 5))

plt.boxplot(df["Sales"].dropna())

plt.title("Sales Distribution and Outliers")
plt.ylabel("Sales")

plt.show()
```

A box plot helps identify:

- Median
- Quartiles
- Spread
- Potential outliers

---

# 23. Visualization 9 — Sales by Category Using Box Plot

```python
categories = df["Category"].dropna().unique()

data = [
    df.loc[df["Category"] == category, "Sales"].dropna()
    for category in categories
]

plt.figure(figsize=(10, 6))

plt.boxplot(
    data,
    labels=categories
)

plt.title("Sales Distribution by Category")
plt.xlabel("Category")
plt.ylabel("Sales")

plt.tight_layout()
plt.show()
```

### Business Interpretation

Compare:

- Median sales
- Spread
- Outliers
- Variability between categories

---

# 24. Visualization 10 — Sales vs Profit

### Business Question

Is there a relationship between sales and profit?

```python
plt.figure(figsize=(10, 6))

plt.scatter(
    df["Sales"],
    df["Profit"],
    alpha=0.6
)

plt.title("Sales vs Profit")
plt.xlabel("Sales")
plt.ylabel("Profit")

plt.grid()
plt.tight_layout()
plt.show()
```

### Interpretation

Look for:

- Positive relationship
- Negative relationship
- Clusters
- Outliers
- Orders with high sales but low profit

---

# 25. Visualization 11 — Quantity vs Sales

```python
plt.figure(figsize=(10, 6))

plt.scatter(
    df["Quantity"],
    df["Sales"],
    alpha=0.6
)

plt.title("Quantity vs Sales")
plt.xlabel("Quantity")
plt.ylabel("Sales")

plt.grid()
plt.tight_layout()
plt.show()
```

### Business Question

Does selling more units generally result in higher sales?

---

# 26. Add a Trend Line

NumPy can calculate a simple linear trend.

```python
x = df["Sales"]
y = df["Profit"]

valid = x.notna() & y.notna()

x = x[valid]
y = y[valid]

slope, intercept = np.polyfit(x, y, 1)

trend = slope * x + intercept
```

Plot:

```python
plt.figure(figsize=(10, 6))

plt.scatter(x, y, alpha=0.5)

plt.plot(
    x,
    trend,
    linewidth=2,
    label="Trend Line"
)

plt.title("Sales vs Profit with Trend Line")
plt.xlabel("Sales")
plt.ylabel("Profit")
plt.legend()
plt.grid()

plt.tight_layout()
plt.show()
```

### Interpretation

The trend line helps us understand the overall direction of the relationship.

---

# 27. Visualization 12 — Customer Segment Sales Share

### Business Question

Which customer segment contributes the most sales?

```python
segment_sales = (
    df.groupby("CustomerSegment")["Sales"]
      .sum()
      .sort_values(ascending=False)
)

segment_sales
```

Create a pie chart:

```python
plt.figure(figsize=(8, 8))

plt.pie(
    segment_sales.values,
    labels=segment_sales.index,
    autopct="%1.1f%%",
    startangle=90
)

plt.title("Sales Share by Customer Segment")

plt.show()
```

### Important

Pie charts should be used when:

- There are relatively few categories
- The main question is share/proportion

Do not use pie charts for dozens of categories.

---

# 28. Visualization 13 — Sales by Region

```python
region_sales = (
    df.groupby("Region")["Sales"]
      .sum()
      .sort_values(ascending=False)
)

plt.figure(figsize=(10, 6))

plt.bar(
    region_sales.index,
    region_sales.values
)

plt.title("Sales by Region")
plt.xlabel("Region")
plt.ylabel("Total Sales")

plt.tight_layout()
plt.show()
```

### Business Question

Which region contributes the most revenue?

---

# 29. Visualization 14 — Salesperson Performance

```python
salesperson_sales = (
    df.groupby("Salesperson")["Sales"]
      .sum()
      .sort_values(ascending=False)
)

plt.figure(figsize=(10, 6))

plt.bar(
    salesperson_sales.index,
    salesperson_sales.values
)

plt.title("Sales by Salesperson")
plt.xlabel("Salesperson")
plt.ylabel("Total Sales")

plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

---

# 30. Visualization 15 — Salesperson Sales vs Profit

```python
salesperson_summary = (
    df.groupby("Salesperson")
      .agg(
          Sales=("Sales", "sum"),
          Profit=("Profit", "sum")
      )
      .sort_values("Sales", ascending=False)
)

salesperson_summary
```

Create grouped bars:

```python
x = np.arange(len(salesperson_summary))
width = 0.35

plt.figure(figsize=(12, 6))

plt.bar(
    x - width / 2,
    salesperson_summary["Sales"],
    width,
    label="Sales"
)

plt.bar(
    x + width / 2,
    salesperson_summary["Profit"],
    width,
    label="Profit"
)

plt.title("Sales vs Profit by Salesperson")
plt.xlabel("Salesperson")
plt.ylabel("Amount")

plt.xticks(
    x,
    salesperson_summary.index,
    rotation=45
)

plt.legend()
plt.tight_layout()
plt.show()
```

### Business Question

Who generates high sales and also maintains strong profitability?

---

# 31. Subplots

Subplots allow several related charts to be displayed together.

```python
fig, axes = plt.subplots(
    2,
    2,
    figsize=(14, 10)
)
```

---

# 32. Business Overview — 2 × 2 Visualization

```python
fig, axes = plt.subplots(
    2,
    2,
    figsize=(14, 10)
)

# 1. Category Sales
axes[0, 0].bar(
    category_sales.index,
    category_sales.values
)
axes[0, 0].set_title("Sales by Category")
axes[0, 0].tick_params(axis="x", rotation=45)

# 2. Monthly Sales
axes[0, 1].plot(
    monthly_sales.index,
    monthly_sales.values,
    marker="o"
)
axes[0, 1].set_title("Monthly Sales Trend")
axes[0, 1].tick_params(axis="x", rotation=45)

# 3. Sales Distribution
axes[1, 0].hist(
    df["Sales"].dropna(),
    bins=20
)
axes[1, 0].set_title("Sales Distribution")

# 4. Sales vs Profit
axes[1, 1].scatter(
    df["Sales"],
    df["Profit"],
    alpha=0.5
)
axes[1, 1].set_title("Sales vs Profit")
axes[1, 1].set_xlabel("Sales")
axes[1, 1].set_ylabel("Profit")

plt.tight_layout()
plt.show()
```

This creates a simple business overview dashboard.

---

# 33. Professional Chart Formatting

A good visualization should normally include:

```python
plt.figure(figsize=(10, 6))

plt.title("Meaningful Business Title")

plt.xlabel("X Axis")
plt.ylabel("Y Axis")

plt.legend()

plt.grid()

plt.xticks(rotation=45)

plt.tight_layout()

plt.show()
```

### Important Formatting Commands

```python
figsize=(10, 6)
```

Controls chart size.

```python
plt.title()
```

Adds chart title.

```python
plt.xlabel()
```

Adds X-axis label.

```python
plt.ylabel()
```

Adds Y-axis label.

```python
plt.legend()
```

Explains multiple series.

```python
plt.grid()
```

Improves readability.

```python
plt.xticks(rotation=45)
```

Rotates crowded labels.

```python
plt.tight_layout()
```

Prevents labels from being cut off.

---

# 34. Annotate the Highest Month

Find the highest sales month:

```python
best_month = monthly_sales.idxmax()
best_sales = monthly_sales.max()

best_month, best_sales
```

Create the chart:

```python
plt.figure(figsize=(12, 6))

plt.plot(
    monthly_sales.index,
    monthly_sales.values,
    marker="o"
)

plt.annotate(
    f"Highest: {best_month}\n{best_sales:,.0f}",
    xy=(best_month, best_sales),
    xytext=(20, 20),
    textcoords="offset points",
    arrowprops=dict(arrowstyle="->")
)

plt.title("Monthly Sales Trend")
plt.xlabel("Month")
plt.ylabel("Sales")

plt.xticks(rotation=45)
plt.grid()
plt.tight_layout()
plt.show()
```

This demonstrates how to highlight an important business finding.

---

# 35. Save a Visualization

Charts can be saved as PNG files.

```python
plt.figure(figsize=(10, 6))

plt.bar(
    category_sales.index,
    category_sales.values
)

plt.title("Sales by Category")
plt.xlabel("Category")
plt.ylabel("Total Sales")

plt.tight_layout()

plt.savefig(
    "sales_by_category.png",
    dpi=300,
    bbox_inches="tight"
)

plt.show()
```

### Important

`dpi=300` provides high-resolution output suitable for reports and presentations.

---

# 36. Chart Selection Exercise

For each business question, select the most suitable visualization.

### Question 1

Which region has the highest sales?

**Answer:** Bar chart

### Question 2

How are sales changing month by month?

**Answer:** Line chart

### Question 3

What is the distribution of order values?

**Answer:** Histogram

### Question 4

Are there unusual sales values?

**Answer:** Box plot

### Question 5

Is there a relationship between sales and profit?

**Answer:** Scatter plot

### Question 6

Which products are the top 10?

**Answer:** Horizontal bar chart

### Question 7

What percentage of sales comes from each customer segment?

**Answer:** Pie chart

---

# 37. Business Interpretation

A Data Analyst should not stop after creating a chart.

The analyst must answer:

> What does this visualization tell the business?

Example:

### Observation

The Technology category has the highest total sales.

### Business Interpretation

Technology is currently the strongest revenue-generating category and may deserve additional inventory, marketing, and sales attention.

### Avoid

"The blue bar is high."

That is a visual observation, not a business insight.

---

# 38. EDA Questions

Answer these using Python.

### Q1
How many rows and columns are in the dataset?

### Q2
Which columns contain missing values?

### Q3
Which category has the highest sales?

### Q4
Which region has the highest sales?

### Q5
What is the highest-selling product?

### Q6
Which month has the highest sales?

### Q7
Which month has the highest profit?

### Q8
Are there negative-profit orders?

### Q9
Are there sales outliers?

### Q10
Does sales appear to have a positive relationship with profit?

---

# 39. Day 6 Visualization Challenge

Create these 10 visualizations without copying the previous code directly.

## Chart 1

Sales by Category

**Chart:** Bar

## Chart 2

Top 10 Products

**Chart:** Horizontal Bar

## Chart 3

Sales by Region

**Chart:** Bar

## Chart 4

Sales by Salesperson

**Chart:** Bar

## Chart 5

Monthly Sales Trend

**Chart:** Line

## Chart 6

Monthly Profit Trend

**Chart:** Line

## Chart 7

Sales Distribution

**Chart:** Histogram

## Chart 8

Sales Outliers

**Chart:** Box Plot

## Chart 9

Sales vs Profit

**Chart:** Scatter

## Chart 10

Customer Segment Sales Share

**Chart:** Pie

---

# 40. Mini Project — Retail Sales Exploratory Data Analysis & Visualization

## Project Objective

Analyze the retail sales dataset and create a complete visualization report using Python.

The final notebook should contain:

1. Data loading
2. Data inspection
3. Data quality checks
4. Summary statistics
5. Business calculations
6. Visualizations
7. Business insights

---

# 41. Required Project Visualizations

Create at least:

### 1. Sales by Category

Bar chart.

### 2. Top 10 Products

Horizontal bar chart.

### 3. Sales by Region

Bar chart.

### 4. Sales by Salesperson

Bar chart.

### 5. Monthly Sales

Line chart.

### 6. Monthly Profit

Line chart.

### 7. Sales Distribution

Histogram.

### 8. Sales Outliers

Box plot.

### 9. Sales vs Profit

Scatter plot.

### 10. Customer Segment Share

Pie chart.

---

# 42. Final Visualization Report

Your notebook should contain a section:

```text
## Business Insights
```

Write at least 8 insights.

Example structure:

```text
1. The highest-selling category is ______.
2. The lowest-selling category is ______.
3. The top product by sales is ______.
4. The strongest region is ______.
5. The highest-sales month is ______.
6. The highest-profit month is ______.
7. Sales contain ______ potential outliers.
8. Sales and profit show ______ relationship.
```

Students must fill these using their actual analysis.

---

# 43. Visualization Quality Checklist

Before submitting the notebook, verify:

- [ ] Correct chart type selected
- [ ] Chart has a meaningful title
- [ ] X-axis has a label
- [ ] Y-axis has a label
- [ ] Long category labels are readable
- [ ] Legend is included when necessary
- [ ] Grid is used when useful
- [ ] `tight_layout()` is used
- [ ] No unnecessary decoration
- [ ] Values are understandable
- [ ] Business interpretation is written below the chart
- [ ] Charts are not misleading
- [ ] Charts answer a business question

---

# 44. Common Visualization Mistakes

## Mistake 1 — Wrong Chart Type

Do not use a pie chart for 20 categories.

Use a bar chart instead.

---

## Mistake 2 — No Title

A chart without a title forces the reader to guess what it represents.

---

## Mistake 3 — Missing Axis Labels

Always explain what the X and Y axes represent.

---

## Mistake 4 — Too Many Categories

If a bar chart has too many categories, show the Top N.

Example:

```python
.head(10)
```

---

## Mistake 5 — Ignoring Outliers

Outliers may represent:

- Data-entry errors
- Special transactions
- Large customers
- High-value orders

Always investigate them.

---

## Mistake 6 — No Business Interpretation

A Data Analyst should explain what the chart means for the business.

---

# 45. Important Matplotlib Commands

```python
plt.figure()
plt.bar()
plt.barh()
plt.plot()
plt.hist()
plt.boxplot()
plt.scatter()
plt.pie()
plt.subplots()
plt.title()
plt.xlabel()
plt.ylabel()
plt.legend()
plt.grid()
plt.xticks()
plt.annotate()
plt.savefig()
plt.tight_layout()
plt.show()
```

---

# 46. Pandas → Matplotlib Workflow

A common Data Analytics workflow is:

```text
Raw Dataset
     ↓
Pandas
     ↓
Clean Data
     ↓
Group / Aggregate
     ↓
Business Metric
     ↓
Matplotlib
     ↓
Visualization
     ↓
Business Insight
```

Example:

```python
category_sales = (
    df.groupby("Category")["Sales"]
      .sum()
      .sort_values(ascending=False)
)
```

Then:

```python
plt.bar(
    category_sales.index,
    category_sales.values
)

plt.show()
```

This is one of the most important workflows for a Data Analyst.

---

# 47. Day 6 Learning Flow

```text
EDA
 ↓
Understand Dataset
 ↓
Choose Business Question
 ↓
Select Correct Chart
 ↓
Group / Aggregate Data
 ↓
Create Visualization
 ↓
Format Chart
 ↓
Interpret Result
 ↓
Write Business Insight
```

---

# 48. Instructor Teaching Pattern

For each visualization, follow this pattern:

### Step 1 — Ask a Business Question

Example:

> Which category generates the highest sales?

### Step 2 — Prepare the Data

```python
category_sales = df.groupby("Category")["Sales"].sum()
```

### Step 3 — Select the Chart

Bar chart.

### Step 4 — Create the Chart

```python
plt.bar(...)
```

### Step 5 — Format

Add:

- Title
- Axis labels
- Grid if useful
- Rotation if necessary
- `tight_layout()`

### Step 6 — Interpret

Ask students:

> What does this chart tell the business?

This pattern should be repeated for every visualization.

---

# 49. Day 6 Final Practice

Students should independently create:

```text
1. Category Sales Bar Chart
2. Top Products Horizontal Bar Chart
3. Region Sales Bar Chart
4. Salesperson Performance Bar Chart
5. Monthly Sales Line Chart
6. Monthly Profit Line Chart
7. Sales Histogram
8. Sales Box Plot
9. Sales vs Profit Scatter Plot
10. Customer Segment Pie Chart
```

Then write:

```text
8 Business Insights
```

based on their results.

---

# 50. Day 7 Preview — Business Dashboard & Reporting

Day 7 will move from individual charts to business reporting.

Topics:

1. KPI cards
2. Business dashboard structure
3. Combining multiple visualizations
4. Executive-level reporting
5. Dashboard storytelling
6. Selecting important KPIs
7. Building a final business report
8. Presenting insights to stakeholders

---

# 51. Final Takeaway

Day 6 is not only about learning Matplotlib syntax.

The main Data Analytics skill is:

> **Business Question → Data Analysis → Correct Visualization → Business Insight**

A professional Data Analyst should be able to explain:

```text
What happened?
Why did it happen?
What does the visualization show?
What is important?
What should the business investigate next?
```

---

# End of Day 6

**Next:** Day 7 — Business Dashboard & Reporting
