# Day 7 — Exploratory Data Analysis (EDA)

## 1. Overview

Exploratory Data Analysis (EDA) is the process of inspecting and summarizing a dataset to understand its structure, quality, patterns, relationships, and unusual values before making conclusions.

**Tools:** Python, Pandas, NumPy, Matplotlib, and Seaborn  
**Environment:** Google Colab  
**Dataset:** `day7_eda_practice_dataset.csv`

## 2. Learning Outcomes

By the end of this notebook-style exercise, you will be able to:

- Load a CSV file and inspect its structure.
- Summarize numerical and categorical columns.
- Find missing values and duplicate records.
- Identify inconsistent categories and suspicious values.
- Explore distributions, outliers, trends, and relationships.
- Write evidence-based observations from data.

## 3. Dataset Description

The dataset contains fictional sales records. It intentionally includes some data-quality issues so they can be discovered during analysis.

| Column | Description |
|---|---|
| `Order_ID` | Unique order reference expected for each record |
| `Order_Date` | Date of the order |
| `Region` | Sales region |
| `Category` | Product category |
| `Product` | Product name |
| `Quantity` | Number of units |
| `Unit_Price` | Price per unit |
| `Discount` | Discount rate, stored as a decimal |
| `Sales` | Recorded sales amount |
| `Cost` | Recorded cost |
| `Profit` | Recorded profit |
| `Customer_ID` | Customer reference |
| `Sales_Channel` | Online, Store, or Partner |
| `Customer_Rating` | Customer rating, expected to be between 1 and 5 |

## 4. Open Google Colab and Load the Dataset

1. Open [Google Colab](https://colab.research.google.com/).
2. Create a new notebook.
3. Upload `day7_eda_practice_dataset.csv` using the Files panel, or run the upload code below.

```python
from google.colab import files
uploaded = files.upload()
```

Choose `day7_eda_practice_dataset.csv`.

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

sns.set_theme(style="whitegrid")

df = pd.read_csv("day7_eda_practice_dataset.csv")
```

If the CSV has already been uploaded to the Colab session, skip the upload step.

## 5. Understand the Dataset Structure

### 5.1 Display the first five records

```python
df.head()
```

### 5.2 Display the last five records

```python
df.tail()
```

### 5.3 Check the number of rows and columns

```python
df.shape
```

**Expected:** `(180, 14)` before any rows are removed.

### 5.4 Inspect column names

```python
df.columns.tolist()
```

### 5.5 Check data types and non-null counts

```python
df.info()
```

### 5.6 Review numerical summaries

```python
df.describe()
```

### 5.7 Review categorical summaries

```python
df.describe(include="object")
```

## 6. Check Data Quality

### 6.1 Missing values by column

```python
missing_count = df.isna().sum()
missing_percent = (df.isna().mean() * 100).round(2)

missing_summary = pd.DataFrame({
    "Missing_Count": missing_count,
    "Missing_Percent": missing_percent
}).sort_values("Missing_Count", ascending=False)

missing_summary
```

**Interpretation:** A missing value is not automatically an error. Determine why the value is missing and whether it is important to the analysis before deciding how to handle it.

### 6.2 Duplicate records

```python
df.duplicated().sum()
```

View duplicate rows:

```python
df[df.duplicated(keep=False)]
```

Do not delete duplicates automatically. Confirm whether they are accidental duplicates or legitimate repeated transactions.

### 6.3 Unique values in categorical columns

```python
categorical_cols = [
    "Region", "Category", "Product", "Sales_Channel"
]

for col in categorical_cols:
    print(f"\n{col}")
    print(df[col].value_counts(dropna=False))
```

Look for differences such as `North` and ` north `, or `Electronics` and `electronics`.

### 6.4 Check numerical ranges

```python
numeric_cols = [
    "Quantity", "Unit_Price", "Discount",
    "Sales", "Cost", "Profit", "Customer_Rating"
]

df[numeric_cols].agg(["min", "max", "mean", "median"])
```

Check suspicious values:

```python
df[df["Quantity"] <= 0]
```

```python
df[df["Unit_Price"] <= 0]
```

```python
df[
    (df["Discount"] < 0) |
    (df["Discount"] > 1)
]
```

```python
df[
    (df["Customer_Rating"] < 1) |
    (df["Customer_Rating"] > 5)
]
```

These checks flag values that may violate the expected business rules. Investigate them before changing anything.

### 6.5 Check date parsing

```python
df["Order_Date"] = pd.to_datetime(
    df["Order_Date"],
    errors="coerce"
)

df["Order_Date"].isna().sum()
```

## 7. Explore Numerical Distributions

### 7.1 Histogram of sales

```python
plt.figure(figsize=(9, 5))
sns.histplot(data=df, x="Sales", bins=20, kde=True)
plt.title("Distribution of Sales")
plt.xlabel("Sales")
plt.ylabel("Number of Orders")
plt.tight_layout()
plt.show()
```

Questions:
- Are most order values low, medium, or high?
- Is the distribution skewed?
- Are there unusually large or small values?

### 7.2 Box plot of profit

```python
plt.figure(figsize=(9, 5))
sns.boxplot(data=df, x="Profit")
plt.title("Distribution of Profit")
plt.xlabel("Profit")
plt.tight_layout()
plt.show()
```

A box plot can highlight possible outliers. A flagged point is not necessarily an error; it may represent a genuine business event.

### 7.3 Summary statistics

```python
df["Sales"].agg(["count", "mean", "median", "std", "min", "max"])
```

Compare mean and median. A large difference may indicate skewness or extreme observations.

## 8. Explore Categories

### 8.1 Number of orders by category

```python
plt.figure(figsize=(10, 5))
sns.countplot(data=df, x="Category", order=df["Category"].value_counts().index)
plt.title("Number of Records by Category")
plt.xlabel("Category")
plt.ylabel("Number of Records")
plt.xticks(rotation=30, ha="right")
plt.tight_layout()
plt.show()
```

### 8.2 Total sales by category

```python
category_sales = (
    df.groupby("Category", dropna=False)["Sales"]
      .sum(min_count=1)
      .sort_values(ascending=False)
)

category_sales
```

```python
category_sales.plot(kind="bar", figsize=(9, 5))
plt.title("Total Sales by Category")
plt.xlabel("Category")
plt.ylabel("Total Sales")
plt.xticks(rotation=30, ha="right")
plt.tight_layout()
plt.show()
```

**Important:** Number of records and total sales answer different questions. A category can have many orders without having the highest revenue.

### 8.3 Compare sales by region

```python
region_sales = (
    df.groupby("Region", dropna=False)["Sales"]
      .sum(min_count=1)
      .sort_values(ascending=False)
)

region_sales
```

## 9. Explore Time Trends

### 9.1 Aggregate sales by month

```python
monthly_sales = (
    df.dropna(subset=["Order_Date"])
      .set_index("Order_Date")["Sales"]
      .resample("MS")
      .sum(min_count=1)
)

monthly_sales
```

### 9.2 Plot the trend

```python
plt.figure(figsize=(12, 5))
monthly_sales.plot(marker="o")
plt.title("Monthly Sales Trend")
plt.xlabel("Month")
plt.ylabel("Total Sales")
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
```

Look for increases, decreases, peaks, and periods that deserve further investigation. A trend alone does not prove what caused the change.

## 10. Explore Relationships

### 10.1 Quantity versus sales

```python
plt.figure(figsize=(9, 5))
sns.scatterplot(data=df, x="Quantity", y="Sales")
plt.title("Quantity vs Sales")
plt.xlabel("Quantity")
plt.ylabel("Sales")
plt.tight_layout()
plt.show()
```

Look for patterns, clusters, and observations that do not follow the general pattern.

### 10.2 Sales by category

```python
plt.figure(figsize=(10, 5))
sns.boxplot(data=df, x="Category", y="Sales")
plt.title("Sales Distribution by Category")
plt.xlabel("Category")
plt.ylabel("Sales")
plt.xticks(rotation=30, ha="right")
plt.tight_layout()
plt.show()
```

### 10.3 Correlation among numerical variables

```python
numeric_df = df.select_dtypes(include="number")
correlation = numeric_df.corr()

plt.figure(figsize=(10, 7))
sns.heatmap(correlation, annot=True, fmt=".2f", cmap="coolwarm")
plt.title("Correlation Heatmap")
plt.tight_layout()
plt.show()
```

Correlation measures the strength and direction of a linear relationship. Correlation does not establish causation, and correlations can be affected by unusual values or missing data.

## 11. Optional: Create a Cleaned Copy for Further Exploration

Keep the original data unchanged. Create a separate copy and apply only clearly justified corrections.

```python
eda_df = df.copy()

# Standardize whitespace and capitalization in text columns.
for col in ["Region", "Category", "Product", "Sales_Channel"]:
    eda_df[col] = eda_df[col].astype("string").str.strip()

# Standardize known category labels.
eda_df["Region"] = eda_df["Region"].str.title()
eda_df["Category"] = eda_df["Category"].str.title()
eda_df["Sales_Channel"] = eda_df["Sales_Channel"].str.title()

eda_df.head()
```

This is a demonstration, not a complete cleaning policy. Decide how to handle missing values, invalid values, and duplicates based on business rules. Do not assume a missing sales amount is zero or remove a record solely because it looks unusual.

## 12. Write Findings from Evidence

Use this structure for each finding:

- **Observation:** What does the dataset or chart show?
- **Evidence:** Which value, summary, or visualization supports it?
- **Possible implication:** Why might it matter to the business?
- **Next question:** What additional information would help explain it?

Example format:

> **Observation:** The total sales chart shows that one category contributes more recorded sales than the others.  
> **Evidence:** Refer to the values in `category_sales`.  
> **Possible implication:** The business may want to monitor inventory and profitability for this category.  
> **Next question:** Does the category also have the highest profit?

Write conclusions from your actual output; do not copy assumptions without checking the data.

## 13. Practice Exercises

### Exercise 1 — Dataset overview
1. Display the dataset shape.
2. List all columns and their data types.
3. Identify numerical and categorical columns.

### Exercise 2 — Data quality
1. Count missing values in each column.
2. Find duplicate rows.
3. List distinct values in `Region` and `Sales_Channel`.
4. Find records with zero or negative quantity.
5. Find ratings outside the expected 1–5 range.

### Exercise 3 — Sales analysis
1. Find total recorded sales by category.
2. Find total recorded sales by region.
3. Find the five products with the highest recorded sales.
4. Compare mean and median sales.

### Exercise 4 — Visual exploration
1. Create a histogram of sales.
2. Create a box plot of profit.
3. Plot monthly sales.
4. Create a scatter plot of quantity versus sales.
5. Generate a correlation heatmap.

### Exercise 5 — Business findings
Write three findings. Each must include an observation, evidence from your output, a possible implication, and a follow-up question.

## 14. Assignment — Sales Data Investigation

Complete the following independently:

1. Load the CSV file and inspect the dataset.
2. Produce a missing-value summary.
3. Identify duplicates and inconsistent category labels.
4. Flag invalid quantity, unit-price, and rating values.
5. Create a category sales ranking.
6. Create a monthly sales trend chart.
7. Investigate the relationship between quantity and sales.
8. Generate a correlation heatmap.
9. Write five evidence-based findings.
10. Recommend two additional analyses the business should perform.

**Submission checklist**
- Python notebook (`.ipynb`)
- Three or more charts
- Data-quality summary
- Five findings supported by actual output
- Two recommendations or follow-up questions

## 15. Expected Outputs and Validation

Use these checks to validate your notebook:

| Check | Expected result |
|---|---|
| Initial dataset shape | 180 rows × 14 columns |
| Date parsing | `Order_Date` becomes a datetime column |
| Missing-value analysis | A table with count and percentage for each column |
| Duplicate analysis | At least one duplicate record is detected |
| Invalid-value checks | Suspicious quantity, unit price, and rating records are flagged |
| Category analysis | A ranked series of total sales by category |
| Monthly analysis | A time-indexed monthly sales series and line chart |
| Relationship analysis | A quantity-versus-sales scatter plot |
| Correlation analysis | A numerical correlation matrix and heatmap |

Exact sales totals and rankings should be taken from the CSV and calculated by your code. Data cleaning choices can change results, so record any exclusions or corrections.

## 16. Completion

At the end of this exercise, the notebook should contain:
- Dataset overview
- Data-quality investigation
- Numerical and categorical exploration
- Time-series analysis
- Relationship analysis
- Visualizations
- Evidence-based findings
- Completed assignment

**Day 7 — Exploratory Data Analysis (EDA) complete.**
