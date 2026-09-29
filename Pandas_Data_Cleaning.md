# Pandas for Data Analytics — Day 2
## Data Cleaning & Preparation — Google Colab Beginner Lab

> **Prerequisite:** Pandas Day 1, basic Python syntax, Excel and SQL knowledge.

---

# Today's Objective

By the end of this session, you will be able to:

- Load a messy CSV dataset
- Identify missing values
- Count missing values
- Find rows containing missing values
- Fill missing values using `fillna()`
- Remove missing rows using `dropna()`
- Detect duplicate records
- Remove duplicates
- Clean text using Pandas `.str`
- Use `str.strip()`
- Use `str.upper()`
- Use `str.lower()`
- Use `str.title()`
- Replace incorrect values
- Inspect unique values
- Convert dates using `pd.to_datetime()`
- Extract year, month and day from dates
- Convert numeric data types
- Handle invalid numeric values
- Create calculated columns
- Build a simple data-quality report
- Export a cleaned dataset to CSV

---

# 1. Day 1 vs Day 2

### Day 1

We learned how to:

```text
Load Data
   ↓
Explore Data
   ↓
Select Data
   ↓
Filter Data
   ↓
Sort Data
   ↓
Calculate Statistics
   ↓
GROUP BY
```

### Day 2

Today we will learn:

```text
Messy Data
    ↓
Inspect
    ↓
Missing Values
    ↓
Duplicates
    ↓
Text Cleaning
    ↓
Date Cleaning
    ↓
Data Type Conversion
    ↓
Create Calculated Columns
    ↓
Quality Check
    ↓
Clean Dataset
```

---

# 2. What is Data Cleaning?

Real-world data is often messy.

For example:

```text
Chennai
chennai
CHENNAI
 Chennai
```

These values may represent the same city.

Another example:

```text
Cash
cash
CASH
 cash
```

Data cleaning makes these values consistent.

Common data problems include:

- Missing values
- Duplicate records
- Extra spaces
- Different capitalization
- Incorrect spellings
- Incorrect data types
- Invalid dates
- Incorrect numeric values

---

# 3. Open Google Colab

Open:

https://colab.research.google.com/

Create a new notebook.

Suggested notebook name:

```text
Pandas_Day_2_Data_Cleaning
```

Upload:

```text
customer_sales_messy.csv
```

---

# 4. Import Pandas

```python
import pandas as pd
```

---

# 5. Load Today's Dataset

```python
df = pd.read_csv("customer_sales_messy.csv")
```

Display:

```python
df
```

---

# 6. Understand Today's Dataset

Our dataset contains:

| Column | Meaning |
|---|---|
| Order_ID | Unique order number |
| Customer_Name | Customer name |
| City | Customer city |
| Product | Product purchased |
| Category | Product category |
| Quantity | Number of products |
| Price | Product price |
| Order_Date | Date of order |
| Payment_Mode | Payment method |
| Salesperson | Salesperson |
| Customer_Rating | Customer rating |

---

# 7. First Inspection

Always inspect a dataset before cleaning it.

```python
df.head()
```

```python
df.tail()
```

```python
df.shape
```

```python
df.columns
```

```python
df.info()
```

---

# 8. Ask Students: What Problems Can You See?

Before using any cleaning command, ask:

1. Are there missing values?
2. Are there duplicate rows?
3. Are city names consistent?
4. Are payment modes consistent?
5. Are there extra spaces?
6. Is the date column really a date?
7. Are numeric columns actually numeric?

---

# 9. Check Missing Values

```python
df.isnull()
```

This returns:

```text
True
False
```

Usually we want the count:

```python
df.isnull().sum()
```

---

# 10. Total Missing Values

```python
df.isnull().sum().sum()
```

This gives the total number of missing cells in the entire dataset.

---

# 11. Find Rows Having Missing Values

```python
df[df.isnull().any(axis=1)]
```

### Explanation

```text
axis=1
```

means:

> Check across columns for each row.

---

# 12. Missing Values by Column

```python
missing = df.isnull().sum()

missing
```

You can also calculate the percentage:

```python
missing_percentage = (
    df.isnull().mean() * 100
)

missing_percentage
```

---

# 13. Handling Missing Values

There are two common approaches:

```text
Missing Data
     |
     ├── Fill the value
     |
     └── Remove the row
```

---

# 14. Fill Missing Text Values

For example:

```python
df["City"] = df["City"].fillna("Unknown")
```

Check:

```python
df["City"].isnull().sum()
```

---

# 15. Fill Missing Numeric Values

Use the average price:

```python
df["Price"] = df["Price"].fillna(
    df["Price"].mean()
)
```

Check:

```python
df["Price"].isnull().sum()
```

---

# 16. Fill Missing Customer Rating

```python
df["Customer_Rating"] = df["Customer_Rating"].fillna(
    df["Customer_Rating"].mean()
)
```

---

# 17. Fill Missing Category

```python
df["Category"] = df["Category"].fillna("Unknown")
```

---

# 18. Remove Rows with Missing Values

If we want to remove rows containing missing data:

```python
df = df.dropna()
```

### Important

`dropna()` removes rows containing missing values.

Do not use this blindly in real projects.

Sometimes filling the missing value is better.

---

# 19. Remove Rows Only When One Important Column Is Missing

For example:

```python
df = df.dropna(
    subset=["Order_ID"]
)
```

This removes rows where `Order_ID` is missing.

---

# 20. Duplicate Records

Check whether rows are duplicated:

```python
df.duplicated()
```

Count duplicates:

```python
df.duplicated().sum()
```

---

# 21. Display Duplicate Rows

```python
df[df.duplicated()]
```

This lets us inspect the duplicate records before deleting them.

---

# 22. Remove Duplicate Rows

```python
df = df.drop_duplicates()
```

Check again:

```python
df.duplicated().sum()
```

Expected:

```text
0
```

---

# 23. Check Duplicate Order IDs

An Order_ID should normally be unique.

```python
df["Order_ID"].duplicated().sum()
```

Find duplicate IDs:

```python
df[df["Order_ID"].duplicated()]
```

---

# 24. Text Cleaning with `.str`

Pandas provides the `.str` accessor for string operations.

Example:

```python
df["City"].str.upper()
```

---

# 25. Convert City to Uppercase

```python
df["City"] = df["City"].str.upper()
```

Check:

```python
df["City"].unique()
```

---

# 26. Remove Extra Spaces

```python
df["City"] = df["City"].str.strip()
```

For names:

```python
df["Customer_Name"] = df["Customer_Name"].str.strip()
```

---

# 27. Convert Customer Names to Title Case

```python
df["Customer_Name"] = df["Customer_Name"].str.title()
```

Example:

```text
ARUN KUMAR
arun kumar
Arun Kumar
```

becomes:

```text
Arun Kumar
Arun Kumar
Arun Kumar
```

---

# 28. Clean Payment Mode

Use multiple string methods together:

```python
df["Payment_Mode"] = (
    df["Payment_Mode"]
    .str.strip()
    .str.title()
)
```

Now values such as:

```text
cash
CASH
 cash
Cash
```

become:

```text
Cash
```

---

# 29. Inspect Unique Values

Before replacing incorrect values, inspect them.

```python
df["City"].unique()
```

```python
df["Payment_Mode"].unique()
```

```python
df["Category"].unique()
```

---

# 30. Count Unique Values

```python
df["City"].nunique()
```

---

# 31. Replace Incorrect Values

Suppose the business wants:

```text
Bangalore
```

to become:

```text
Bengaluru
```

Use:

```python
df["City"] = df["City"].replace(
    {"Bangalore": "Bengaluru"}
)
```

---

# 32. Check City Values Again

```python
df["City"].value_counts()
```

This helps us verify the cleaning.

---

# 33. Date Cleaning

First check the datatype:

```python
df["Order_Date"].dtype
```

It may be:

```text
object
```

Convert it to a real datetime:

```python
df["Order_Date"] = pd.to_datetime(
    df["Order_Date"],
    errors="coerce"
)
```

---

# 34. Why `errors="coerce"`?

If Pandas finds an invalid date, it converts it to:

```text
NaT
```

instead of stopping the program.

`NaT` means:

> Not a Time

---

# 35. Find Invalid or Missing Dates

```python
df["Order_Date"].isnull().sum()
```

Display them:

```python
df[df["Order_Date"].isnull()]
```

---

# 36. Extract Year

```python
df["Order_Year"] = df["Order_Date"].dt.year
```

---

# 37. Extract Month

```python
df["Order_Month"] = df["Order_Date"].dt.month
```

---

# 38. Extract Month Name

```python
df["Month_Name"] = df["Order_Date"].dt.month_name()
```

---

# 39. Extract Day

```python
df["Order_Day"] = df["Order_Date"].dt.day
```

---

# 40. Extract Day Name

```python
df["Day_Name"] = df["Order_Date"].dt.day_name()
```

---

# 41. Numeric Data Type Conversion

Check datatypes:

```python
df.dtypes
```

Convert Quantity:

```python
df["Quantity"] = pd.to_numeric(
    df["Quantity"],
    errors="coerce"
)
```

Convert Price:

```python
df["Price"] = pd.to_numeric(
    df["Price"],
    errors="coerce"
)
```

---

# 42. Why `errors="coerce"`?

If a value cannot be converted to a number:

```text
abc
```

Pandas converts it to:

```text
NaN
```

This allows us to identify bad data.

---

# 43. Check Data Types Again

```python
df.dtypes
```

We want:

```text
Quantity            numeric
Price               numeric
Order_Date          datetime
```

---

# 44. Create Total Sales

Now create a calculated column:

```python
df["Total_Sales"] = (
    df["Quantity"] * df["Price"]
)
```

Display:

```python
df[
    [
        "Product",
        "Quantity",
        "Price",
        "Total_Sales"
    ]
]
```

---

# 45. Final Missing Value Check

```python
df.isnull().sum()
```

---

# 46. Final Duplicate Check

```python
df.duplicated().sum()
```

---

# 47. Final Dataset Information

```python
df.info()
```

---

# 48. Simple Data Quality Report

Run:

```python
print("Rows:", df.shape[0])
print("Columns:", df.shape[1])
print(
    "Missing Values:",
    df.isnull().sum().sum()
)
print(
    "Duplicate Rows:",
    df.duplicated().sum()
)
```

---

# 49. Mini Analysis After Cleaning

## Task 1 — Most Common City

```python
df["City"].value_counts()
```

---

## Task 2 — Total Sales by Product

```python
df.groupby("Product")["Total_Sales"].sum()
```

---

## Task 3 — Total Sales by Category

```python
df.groupby("Category")["Total_Sales"].sum()
```

---

## Task 4 — Most Common Payment Method

```python
df["Payment_Mode"].value_counts()
```

---

## Task 5 — Average Customer Rating

```python
df["Customer_Rating"].mean()
```

---

## Task 6 — Sales by City

```python
df.groupby("City")["Total_Sales"].sum().sort_values(
    ascending=False
)
```

---

# 50. Export the Clean Dataset

```python
df.to_csv(
    "customer_sales_cleaned.csv",
    index=False
)
```

---

# 51. Download the File in Google Colab

Open:

```text
Left Sidebar
     ↓
Files
     ↓
customer_sales_cleaned.csv
     ↓
Three Dots
     ↓
Download
```

---

# 52. Day 2 Assignment

Do these tasks without looking at the answers.

### Task 1

Load:

```text
customer_sales_messy.csv
```

### Task 2

Display the first 10 rows.

### Task 3

Find the number of rows and columns.

### Task 4

Check all datatypes.

### Task 5

Find missing values in every column.

### Task 6

Find the total number of missing cells.

### Task 7

Display rows containing missing values.

### Task 8

Fill missing City values with:

```text
Unknown
```

### Task 9

Fill missing Price values using the average Price.

### Task 10

Find duplicate rows.

### Task 11

Remove duplicate rows.

### Task 12

Remove extra spaces from Customer_Name.

### Task 13

Convert Customer_Name to title case.

### Task 14

Convert City to uppercase.

### Task 15

Clean Payment_Mode.

### Task 16

Display unique City values.

### Task 17

Replace:

```text
Bangalore
```

with:

```text
Bengaluru
```

### Task 18

Convert Order_Date into datetime.

### Task 19

Create:

```text
Order_Year
```

### Task 20

Create:

```text
Order_Month
```

### Task 21

Create:

```text
Month_Name
```

### Task 22

Create:

```text
Total_Sales
```

using:

```text
Quantity × Price
```

### Task 23

Find total sales by Category.

### Task 24

Find total sales by City.

### Task 25

Export the cleaned dataset as:

```text
customer_sales_cleaned.csv
```

---

# 53. Challenge Task

Create a summary table containing:

```text
City
Number of Orders
Total Sales
Average Rating
```

Hint:

```python
df.groupby("City").agg(
    Orders=("Order_ID", "count"),
    Total_Sales=("Total_Sales", "sum"),
    Average_Rating=("Customer_Rating", "mean")
)
```

---

# 54. SQL → Pandas Data Cleaning Reference

| SQL / Excel Concept | Pandas |
|---|---|
| Find NULL | `df.isnull()` |
| Count NULL | `df.isnull().sum()` |
| Remove NULL rows | `df.dropna()` |
| Fill NULL | `df.fillna()` |
| Find duplicates | `df.duplicated()` |
| Remove duplicates | `df.drop_duplicates()` |
| Uppercase | `.str.upper()` |
| Lowercase | `.str.lower()` |
| Remove spaces | `.str.strip()` |
| Title Case | `.str.title()` |
| Replace value | `.replace()` |
| Unique values | `.unique()` |
| Count unique values | `.nunique()` |
| Convert date | `pd.to_datetime()` |
| Convert numeric | `pd.to_numeric()` |

---

# 55. Important Commands to Remember

```python
import pandas as pd

df = pd.read_csv("customer_sales_messy.csv")

df.head()
df.tail()
df.shape
df.columns
df.info()
df.dtypes

df.isnull().sum()
df.isnull().sum().sum()

df.dropna()
df.fillna()

df.duplicated().sum()
df.drop_duplicates()

df["City"].str.upper()
df["City"].str.lower()
df["City"].str.strip()
df["Customer_Name"].str.title()

df["City"].unique()
df["City"].nunique()

df["Order_Date"] = pd.to_datetime(
    df["Order_Date"],
    errors="coerce"
)

df["Quantity"] = pd.to_numeric(
    df["Quantity"],
    errors="coerce"
)

df["Total_Sales"] = (
    df["Quantity"] * df["Price"]
)

df.to_csv(
    "customer_sales_cleaned.csv",
    index=False
)
```

---

# 56. Day 2 Learning Flow

```text
Load Messy Dataset
        ↓
head()
        ↓
shape
        ↓
info()
        ↓
Check Missing Values
        ↓
fillna()
        ↓
dropna()
        ↓
Check Duplicates
        ↓
drop_duplicates()
        ↓
Text Cleaning
        ↓
strip()
        ↓
upper()
        ↓
title()
        ↓
replace()
        ↓
Unique Values
        ↓
Date Conversion
        ↓
to_datetime()
        ↓
Extract Date Parts
        ↓
Numeric Conversion
        ↓
to_numeric()
        ↓
Create Total_Sales
        ↓
Final Quality Check
        ↓
Export Clean CSV
```

---

# 57. Day 1 → Day 2 → Next

```text
DAY 1
Pandas Basics
     ↓
DAY 2
Data Cleaning
     ↓
DAY 3
Data Transformation
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

---

# Next Session

Recommended Day 3 topics:

1. `apply()`
2. `lambda`
3. Conditional columns
4. `map()`
5. `replace()`
6. Binning with `pd.cut()`
7. Ranking
8. `sort_values()`
9. Advanced `groupby()`
10. `agg()`
11. Pivot tables
12. Practical business analysis
