# Excel Formula Revision Guide for Data Analytics Students

**Level:** Beginner to Intermediate  
**Purpose:** Revise important Excel formulas and data-cleaning tools for Data Analytics and Power BI preparation.

## 1. Basic Excel Formulas

Assume the dataset has:
- Column A — Employee Name
- Column B — Department
- Column C — Salary
- Column D — Sales
- Column E — Quantity
- Column F — Cost
- Column G — Profit
- Column H — Discount Percentage

| Formula | Purpose | Example |
|---|---|---|
| `SUM` | Add values | `=SUM(D2:D100)` |
| `AVERAGE` | Calculate average | `=AVERAGE(C2:C100)` |
| `MIN` | Find lowest value | `=MIN(D2:D100)` |
| `MAX` | Find highest value | `=MAX(D2:D100)` |
| `COUNT` | Count numeric cells | `=COUNT(D2:D100)` |
| `COUNTA` | Count non-empty cells | `=COUNTA(A2:A100)` |
| `COUNTBLANK` | Count blank cells | `=COUNTBLANK(A2:A100)` |
| `ROUND` | Round a number | `=ROUND(C2,2)` |

## 2. Logical Formulas

| Formula | Purpose | Example |
|---|---|---|
| `IF` | Return a result based on a condition | `=IF(C2>=30000,"High","Low")` |
| `AND` | TRUE if all conditions are true | `=AND(C2>=20000,C2<=50000)` |
| `OR` | TRUE if any condition is true | `=OR(B2="IT",B2="HR")` |
| `IFERROR` | Return a fallback result if a formula errors | `=IFERROR(D2/E2,0)` |
| `IFS` | Check multiple conditions in order | `=IFS(C2>=50000,"High",C2>=30000,"Medium",TRUE,"Low")` |

## 3. Lookup Formulas

| Formula | Purpose | Example |
|---|---|---|
| `VLOOKUP` | Look up a value vertically | `=VLOOKUP(A2,$H$2:$J$100,2,FALSE)` |
| `HLOOKUP` | Look up a value horizontally | `=HLOOKUP(B1,$H$1:$M$3,2,FALSE)` |
| `XLOOKUP` | Find a match and return a related value | `=XLOOKUP(A2,H2:H100,I2:I100,"Not Found")` |
| `INDEX` + `MATCH` | Find a position, then return a related value | `=INDEX(I2:I100,MATCH(A2,H2:H100,0))` |

**Note:** `XLOOKUP` is available in newer Excel versions, including Microsoft 365. For `VLOOKUP`, the lookup column must be the first column of the selected table range.

## 4. Text Formulas

| Formula | Purpose | Example |
|---|---|---|
| `LEFT` | Return characters from the left | `=LEFT(A2,3)` |
| `RIGHT` | Return characters from the right | `=RIGHT(A2,4)` |
| `MID` | Return characters from the middle | `=MID(A2,2,5)` |
| `LEN` | Count characters | `=LEN(A2)` |
| `TRIM` | Remove extra spaces | `=TRIM(A2)` |
| `PROPER` | Capitalize the first letter of each word | `=PROPER(A2)` |
| `UPPER` | Convert text to uppercase | `=UPPER(A2)` |
| `LOWER` | Convert text to lowercase | `=LOWER(A2)` |
| `CONCAT` | Join text values | `=CONCAT(A2,B2)` |
| `TEXTJOIN` | Join text with a delimiter | `=TEXTJOIN(" ",TRUE,A2,B2)` |
| `FIND` | Find text position; case-sensitive | `=FIND("@",A2)` |
| `SUBSTITUTE` | Replace matching text | `=SUBSTITUTE(A2,"Old","New")` |
| `TEXT` | Format a number or date as text | `=TEXT(C2,"₹#,##0")` |

## 5. Conditional Calculation Formulas

| Formula | Purpose | Example |
|---|---|---|
| `SUMIF` | Sum values matching one condition | `=SUMIF(B2:B100,"IT",C2:C100)` |
| `SUMIFS` | Sum values matching multiple conditions | `=SUMIFS(D2:D100,B2:B100,"IT",C2:C100,">30000")` |
| `COUNTIF` | Count cells matching one condition | `=COUNTIF(B2:B100,"IT")` |
| `COUNTIFS` | Count cells matching multiple conditions | `=COUNTIFS(B2:B100,"IT",C2:C100,">30000")` |
| `AVERAGEIF` | Average values matching one condition | `=AVERAGEIF(B2:B100,"IT",C2:C100)` |
| `AVERAGEIFS` | Average values matching multiple conditions | `=AVERAGEIFS(C2:C100,B2:B100,"IT",D2:D100,">10000")` |

## 6. Date and Time Formulas

| Formula | Purpose | Example |
|---|---|---|
| `TODAY` | Current date | `=TODAY()` |
| `NOW` | Current date and time | `=NOW()` |
| `DAY` | Extract day number | `=DAY(F2)` |
| `MONTH` | Extract month number | `=MONTH(F2)` |
| `YEAR` | Extract year | `=YEAR(F2)` |
| `DATE` | Create a date | `=DATE(2026,10,12)` |
| `DATEDIF` | Find difference between dates | `=DATEDIF(F2,TODAY(),"Y")` |
| `EOMONTH` | Return the last day of a month | `=EOMONTH(F2,0)` |
| `NETWORKDAYS` | Count working days between dates | `=NETWORKDAYS(F2,G2)` |

## 7. Dynamic Array Formulas

These are available in newer Excel versions, including Microsoft 365.

| Formula | Purpose | Example |
|---|---|---|
| `UNIQUE` | Return unique values | `=UNIQUE(B2:B100)` |
| `FILTER` | Return rows matching a condition | `=FILTER(A2:D100,B2:B100="IT","No Results")` |
| `SORT` | Sort a range | `=SORT(A2:D100,4,-1)` |
| `SORTBY` | Sort by another range | `=SORTBY(A2:D100,D2:D100,-1)` |
| `SEQUENCE` | Generate a sequence of numbers | `=SEQUENCE(10)` |

## 8. Business and Sales Formulas

Using the example columns listed at the beginning:

| Business calculation | Formula |
|---|---|
| Total revenue | `=SUM(D2:D100)` |
| Total quantity sold | `=SUM(E2:E100)` |
| Average sales | `=AVERAGE(D2:D100)` |
| Profit per row | `=D2-F2` |
| Profit margin | `=IFERROR(G2/D2,0)` |
| Discount amount | `=D2*H2` |
| Sales after discount | `=D2-(D2*H2)` |
| Sales target status | `=IF(D2>=100000,"Achieved","Not Achieved")` |
| Rank sales from highest to lowest | `=RANK.EQ(D2,$D$2:$D$100,0)` |

**Important:** These examples assume D is Sales/Revenue, F is Cost, G is Profit, and H is the discount rate (for example, 10%). Adjust references to match your dataset. Avoid applying a discount twice if D already contains discounted sales.

## 9. Excel Data-Cleaning Tools

These are Excel features, not formulas:
- Remove Duplicates
- Find & Replace
- Text to Columns
- Flash Fill
- Sort and Filter
- Conditional Formatting
- Data Validation
- Find and handle blank cells
- Format as Table
- PivotTables and PivotCharts

### Helpful data-cleaning examples

- Remove extra spaces: `=TRIM(A2)`
- Standardize names: `=PROPER(TRIM(A2))`
- Convert text to uppercase: `=UPPER(A2)`
- Check for blank cells: `=IF(A2="","Missing","Available")`
- Flag duplicate IDs: `=IF(COUNTIF($A$2:A2,A2)>1,"Duplicate","Unique")`

## 10. Practice Exercises

Use a practice dataset with employee or sales records.

1. Calculate total sales and average sales.
2. Find the highest and lowest salary.
3. Use `IF` to label salaries as High or Low.
4. Count employees in the IT department.
5. Use `SUMIF` to calculate total sales for the South region.
6. Use `XLOOKUP` or `VLOOKUP` to return an employee's department.
7. Use `TRIM` and `PROPER` to clean employee names.
8. Extract the month and year from an order date.
9. Use `COUNTIF` to flag duplicate order IDs.
10. Use `FILTER` to show only rows for a selected department, if supported by the Excel version.

## 11. Quick Revision Quiz

1. Which formula adds all values in a range?
2. Which formula counts cells matching a condition?
3. Which formula removes extra spaces around text?
4. Which formula retrieves a matching value from a lookup range?
5. Which formula returns the current date?
6. What is the difference between `COUNT` and `COUNTA`?
7. When would you use `SUMIFS` instead of `SUMIF`?
8. Why should you check data types before building a PivotTable or Power BI report?

### Answer Key

1. `SUM`
2. `COUNTIF`
3. `TRIM`
4. `XLOOKUP` (or `VLOOKUP` / `INDEX` + `MATCH`)
5. `TODAY`
6. `COUNT` counts numeric cells; `COUNTA` counts non-empty cells.
7. Use `SUMIFS` when more than one condition is required.
8. Incorrect data types can cause sorting, calculations, date grouping, and aggregation errors.

## Suggested Revision Order

1. Basic formulas
2. IF and logical functions
3. Lookup formulas
4. Text functions and data cleaning
5. Conditional calculations
6. Date functions
7. Dynamic arrays
8. PivotTables, charts, and dashboards

**Learning outcome:** Students should be able to calculate, clean, look up, summarize, and prepare data in Excel for practical Data Analytics assignments and Power BI reports.
