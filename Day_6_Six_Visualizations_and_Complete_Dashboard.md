# Day 6 — Six Visualizations and Complete Sales Dashboard

## 1. Load the dataset

```python
import pandas as pd
import matplotlib.pyplot as plt

# Load dataset
df = pd.read_csv("day4_sales_analysis_merged.csv")

# Clean column names and prepare data types
df.columns = df.columns.str.strip()
df["Order_Date"] = pd.to_datetime(df["Order_Date"], errors="coerce")

numeric_columns = ["Quantity", "Net_Sales", "Profit"]
for column in numeric_columns:
    df[column] = pd.to_numeric(df[column], errors="coerce")

# Remove rows missing fields required for the visualizations
df = df.dropna(
    subset=["Order_Date", "Quantity", "Net_Sales", "Profit",
            "Category", "Region", "Payment_Mode"]
)

print("Dataset shape:", df.shape)
display(df.head())
```

## 2. Diagram 1 — Net Sales by Category

```python
category_sales = (
    df.groupby("Category")["Net_Sales"]
      .sum()
      .sort_values(ascending=False)
)

plt.figure(figsize=(9, 5))
category_sales.plot(kind="bar")
plt.title("Net Sales by Category")
plt.xlabel("Category")
plt.ylabel("Net Sales")
plt.xticks(rotation=35, ha="right")
plt.tight_layout()
plt.show()
```

## 3. Diagram 2 — Net Sales by Region

```python
region_sales = (
    df.groupby("Region")["Net_Sales"]
      .sum()
      .sort_values(ascending=False)
)

plt.figure(figsize=(8, 5))
region_sales.plot(kind="bar")
plt.title("Net Sales by Region")
plt.xlabel("Region")
plt.ylabel("Net Sales")
plt.xticks(rotation=0)
plt.tight_layout()
plt.show()
```

## 4. Diagram 3 — Monthly Net Sales Trend

```python
monthly_sales = (
    df.set_index("Order_Date")
      .resample("MS")["Net_Sales"]
      .sum()
)

plt.figure(figsize=(10, 5))
plt.plot(monthly_sales.index, monthly_sales.values, marker="o")
plt.title("Monthly Net Sales Trend")
plt.xlabel("Month")
plt.ylabel("Net Sales")
plt.xticks(rotation=45, ha="right")
plt.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()
```

## 5. Diagram 4 — Profit by Category

```python
category_profit = (
    df.groupby("Category")["Profit"]
      .sum()
      .sort_values(ascending=False)
)

plt.figure(figsize=(9, 5))
category_profit.plot(kind="bar")
plt.title("Profit by Category")
plt.xlabel("Category")
plt.ylabel("Profit")
plt.xticks(rotation=35, ha="right")
plt.tight_layout()
plt.show()
```

## 6. Diagram 5 — Orders by Payment Mode

```python
payment_counts = df["Payment_Mode"].value_counts()

plt.figure(figsize=(8, 6))
plt.pie(
    payment_counts.values,
    labels=payment_counts.index,
    autopct="%1.1f%%",
    startangle=90
)
plt.title("Order Count by Payment Mode")
plt.axis("equal")
plt.tight_layout()
plt.show()
```

## 7. Diagram 6 — Quantity vs Net Sales

```python
plt.figure(figsize=(8, 5))
plt.scatter(df["Quantity"], df["Net_Sales"], alpha=0.6)
plt.title("Quantity vs Net Sales")
plt.xlabel("Quantity")
plt.ylabel("Net Sales")
plt.grid(True, alpha=0.25)
plt.tight_layout()
plt.show()
```

---

## 8. Complete Sales Analytics Dashboard — All Six Charts

Run this **entire cell at once** after running the dataset-loading cell above. It creates one dashboard with six charts.

```python
import matplotlib.pyplot as plt
import pandas as pd

# Prepare summaries for the dashboard
category_sales = df.groupby("Category")["Net_Sales"].sum().sort_values(ascending=False)
region_sales = df.groupby("Region")["Net_Sales"].sum().sort_values(ascending=False)
monthly_sales = (
    df.set_index("Order_Date")
      .resample("MS")["Net_Sales"]
      .sum()
)
category_profit = df.groupby("Category")["Profit"].sum().sort_values(ascending=False)
payment_counts = df["Payment_Mode"].value_counts()

# Create dashboard layout: 2 rows x 3 columns
fig, axes = plt.subplots(2, 3, figsize=(20, 12))
fig.suptitle("SALES ANALYTICS DASHBOARD", fontsize=22, fontweight="bold")

# Chart 1: Net sales by category
category_sales.plot(kind="bar", ax=axes[0, 0])
axes[0, 0].set_title("Net Sales by Category")
axes[0, 0].set_xlabel("Category")
axes[0, 0].set_ylabel("Net Sales")
axes[0, 0].tick_params(axis="x", rotation=35)

# Chart 2: Net sales by region
region_sales.plot(kind="bar", ax=axes[0, 1])
axes[0, 1].set_title("Net Sales by Region")
axes[0, 1].set_xlabel("Region")
axes[0, 1].set_ylabel("Net Sales")
axes[0, 1].tick_params(axis="x", rotation=0)

# Chart 3: Monthly net sales trend
axes[0, 2].plot(monthly_sales.index, monthly_sales.values, marker="o")
axes[0, 2].set_title("Monthly Net Sales Trend")
axes[0, 2].set_xlabel("Month")
axes[0, 2].set_ylabel("Net Sales")
axes[0, 2].tick_params(axis="x", rotation=45)
axes[0, 2].grid(True, alpha=0.3)

# Chart 4: Profit by category
category_profit.plot(kind="bar", ax=axes[1, 0])
axes[1, 0].set_title("Profit by Category")
axes[1, 0].set_xlabel("Category")
axes[1, 0].set_ylabel("Profit")
axes[1, 0].tick_params(axis="x", rotation=35)

# Chart 5: Order count by payment mode
axes[1, 1].pie(
    payment_counts.values,
    labels=payment_counts.index,
    autopct="%1.1f%%",
    startangle=90
)
axes[1, 1].set_title("Order Count by Payment Mode")
axes[1, 1].axis("equal")

# Chart 6: Quantity vs net sales
axes[1, 2].scatter(df["Quantity"], df["Net_Sales"], alpha=0.6)
axes[1, 2].set_title("Quantity vs Net Sales")
axes[1, 2].set_xlabel("Quantity")
axes[1, 2].set_ylabel("Net Sales")
axes[1, 2].grid(True, alpha=0.25)

# Improve spacing and render the complete dashboard
plt.tight_layout(rect=[0, 0, 1, 0.95])
plt.show()
```
