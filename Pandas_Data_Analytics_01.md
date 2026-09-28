# Pandas for Data Analytics — Google Colab Beginner Lab

## Today's Objective

By the end of this session, you will be able to:

- Import Pandas
- Create and load a DataFrame
- Read a CSV file
- Understand rows and columns
- Inspect a dataset
- Select columns
- Filter rows
- Sort data
- Create a new column
- Update values
- Find basic statistics
- Use simple `groupby()` analysis
- Export the result to CSV

> **Prerequisite:** Basic Python syntax, Excel and SQL knowledge.

---

# 1. What is Pandas?

**Pandas** is a Python library used for:

- Data cleaning
- Data manipulation
- Data analysis
- Data preparation
- Exploratory data analysis (EDA)

A simple way to remember it:

```text
Excel      → Work with tables manually
SQL        → Query data from databases
Pandas     → Analyze and manipulate data using Python
Power BI   → Visualize and report the data
```

Since you already know SQL and Excel, many Pandas operations will look familiar.

---

# 2. Open Google Colab

Open:

https://colab.research.google.com/

Create a new notebook.

Suggested notebook name:

```text
Pandas_Day_1_Basics
```

---

# 3. Import Pandas

Run:

```python
import pandas as pd
```

### Explanation

`pandas` is the library name.

`pd` is the short name (alias) normally used for Pandas.

Instead of writing:

```python
pandas.DataFrame()
```

we can write:

```python
pd.DataFrame()
```

---

# 4. Create a Simple DataFrame

Before reading a file, understand what a DataFrame looks like.

Run:

```python
data = {
    "Name": ["Arun", "Priya", "Rahul", "Divya"],
    "Department": ["IT", "HR", "IT", "Finance"],
    "Salary": [45000, 40000, 55000, 50000]
}

df = pd.DataFrame(data)

df
```

### What is a DataFrame?

A DataFrame is a table containing:

- Rows
- Columns
- Data

It is similar to an Excel worksheet.

---

# 5. Load Today's Dataset

Today's dataset is:

```text
employees_pandas_training.csv
```

Upload the CSV file to Google Colab.

In Colab:

**Left sidebar → Files → Upload**

Then run:

```python
df = pd.read_csv("employees_pandas_training.csv")
```

Display the data:

```python
df
```

---

# 6. Understand the Dataset

Our dataset contains these columns:

| Column | Meaning |
|---|---|
| Employee_ID | Unique employee number |
| Name | Employee name |
| Department | Employee department |
| City | Employee city |
| Salary | Employee salary |
| Experience_Years | Years of experience |
| Status | Active / Inactive |

---

# 7. View First 5 Rows

```python
df.head()
```

### Explanation

`head()` displays the first 5 rows by default.

You can specify the number:

```python
df.head(10)
```

This displays the first 10 rows.

---

# 8. View Last 5 Rows

```python
df.tail()
```

For example:

```python
df.tail(3)
```

This displays the last 3 rows.

---

# 9. Check Number of Rows and Columns

```python
df.shape
```

Example result:

```text
(20, 7)
```

Meaning:

```text
20 rows
7 columns
```

Important:

```python
df.shape[0]
```

Number of rows.

```python
df.shape[1]
```

Number of columns.

---

# 10. View Column Names

```python
df.columns
```

You will see:

```text
Employee_ID
Name
Department
City
Salary
Experience_Years
Status
```

---

# 11. Check Data Types

```python
df.dtypes
```

### Explanation

This tells us the type of each column.

For example:

```text
Employee_ID          int64
Name                object
Department          object
City                object
Salary               int64
Experience_Years     int64
Status              object
```

Think of this as understanding what type of data each Excel column contains.

---

# 12. Get Dataset Information

```python
df.info()
```

`info()` gives useful information about:

- Number of rows
- Column names
- Non-null values
- Data types

This is one of the first commands to run when receiving a new dataset.

---

# 13. Basic Statistical Summary

```python
df.describe()
```

This gives statistical information for numeric columns.

You will see values such as:

- Count
- Mean
- Standard deviation
- Minimum
- Maximum

For salary:

```python
df["Salary"].describe()
```

---

# 14. Select One Column

To select the Salary column:

```python
df["Salary"]
```

To select the Department column:

```python
df["Department"]
```

### SQL comparison

SQL:

```sql
SELECT Salary
FROM employees;
```

Pandas:

```python
df["Salary"]
```

---

# 15. Select Multiple Columns

```python
df[["Name", "Department", "Salary"]]
```

### Important

One column:

```python
df["Salary"]
```

Multiple columns:

```python
df[["Name", "Salary"]]
```

Notice the double square brackets for multiple columns.

---

# 16. Filter Rows

Find employees whose salary is greater than 50,000.

```python
df[df["Salary"] > 50000]
```

### SQL comparison

SQL:

```sql
SELECT *
FROM employees
WHERE Salary > 50000;
```

Pandas:

```python
df[df["Salary"] > 50000]
```

---

# 17. Filter by Department

Find IT employees:

```python
df[df["Department"] == "IT"]
```

### SQL equivalent

```sql
SELECT *
FROM employees
WHERE Department = 'IT';
```

---

# 18. Multiple Conditions

Find IT employees earning more than 50,000:

```python
df[(df["Department"] == "IT") & (df["Salary"] > 50000)]
```

### Important

Use:

```python
&
```

for AND.

Use:

```python
|
```

for OR.

Example:

```python
df[(df["Department"] == "IT") | (df["Department"] == "HR")]
```

---

# 19. Sort Data

Sort employees by salary from low to high:

```python
df.sort_values("Salary")
```

Sort salary from high to low:

```python
df.sort_values("Salary", ascending=False)
```

### SQL comparison

SQL:

```sql
SELECT *
FROM employees
ORDER BY Salary DESC;
```

Pandas:

```python
df.sort_values("Salary", ascending=False)
```

---

# 20. Find the Highest Salary

```python
df["Salary"].max()
```

Find the lowest salary:

```python
df["Salary"].min()
```

Find average salary:

```python
df["Salary"].mean()
```

Find total salary:

```python
df["Salary"].sum()
```

Find number of salary records:

```python
df["Salary"].count()
```

---

# 21. Create a New Column

Suppose we want to calculate an annual bonus of 10% of salary.

```python
df["Bonus"] = df["Salary"] * 0.10
```

Display:

```python
df[["Name", "Salary", "Bonus"]]
```

This is similar to creating a calculated column in Excel or Power BI.

---

# 22. Create Another Calculated Column

Calculate salary after adding the bonus:

```python
df["Salary_With_Bonus"] = df["Salary"] + df["Bonus"]
```

View:

```python
df[["Name", "Salary", "Bonus", "Salary_With_Bonus"]]
```

---

# 23. Update a Value

Suppose Arun's salary should be 48,000.

```python
df.loc[df["Name"] == "Arun", "Salary"] = 48000
```

Check:

```python
df[df["Name"] == "Arun"]
```

### Why use `loc`?

`loc` allows us to select rows and columns using labels or conditions.

---

# 24. Count Departments

```python
df["Department"].value_counts()
```

This tells us how many employees are in each department.

---

# 25. Count Cities

```python
df["City"].value_counts()
```

This gives the number of employees in each city.

---

# 26. GroupBy — Very Important for Data Analytics

Calculate average salary by department:

```python
df.groupby("Department")["Salary"].mean()
```

### SQL equivalent

```sql
SELECT Department, AVG(Salary)
FROM employees
GROUP BY Department;
```

### Pandas

```python
df.groupby("Department")["Salary"].mean()
```

This is one of the most important Pandas operations for Data Analysts.

---

# 27. Multiple Aggregations

Find:

- Number of employees
- Average salary
- Maximum salary
- Minimum salary

by department.

```python
df.groupby("Department")["Salary"].agg(
    ["count", "mean", "max", "min"]
)
```

---

# 28. GroupBy with Multiple Columns

Find average salary by department and city:

```python
df.groupby(["Department", "City"])["Salary"].mean()
```

This is similar to doing a multi-column `GROUP BY` in SQL.

---

# 29. Check Missing Values

```python
df.isnull().sum()
```

This tells us how many missing values exist in each column.

For today's clean dataset, the result should be zero for all columns.

---

# 30. Check Duplicate Rows

```python
df.duplicated().sum()
```

This tells us how many duplicate rows exist.

---

# 31. Remove Duplicate Rows

If duplicates exist:

```python
df = df.drop_duplicates()
```

---

# 32. Export Data to CSV

After analysis or cleaning:

```python
df.to_csv("employees_cleaned.csv", index=False)
```

The file can then be downloaded from the Colab Files panel.

---

# 33. Mini Analysis Exercise

Ask the student to solve these without looking at the answers.

### Task 1

Display only:

```text
Name
Department
Salary
```

---

### Task 2

Find employees with salary greater than 50,000.

---

### Task 3

Find employees from Chennai.

---

### Task 4

Find IT employees with more than 4 years of experience.

---

### Task 5

Sort employees by salary from highest to lowest.

---

### Task 6

Find the average salary.

---

### Task 7

Find the highest salary.

---

### Task 8

Count employees in each department.

---

### Task 9

Calculate average salary by department.

---

### Task 10

Create a new column called `Bonus` containing 10% of salary.

---

# 34. Exercise Answers

### Task 1

```python
df[["Name", "Department", "Salary"]]
```

### Task 2

```python
df[df["Salary"] > 50000]
```

### Task 3

```python
df[df["City"] == "Chennai"]
```

### Task 4

```python
df[(df["Department"] == "IT") & (df["Experience_Years"] > 4)]
```

### Task 5

```python
df.sort_values("Salary", ascending=False)
```

### Task 6

```python
df["Salary"].mean()
```

### Task 7

```python
df["Salary"].max()
```

### Task 8

```python
df["Department"].value_counts()
```

### Task 9

```python
df.groupby("Department")["Salary"].mean()
```

### Task 10

```python
df["Bonus"] = df["Salary"] * 0.10
```

---

# 35. SQL → Pandas Quick Reference

| SQL | Pandas |
|---|---|
| `SELECT *` | `df` |
| `SELECT Name` | `df["Name"]` |
| `SELECT Name, Salary` | `df[["Name", "Salary"]]` |
| `WHERE Salary > 50000` | `df[df["Salary"] > 50000]` |
| `ORDER BY Salary` | `df.sort_values("Salary")` |
| `ORDER BY Salary DESC` | `df.sort_values("Salary", ascending=False)` |
| `COUNT(*)` | `df.shape[0]` |
| `COUNT(column)` | `df["column"].count()` |
| `AVG(Salary)` | `df["Salary"].mean()` |
| `SUM(Salary)` | `df["Salary"].sum()` |
| `MAX(Salary)` | `df["Salary"].max()` |
| `MIN(Salary)` | `df["Salary"].min()` |
| `GROUP BY Department` | `df.groupby("Department")` |
| `GROUP BY + AVG` | `df.groupby("Department")["Salary"].mean()` |

---

# 36. Today's Learning Flow

```text
Import Pandas
      ↓
Create DataFrame
      ↓
Read CSV
      ↓
head()
      ↓
tail()
      ↓
shape
      ↓
columns
      ↓
dtypes
      ↓
info()
      ↓
describe()
      ↓
Select Columns
      ↓
Filter Rows
      ↓
Sort Data
      ↓
Basic Statistics
      ↓
Create Columns
      ↓
Update Values
      ↓
value_counts()
      ↓
groupby()
      ↓
Missing Values
      ↓
Duplicates
      ↓
Export CSV
```

---

# 37. Important Commands to Remember Today

```python
import pandas as pd

pd.read_csv()

df.head()
df.tail()
df.shape
df.columns
df.dtypes
df.info()
df.describe()

df["column"]
df[["column1", "column2"]]

df[df["Salary"] > 50000]

df.sort_values("Salary", ascending=False)

df["Salary"].mean()
df["Salary"].sum()
df["Salary"].min()
df["Salary"].max()
df["Salary"].count()

df["Department"].value_counts()

df.groupby("Department")["Salary"].mean()

df.isnull().sum()
df.duplicated().sum()

df.to_csv("output.csv", index=False)
```

---

# Next Session

Next, continue with **Pandas Data Cleaning**:

1. Missing values
2. `isnull()`
3. `fillna()`
4. `dropna()`
5. Duplicate records
6. String cleaning
7. `str.upper()`
8. `str.lower()`
9. `str.strip()`
10. Date columns
11. `to_datetime()`
12. Data type conversion
13. `astype()`
14. Creating a realistic messy dataset
15. Complete data-cleaning exercise

---

## Dataset

This README uses:

```text
employees_pandas_training.csv
```

Keep the CSV file in the same working directory as the notebook.
