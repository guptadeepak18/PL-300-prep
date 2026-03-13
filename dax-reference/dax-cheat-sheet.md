# DAX Function Reference — Complete Cheat Sheet

> 📋 **Purpose:** Quick-reference guide to all DAX functions commonly tested on the PL-300 exam
> 🕐 **Format:** Organized by category with syntax, descriptions, and examples

---

## Table of Contents

1. [Aggregation Functions](#1-aggregation-functions)
2. [Filter Functions](#2-filter-functions)
3. [Table Functions](#3-table-functions)
4. [Time Intelligence Functions](#4-time-intelligence-functions)
5. [Logical Functions](#5-logical-functions)
6. [Text Functions](#6-text-functions)
7. [Math Functions](#7-math-functions)
8. [Statistical Functions](#8-statistical-functions)
9. [Information Functions](#9-information-functions)
10. [Iterator Functions](#10-iterator-functions)

---

## 1. Aggregation Functions

Aggregation functions operate on a column and return a single scalar value. They are the foundation of most DAX measures.

> **Exam Tip:** Know the difference between `COUNT` (numbers only), `COUNTA` (non-blank values), and `COUNTROWS` (rows in a table). This distinction appears frequently on the PL-300.

---

### SUM

```dax
SUM(<column>)
```

- **Description:** Returns the sum of all numbers in a column.
- **Example:**

```dax
Total Sales = SUM(Sales[SalesAmount])
```

> **Exam Tip:** `SUM` only works on a column reference. To sum an expression, use `SUMX`.

---

### AVERAGE

```dax
AVERAGE(<column>)
```

- **Description:** Returns the arithmetic mean of all numbers in a column.
- **Example:**

```dax
Avg Order Value = AVERAGE(Sales[OrderTotal])
```

---

### MIN

```dax
MIN(<column>)
MIN(<expression1>, <expression2>)
```

- **Description:** Returns the smallest value in a column, or the smaller of two scalar expressions.
- **Example:**

```dax
First Sale Date = MIN(Sales[OrderDate])
```

---

### MAX

```dax
MAX(<column>)
MAX(<expression1>, <expression2>)
```

- **Description:** Returns the largest value in a column, or the larger of two scalar expressions.
- **Example:**

```dax
Latest Sale Date = MAX(Sales[OrderDate])
```

---

### COUNT

```dax
COUNT(<column>)
```

- **Description:** Counts the number of rows that contain a **number** in the specified column.
- **Example:**

```dax
Numeric Count = COUNT(Sales[Quantity])
```

> **Exam Tip:** `COUNT` only counts numeric values. Use `COUNTA` for non-blank values of any type.

---

### COUNTA

```dax
COUNTA(<column>)
```

- **Description:** Counts the number of rows that contain a **non-blank** value (any data type).
- **Example:**

```dax
Non Blank Names = COUNTA(Customer[CustomerName])
```

---

### COUNTBLANK

```dax
COUNTBLANK(<column>)
```

- **Description:** Counts the number of **blank** cells in a column.
- **Example:**

```dax
Missing Emails = COUNTBLANK(Customer[Email])
```

---

### COUNTROWS

```dax
COUNTROWS(<table>)
```

- **Description:** Counts the number of rows in a table or table expression.
- **Example:**

```dax
Total Transactions = COUNTROWS(Sales)
```

> **Exam Tip:** `COUNTROWS` takes a **table** (not a column). Combine with `FILTER` to count rows meeting a condition.

---

### DISTINCTCOUNT

```dax
DISTINCTCOUNT(<column>)
```

- **Description:** Counts the number of distinct (unique) values in a column. Includes BLANK as a value if present.
- **Example:**

```dax
Unique Customers = DISTINCTCOUNT(Sales[CustomerKey])
```

> **Exam Tip:** `DISTINCTCOUNT` counts BLANK as one distinct value. Use `DISTINCTCOUNTNOBLANK` to exclude blanks.

---

### DISTINCTCOUNTNOBLANK

```dax
DISTINCTCOUNTNOBLANK(<column>)
```

- **Description:** Counts the number of distinct values in a column, **excluding BLANK**.
- **Example:**

```dax
Active Regions = DISTINCTCOUNTNOBLANK(Store[Region])
```

---

### Aggregation Functions Summary Table

| Function | Input | Counts/Sums | Handles Blanks |
|---|---|---|---|
| **SUM** | Column | Sum of numbers | Ignores blanks |
| **AVERAGE** | Column | Arithmetic mean | Ignores blanks |
| **MIN** | Column or 2 scalars | Smallest value | Ignores blanks |
| **MAX** | Column or 2 scalars | Largest value | Ignores blanks |
| **COUNT** | Column | Numeric values only | Ignores blanks |
| **COUNTA** | Column | Non-blank (any type) | Ignores blanks |
| **COUNTBLANK** | Column | Blank cells | Counts blanks |
| **COUNTROWS** | Table | All rows | Counts all rows |
| **DISTINCTCOUNT** | Column | Unique values | Includes BLANK |
| **DISTINCTCOUNTNOBLANK** | Column | Unique non-blank | Excludes BLANK |

---

## 2. Filter Functions

Filter functions manipulate the filter context — the core concept behind DAX evaluation. Understanding these is **critical** for the PL-300.

> **Exam Tip:** `CALCULATE` is the single most important DAX function. Expect multiple questions that test your understanding of how it modifies filter context.

---

### CALCULATE

```dax
CALCULATE(<expression>, <filter1>, <filter2>, ...)
```

- **Description:** Evaluates an expression in a **modified filter context**. Each filter argument overrides or adds to the existing context.
- **Example:**

```dax
Online Sales = CALCULATE(
    SUM(Sales[SalesAmount]),
    Sales[Channel] = "Online"
)
```

> **Exam Tip:** `CALCULATE` performs **context transition** — it converts any row context into an equivalent filter context. This is why it's essential inside calculated columns and iterator functions.

---

### CALCULATETABLE

```dax
CALCULATETABLE(<table_expression>, <filter1>, <filter2>, ...)
```

- **Description:** Evaluates a **table expression** in a modified filter context. Returns a table, not a scalar.
- **Example:**

```dax
High Value Orders = CALCULATETABLE(
    Sales,
    Sales[OrderTotal] > 1000
)
```

---

### FILTER

```dax
FILTER(<table>, <expression>)
```

- **Description:** Returns a table that is a subset of the input table, containing only rows where the expression evaluates to TRUE. This is an **iterator** — it evaluates row by row.
- **Example:**

```dax
Large Orders = FILTER(Sales, Sales[Quantity] > 100)
```

> **Exam Tip:** `FILTER` is less efficient than direct `CALCULATE` filters for simple column comparisons. Use `FILTER` when you need to reference multiple columns or use a measure in the filter condition.

---

### ALL

```dax
ALL(<table_or_column>, [<column1>], [<column2>], ...)
```

- **Description:** Removes all filters from the specified table or column(s). Returns all rows regardless of filter context.
- **Example:**

```dax
% of Total = DIVIDE(
    SUM(Sales[SalesAmount]),
    CALCULATE(SUM(Sales[SalesAmount]), ALL(Sales))
)
```

> **Exam Tip:** `ALL` is used in denominators for percentage-of-total calculations. Know the difference between `ALL(Table)` (removes all filters on table) and `ALL(Table[Column])` (removes filter on one column only).

---

### ALLEXCEPT

```dax
ALLEXCEPT(<table>, <column1>, [<column2>], ...)
```

- **Description:** Removes all filters on a table **except** for the specified columns. Keeps filters on the listed columns.
- **Example:**

```dax
Category % of Region =
DIVIDE(
    SUM(Sales[SalesAmount]),
    CALCULATE(
        SUM(Sales[SalesAmount]),
        ALLEXCEPT(Sales, Sales[Region])
    )
)
```

> **Exam Tip:** `ALLEXCEPT` is the inverse of `ALL`. It preserves filters on the columns you specify and removes everything else.

---

### ALLSELECTED

```dax
ALLSELECTED([<table_or_column>])
```

- **Description:** Removes filters applied by the current visual but **keeps** external filters (slicers, page filters, report filters).
- **Example:**

```dax
% of Filtered Total = DIVIDE(
    SUM(Sales[SalesAmount]),
    CALCULATE(SUM(Sales[SalesAmount]), ALLSELECTED())
)
```

> **Exam Tip:** `ALLSELECTED` is the go-to function for "percentage of visible total" calculations. It respects slicers but ignores the visual's own grouping.

---

### REMOVEFILTERS

```dax
REMOVEFILTERS([<table_or_column>])
```

- **Description:** An alias for `ALL` when used as a `CALCULATE` filter argument. Explicitly signals the intent to clear filters.
- **Example:**

```dax
Total All Products = CALCULATE(
    SUM(Sales[SalesAmount]),
    REMOVEFILTERS(Product[Category])
)
```

---

### KEEPFILTERS

```dax
KEEPFILTERS(<expression>)
```

- **Description:** Used inside `CALCULATE` to **add** a filter condition instead of overriding existing filters. The default behavior of `CALCULATE` replaces filters on the same column.
- **Example:**

```dax
Red in Current Context = CALCULATE(
    SUM(Sales[SalesAmount]),
    KEEPFILTERS(Product[Color] = "Red")
)
```

> **Exam Tip:** Without `KEEPFILTERS`, a `CALCULATE` filter on `Product[Color]` would override any slicer selection on that column. With `KEEPFILTERS`, both the slicer and the explicit filter must be satisfied (intersection).

---

### VALUES

```dax
VALUES(<column_or_table>)
```

- **Description:** Returns a one-column table of **distinct values** from a column in the current filter context. Includes BLANK if present.
- **Example:**

```dax
Selected Year = IF(
    HASONEVALUE(DimDate[Year]),
    VALUES(DimDate[Year])
)
```

---

### HASONEVALUE

```dax
HASONEVALUE(<column>)
```

- **Description:** Returns `TRUE` if the current filter context has exactly **one** distinct value for the specified column.
- **Example:**

```dax
Dynamic Title = IF(
    HASONEVALUE(Product[Category]),
    "Sales for " & VALUES(Product[Category]),
    "Sales for All Categories"
)
```

---

### SELECTEDVALUE

```dax
SELECTEDVALUE(<column>, [<alternateResult>])
```

- **Description:** Returns the single value in a column if there is exactly one value in the current filter context; otherwise returns the alternate result (or BLANK).
- **Example:**

```dax
Current Region = SELECTEDVALUE(Store[Region], "Multiple Regions")
```

> **Exam Tip:** `SELECTEDVALUE` is a shorthand for `IF(HASONEVALUE(...), VALUES(...), <alternate>)`.

---

### ISFILTERED

```dax
ISFILTERED(<column>)
```

- **Description:** Returns `TRUE` if there is a **direct** filter on the specified column.
- **Example:**

```dax
Is Category Filtered = ISFILTERED(Product[Category])
```

---

### ISCROSSFILTERED

```dax
ISCROSSFILTERED(<column>)
```

- **Description:** Returns `TRUE` if the specified column is filtered directly **or** indirectly through a relationship.
- **Example:**

```dax
Is Product Affected = ISCROSSFILTERED(Product[ProductName])
```

> **Exam Tip:** `ISFILTERED` checks direct filters only. `ISCROSSFILTERED` includes filters propagated through relationships. A column can be cross-filtered but not directly filtered.

---

### Filter Functions Summary Table

| Function | Purpose | Returns |
|---|---|---|
| **CALCULATE** | Modify filter context for a scalar expression | Scalar |
| **CALCULATETABLE** | Modify filter context for a table expression | Table |
| **FILTER** | Row-by-row table filter (iterator) | Table |
| **ALL** | Remove all filters | Table |
| **ALLEXCEPT** | Remove all filters except specified columns | Table |
| **ALLSELECTED** | Remove visual-level filters, keep external | Table |
| **REMOVEFILTERS** | Explicit alias for ALL in CALCULATE | — |
| **KEEPFILTERS** | Intersect rather than override filters | — |
| **VALUES** | Distinct values in current context | Table |
| **HASONEVALUE** | Check if single value in context | TRUE/FALSE |
| **SELECTEDVALUE** | Return single value or alternate | Scalar |
| **ISFILTERED** | Check for direct filter | TRUE/FALSE |
| **ISCROSSFILTERED** | Check for direct or indirect filter | TRUE/FALSE |

---

## 3. Table Functions

Table functions return tables that can be used as inputs to other functions, as virtual tables in measures, or in `CALCULATE` arguments.

---

### SUMMARIZE

```dax
SUMMARIZE(<table>, <groupBy_column1>, [<groupBy_column2>], ...)
```

- **Description:** Groups a table by the specified columns and returns a summary table. Can include extension columns but this usage is **not recommended** — use `ADDCOLUMNS` + `SUMMARIZE` instead.
- **Example:**

```dax
SUMMARIZE(Sales, Product[Category], DimDate[Year])
```

> **Exam Tip:** Microsoft recommends `SUMMARIZECOLUMNS` for new measures and `ADDCOLUMNS(SUMMARIZE(...))` when you need calculated columns in a grouped table. Avoid adding calculated columns directly in `SUMMARIZE`.

---

### SUMMARIZECOLUMNS

```dax
SUMMARIZECOLUMNS(
    <groupBy_column1>, [<groupBy_column2>], ...,
    [<filterTable>], ...,
    [<name>, <expression>], ...
)
```

- **Description:** The preferred function for creating summary tables. Groups by columns, applies filters, and adds computed columns in one call.
- **Example:**

```dax
SUMMARIZECOLUMNS(
    DimDate[Year],
    Product[Category],
    "Total Sales", SUM(Sales[SalesAmount]),
    "Order Count", COUNTROWS(Sales)
)
```

> **Exam Tip:** `SUMMARIZECOLUMNS` automatically removes blank rows from results. It cannot be used inside `CALCULATE` or in a measure that uses `CALCULATE` around it.

---

### ADDCOLUMNS

```dax
ADDCOLUMNS(<table>, <name1>, <expression1>, [<name2>, <expression2>], ...)
```

- **Description:** Adds calculated columns to a table expression. Does **not** modify the original table.
- **Example:**

```dax
ADDCOLUMNS(
    SUMMARIZE(Sales, Product[Category]),
    "Category Total", CALCULATE(SUM(Sales[SalesAmount]))
)
```

---

### SELECTCOLUMNS

```dax
SELECTCOLUMNS(<table>, <name1>, <expression1>, [<name2>, <expression2>], ...)
```

- **Description:** Returns a table with only the specified columns. Similar to a SQL `SELECT`.
- **Example:**

```dax
SELECTCOLUMNS(
    Customer,
    "Name", Customer[FullName],
    "City", Customer[City]
)
```

---

### UNION

```dax
UNION(<table1>, <table2>, [<table3>], ...)
```

- **Description:** Combines rows from two or more tables. Tables must have the **same number of columns** with compatible data types. Keeps duplicates.
- **Example:**

```dax
UNION(
    SELECTCOLUMNS(Sales, "ID", Sales[CustomerKey]),
    SELECTCOLUMNS(Returns, "ID", Returns[CustomerKey])
)
```

---

### INTERSECT

```dax
INTERSECT(<table1>, <table2>)
```

- **Description:** Returns rows that exist in **both** tables.
- **Example:**

```dax
Customers Who Returned =
INTERSECT(
    VALUES(Sales[CustomerKey]),
    VALUES(Returns[CustomerKey])
)
```

---

### EXCEPT

```dax
EXCEPT(<table1>, <table2>)
```

- **Description:** Returns rows from `table1` that do **not** appear in `table2`.
- **Example:**

```dax
Customers Without Returns =
EXCEPT(
    VALUES(Sales[CustomerKey]),
    VALUES(Returns[CustomerKey])
)
```

---

### CROSSJOIN

```dax
CROSSJOIN(<table1>, <table2>, [<table3>], ...)
```

- **Description:** Returns the Cartesian product of all input tables (every combination of rows).
- **Example:**

```dax
CROSSJOIN(VALUES(DimDate[Year]), VALUES(Product[Category]))
```

---

### DATATABLE

```dax
DATATABLE(
    <name1>, <type1>,
    <name2>, <type2>, ...,
    {
        {<value1>, <value2>, ...},
        {<value1>, <value2>, ...}
    }
)
```

- **Description:** Creates a table literal with explicit data. Useful for creating static lookup tables or test data.
- **Example:**

```dax
Rating Lookup =
DATATABLE(
    "Rating", STRING,
    "MinScore", INTEGER,
    {
        {"Gold", 90},
        {"Silver", 70},
        {"Bronze", 50}
    }
)
```

---

### ROW

```dax
ROW(<name1>, <expression1>, [<name2>, <expression2>], ...)
```

- **Description:** Returns a single-row table with the specified column names and values.
- **Example:**

```dax
ROW("Total", SUM(Sales[SalesAmount]), "Count", COUNTROWS(Sales))
```

---

### TOPN

```dax
TOPN(<n_value>, <table>, <orderBy_expression>, [<order>])
```

- **Description:** Returns the top N rows of a table, sorted by the specified expression.
- **Example:**

```dax
Top 10 Products =
TOPN(
    10,
    SUMMARIZE(Sales, Product[ProductName]),
    CALCULATE(SUM(Sales[SalesAmount])),
    DESC
)
```

> **Exam Tip:** `TOPN` may return more than N rows if there are ties in the sort expression.

---

### GENERATE

```dax
GENERATE(<table1>, <table2_expression>)
```

- **Description:** Evaluates `table2_expression` for each row in `table1` and returns the cross-apply result. Rows from `table1` that produce an empty `table2` are **excluded**.
- **Example:**

```dax
GENERATE(
    VALUES(Product[Category]),
    TOPN(3, RELATEDTABLE(Sales), Sales[SalesAmount], DESC)
)
```

---

### GENERATEALL

```dax
GENERATEALL(<table1>, <table2_expression>)
```

- **Description:** Same as `GENERATE` but **includes** rows from `table1` even when `table2_expression` returns an empty table.
- **Example:**

```dax
GENERATEALL(
    VALUES(Product[Category]),
    TOPN(3, RELATEDTABLE(Sales), Sales[SalesAmount], DESC)
)
```

> **Exam Tip:** `GENERATE` drops rows with no match (like `INNER JOIN`). `GENERATEALL` keeps them (like `LEFT OUTER JOIN`).

---

## 4. Time Intelligence Functions

Time intelligence functions require a **Date table** marked as a date table in the model, with a contiguous range of dates and no duplicates.

> **Exam Tip:** For time intelligence to work correctly, the Date table must be marked using "Mark as Date Table" in Power BI. The date column must have no gaps, no duplicates, and span the full range of dates in the fact table.

---

### TOTALYTD

```dax
TOTALYTD(<expression>, <dates>, [<filter>], [<year_end_date>])
```

- **Description:** Evaluates the expression year-to-date using the specified date column.
- **Example:**

```dax
Sales YTD = TOTALYTD(SUM(Sales[SalesAmount]), DimDate[Date])
```

> **Exam Tip:** For fiscal years not ending on December 31, pass the year-end date as the fourth argument: `TOTALYTD(..., "06/30")` for a June fiscal year end.

---

### TOTALQTD

```dax
TOTALQTD(<expression>, <dates>, [<filter>])
```

- **Description:** Evaluates the expression quarter-to-date.
- **Example:**

```dax
Sales QTD = TOTALQTD(SUM(Sales[SalesAmount]), DimDate[Date])
```

---

### TOTALMTD

```dax
TOTALMTD(<expression>, <dates>, [<filter>])
```

- **Description:** Evaluates the expression month-to-date.
- **Example:**

```dax
Sales MTD = TOTALMTD(SUM(Sales[SalesAmount]), DimDate[Date])
```

---

### DATESYTD

```dax
DATESYTD(<dates>, [<year_end_date>])
```

- **Description:** Returns a table of dates from the beginning of the year to the last date in the current filter context.
- **Example:**

```dax
Sales YTD =
CALCULATE(
    SUM(Sales[SalesAmount]),
    DATESYTD(DimDate[Date])
)
```

---

### DATESQTD

```dax
DATESQTD(<dates>)
```

- **Description:** Returns a table of dates from the beginning of the quarter to the last date in the current filter context.
- **Example:**

```dax
Sales QTD =
CALCULATE(SUM(Sales[SalesAmount]), DATESQTD(DimDate[Date]))
```

---

### DATESMTD

```dax
DATESMTD(<dates>)
```

- **Description:** Returns a table of dates from the beginning of the month to the last date in the current filter context.
- **Example:**

```dax
Sales MTD =
CALCULATE(SUM(Sales[SalesAmount]), DATESMTD(DimDate[Date]))
```

---

### SAMEPERIODLASTYEAR

```dax
SAMEPERIODLASTYEAR(<dates>)
```

- **Description:** Returns a table of dates shifted back exactly **one year** from the dates in the current context.
- **Example:**

```dax
Sales PY = CALCULATE(
    SUM(Sales[SalesAmount]),
    SAMEPERIODLASTYEAR(DimDate[Date])
)
```

> **Exam Tip:** `SAMEPERIODLASTYEAR` is the most commonly tested time intelligence function. It only shifts by one year — for other periods, use `DATEADD`.

---

### PREVIOUSMONTH

```dax
PREVIOUSMONTH(<dates>)
```

- **Description:** Returns the set of dates in the **previous month** relative to the current context.
- **Example:**

```dax
Sales Prev Month = CALCULATE(
    SUM(Sales[SalesAmount]),
    PREVIOUSMONTH(DimDate[Date])
)
```

---

### PREVIOUSQUARTER

```dax
PREVIOUSQUARTER(<dates>)
```

- **Description:** Returns the set of dates in the **previous quarter**.
- **Example:**

```dax
Sales Prev Qtr = CALCULATE(
    SUM(Sales[SalesAmount]),
    PREVIOUSQUARTER(DimDate[Date])
)
```

---

### PREVIOUSYEAR

```dax
PREVIOUSYEAR(<dates>)
```

- **Description:** Returns the set of dates in the **previous year**.
- **Example:**

```dax
Sales Prev Year = CALCULATE(
    SUM(Sales[SalesAmount]),
    PREVIOUSYEAR(DimDate[Date])
)
```

---

### DATEADD

```dax
DATEADD(<dates>, <number_of_intervals>, <interval>)
```

- **Description:** Shifts dates by the specified number of intervals. Interval can be `DAY`, `MONTH`, `QUARTER`, or `YEAR`.
- **Example:**

```dax
Sales 3 Months Ago = CALCULATE(
    SUM(Sales[SalesAmount]),
    DATEADD(DimDate[Date], -3, MONTH)
)
```

> **Exam Tip:** `DATEADD` is the most flexible time-shift function. Use negative values to go back in time. `SAMEPERIODLASTYEAR` is equivalent to `DATEADD(dates, -1, YEAR)`.

---

### DATESBETWEEN

```dax
DATESBETWEEN(<dates>, <start_date>, <end_date>)
```

- **Description:** Returns a table of dates between the specified start and end dates (inclusive).
- **Example:**

```dax
Q1 Sales = CALCULATE(
    SUM(Sales[SalesAmount]),
    DATESBETWEEN(DimDate[Date], DATE(2023, 1, 1), DATE(2023, 3, 31))
)
```

---

### DATESINPERIOD

```dax
DATESINPERIOD(<dates>, <start_date>, <number_of_intervals>, <interval>)
```

- **Description:** Returns a table of dates starting from a given date and extending by the number of intervals.
- **Example:**

```dax
Rolling 12 Months = CALCULATE(
    SUM(Sales[SalesAmount]),
    DATESINPERIOD(DimDate[Date], MAX(DimDate[Date]), -12, MONTH)
)
```

> **Exam Tip:** `DATESINPERIOD` with a negative interval counts **backward** from the start date. This is the standard pattern for rolling period calculations.

---

### PARALLELPERIOD

```dax
PARALLELPERIOD(<dates>, <number_of_intervals>, <interval>)
```

- **Description:** Shifts the dates by a **full period** (complete months, quarters, or years). Unlike `DATEADD`, it always returns complete periods.
- **Example:**

```dax
Full Prior Year = CALCULATE(
    SUM(Sales[SalesAmount]),
    PARALLELPERIOD(DimDate[Date], -1, YEAR)
)
```

> **Exam Tip:** `PARALLELPERIOD` returns the **entire** shifted period. If the current context is March, `PARALLELPERIOD(..., -1, YEAR)` returns all of the previous year (Jan–Dec), not just the prior March.

---

### OPENINGBALANCEMONTH

```dax
OPENINGBALANCEMONTH(<expression>, <dates>, [<filter>])
```

- **Description:** Evaluates the expression at the **first date of the month** in the current context.
- **Example:**

```dax
Opening Inventory = OPENINGBALANCEMONTH(
    SUM(Inventory[Quantity]),
    DimDate[Date]
)
```

---

### CLOSINGBALANCEMONTH

```dax
CLOSINGBALANCEMONTH(<expression>, <dates>, [<filter>])
```

- **Description:** Evaluates the expression at the **last date of the month** in the current context.
- **Example:**

```dax
Closing Inventory = CLOSINGBALANCEMONTH(
    SUM(Inventory[Quantity]),
    DimDate[Date]
)
```

> **Exam Tip:** Opening/Closing balance functions are essential for **semi-additive measures** like inventory, account balances, and headcount. Know the variants: `OPENINGBALANCEMONTH`, `OPENINGBALANCEQUARTER`, `OPENINGBALANCEYEAR` and their closing counterparts.

---

### Time Intelligence Summary Table

| Function | Shift Type | Returns |
|---|---|---|
| **TOTALYTD / TOTALQTD / TOTALMTD** | Period-to-date | Scalar |
| **DATESYTD / DATESQTD / DATESMTD** | Period-to-date | Table of dates |
| **SAMEPERIODLASTYEAR** | −1 year | Table of dates |
| **PREVIOUSMONTH / QUARTER / YEAR** | Previous full period | Table of dates |
| **DATEADD** | Custom shift by interval | Table of dates |
| **DATESBETWEEN** | Explicit date range | Table of dates |
| **DATESINPERIOD** | From start date ± N intervals | Table of dates |
| **PARALLELPERIOD** | Full period shift | Table of dates |
| **OPENINGBALANCEMONTH** | First date of month | Scalar |
| **CLOSINGBALANCEMONTH** | Last date of month | Scalar |

---

## 5. Logical Functions

Logical functions control flow and evaluate conditions within DAX expressions.

---

### IF

```dax
IF(<logical_test>, <value_if_true>, [<value_if_false>])
```

- **Description:** Checks a condition and returns one of two values.
- **Example:**

```dax
Sales Status = IF(
    [Total Sales] > 100000,
    "Above Target",
    "Below Target"
)
```

---

### SWITCH

```dax
SWITCH(<expression>, <value1>, <result1>, ..., [<else>])
```

- **Description:** Evaluates an expression against a list of values and returns the matching result. Replaces nested `IF` statements.
- **Example:**

```dax
Region Name = SWITCH(
    Store[RegionCode],
    "NE", "Northeast",
    "SE", "Southeast",
    "MW", "Midwest",
    "W", "West",
    "Unknown"
)
```

> **Exam Tip:** Use `SWITCH(TRUE(), ...)` to evaluate multiple independent conditions — a common pattern for range-based classification.

---

### AND

```dax
AND(<logical1>, <logical2>)
```

- **Description:** Returns TRUE if **both** conditions are true. The `&&` operator is equivalent.
- **Example:**

```dax
High Value VIP = AND(Sales[Amount] > 1000, Customer[Tier] = "VIP")
```

---

### OR

```dax
OR(<logical1>, <logical2>)
```

- **Description:** Returns TRUE if **either** condition is true. The `||` operator is equivalent.
- **Example:**

```dax
Flagged = OR(Sales[IsReturn] = TRUE(), Sales[Amount] < 0)
```

---

### NOT

```dax
NOT(<logical>)
```

- **Description:** Reverses the logical value of its argument.
- **Example:**

```dax
Is Not Blank = NOT(ISBLANK(Customer[Email]))
```

---

### TRUE / FALSE

```dax
TRUE()
FALSE()
```

- **Description:** Return the Boolean values TRUE or FALSE. Used in comparisons and as explicit return values.
- **Example:**

```dax
Always True Column = TRUE()
```

---

### IFERROR

```dax
IFERROR(<value>, <value_if_error>)
```

- **Description:** Returns `value` if it is not an error; otherwise returns `value_if_error`.
- **Example:**

```dax
Safe Ratio = IFERROR(Sales[Revenue] / Sales[Cost], 0)
```

> **Exam Tip:** Prefer `DIVIDE` over manual division with `IFERROR` for divide-by-zero scenarios. `DIVIDE` is more efficient and purpose-built.

---

### COALESCE

```dax
COALESCE(<expression1>, <expression2>, ...)
```

- **Description:** Returns the first non-BLANK expression from a list of arguments. Evaluates left to right and stops at the first non-blank value.
- **Example:**

```dax
Display Name = COALESCE(Customer[Nickname], Customer[FirstName], "Guest")
```

> **Exam Tip:** `COALESCE` is ideal for fallback values. It replaces verbose `IF(ISBLANK(...), ...)` patterns.

---

## 6. Text Functions

Text functions manipulate and format string values in DAX.

---

### CONCATENATE

```dax
CONCATENATE(<text1>, <text2>)
```

- **Description:** Joins two text strings into one. Only takes **two** arguments — use `&` operator or `CONCATENATEX` for more.
- **Example:**

```dax
Full Name = CONCATENATE(Customer[FirstName], CONCATENATE(" ", Customer[LastName]))
```

---

### CONCATENATEX

```dax
CONCATENATEX(<table>, <expression>, [<delimiter>], [<orderBy>], [<order>])
```

- **Description:** Concatenates values from a table expression, separated by a delimiter. This is an **iterator** function.
- **Example:**

```dax
Product List = CONCATENATEX(
    VALUES(Product[ProductName]),
    Product[ProductName],
    ", ",
    Product[ProductName], ASC
)
```

> **Exam Tip:** `CONCATENATEX` is the standard way to create comma-separated lists from a column in DAX.

---

### FORMAT

```dax
FORMAT(<value>, <format_string>)
```

- **Description:** Converts a value to text using a format string. Returns a **text** value (not a number).
- **Example:**

```dax
Formatted Date = FORMAT(Sales[OrderDate], "MMMM DD, YYYY")
Formatted Sales = FORMAT([Total Sales], "$#,##0.00")
```

> **Exam Tip:** `FORMAT` returns text, not a number. This means the result **cannot** be used in arithmetic. Use it only for display purposes in card visuals or dynamic titles.

---

### LEFT

```dax
LEFT(<text>, <num_chars>)
```

- **Description:** Returns the specified number of characters from the start of a text string.
- **Example:**

```dax
Area Code = LEFT(Customer[Phone], 3)
```

---

### RIGHT

```dax
RIGHT(<text>, <num_chars>)
```

- **Description:** Returns the specified number of characters from the end of a text string.
- **Example:**

```dax
Last Four = RIGHT(Customer[AccountNumber], 4)
```

---

### MID

```dax
MID(<text>, <start_position>, <num_chars>)
```

- **Description:** Returns a substring starting at the given position.
- **Example:**

```dax
Middle Chars = MID(Product[SKU], 4, 5)
```

---

### LEN

```dax
LEN(<text>)
```

- **Description:** Returns the number of characters in a text string.
- **Example:**

```dax
Name Length = LEN(Customer[FullName])
```

---

### UPPER / LOWER

```dax
UPPER(<text>)
LOWER(<text>)
```

- **Description:** Converts text to all uppercase or all lowercase.
- **Example:**

```dax
Uppercase Code = UPPER(Product[SKU])
```

---

### TRIM

```dax
TRIM(<text>)
```

- **Description:** Removes leading and trailing spaces from a text string.
- **Example:**

```dax
Clean Name = TRIM(Customer[FullName])
```

---

### SUBSTITUTE

```dax
SUBSTITUTE(<text>, <old_text>, <new_text>, [<instance_num>])
```

- **Description:** Replaces occurrences of a substring with a new string.
- **Example:**

```dax
Clean Phone = SUBSTITUTE(Customer[Phone], "-", "")
```

---

### SEARCH

```dax
SEARCH(<find_text>, <within_text>, [<start_position>], [<not_found_value>])
```

- **Description:** Returns the position of a substring within text. **Case-insensitive**. Returns the alternate value if not found.
- **Example:**

```dax
Has Email Domain = IF(
    SEARCH("@company.com", Customer[Email], 1, 0) > 0,
    "Internal",
    "External"
)
```

---

### FIND

```dax
FIND(<find_text>, <within_text>, [<start_position>], [<not_found_value>])
```

- **Description:** Same as `SEARCH` but **case-sensitive**.
- **Example:**

```dax
Position = FIND("ABC", Product[SKU], 1, -1)
```

> **Exam Tip:** `SEARCH` is case-insensitive and supports wildcards. `FIND` is case-sensitive and does not support wildcards.

---

### CONTAINSSTRING

```dax
CONTAINSSTRING(<within_text>, <find_text>)
```

- **Description:** Returns TRUE if one string contains another. **Case-insensitive**.
- **Example:**

```dax
Is Manager = CONTAINSSTRING(Employee[JobTitle], "Manager")
```

---

## 7. Math Functions

Math functions perform arithmetic operations and rounding.

---

### DIVIDE

```dax
DIVIDE(<numerator>, <denominator>, [<alternateResult>])
```

- **Description:** Performs safe division. Returns the alternate result (default BLANK) when the denominator is zero or BLANK.
- **Example:**

```dax
Margin % = DIVIDE(
    [Profit],
    [Revenue],
    0
)
```

> **Exam Tip:** Always use `DIVIDE` instead of the `/` operator to avoid divide-by-zero errors. This is a best practice that appears frequently on the exam.

---

### INT

```dax
INT(<number>)
```

- **Description:** Rounds a number **down** to the nearest integer.
- **Example:**

```dax
Whole Units = INT(Sales[Quantity])
```

---

### ROUND / ROUNDUP / ROUNDDOWN

```dax
ROUND(<number>, <num_digits>)
ROUNDUP(<number>, <num_digits>)
ROUNDDOWN(<number>, <num_digits>)
```

- **Description:** Rounds a number to the specified number of digits. `ROUND` uses standard rounding, `ROUNDUP` always rounds away from zero, `ROUNDDOWN` always rounds toward zero.
- **Example:**

```dax
Rounded Price = ROUND(Product[Price], 2)
Ceiling Price = ROUNDUP(Product[Price], 0)
```

---

### ABS

```dax
ABS(<number>)
```

- **Description:** Returns the absolute (non-negative) value of a number.
- **Example:**

```dax
Absolute Variance = ABS([Actual] - [Budget])
```

---

### SIGN

```dax
SIGN(<number>)
```

- **Description:** Returns +1, 0, or -1 indicating the sign of a number.
- **Example:**

```dax
Trend Direction = SIGN([Current Month] - [Previous Month])
```

---

### POWER

```dax
POWER(<number>, <power>)
```

- **Description:** Raises a number to a power.
- **Example:**

```dax
Squared = POWER(Metrics[Value], 2)
```

---

### SQRT

```dax
SQRT(<number>)
```

- **Description:** Returns the square root of a number.
- **Example:**

```dax
Root Value = SQRT(Metrics[Variance])
```

---

### MOD

```dax
MOD(<number>, <divisor>)
```

- **Description:** Returns the remainder after dividing a number by a divisor.
- **Example:**

```dax
Is Even = IF(MOD(Sales[RowNumber], 2) = 0, "Even", "Odd")
```

---

### CURRENCY

```dax
CURRENCY(<value>)
```

- **Description:** Converts a value to the Currency data type (fixed decimal number with 4 decimal places).
- **Example:**

```dax
Price as Currency = CURRENCY(Product[RawPrice])
```

---

## 8. Statistical Functions

Statistical functions perform calculations like ranking, percentiles, and distributions.

---

### RANKX

```dax
RANKX(<table>, <expression>, [<value>], [<order>], [<ties>])
```

- **Description:** Returns the rank of a value in a table evaluated against an expression.
- **Example:**

```dax
Product Rank = RANKX(
    ALL(Product[ProductName]),
    [Total Sales],
    ,
    DESC,
    Dense
)
```

> **Exam Tip:** The `<ties>` parameter controls how tied values are handled: `Skip` (default — 1,2,2,4) or `Dense` (1,2,2,3). Know the difference for the exam.

---

### PERCENTILE.INC

```dax
PERCENTILE.INC(<column>, <k>)
```

- **Description:** Returns the k-th percentile of values in a column (inclusive — k ranges from 0 to 1).
- **Example:**

```dax
90th Percentile Sales = PERCENTILE.INC(Sales[SalesAmount], 0.9)
```

---

### PERCENTILE.EXC

```dax
PERCENTILE.EXC(<column>, <k>)
```

- **Description:** Returns the k-th percentile (exclusive — k ranges from 1/(n+1) to n/(n+1)).
- **Example:**

```dax
95th Percentile = PERCENTILE.EXC(Sales[SalesAmount], 0.95)
```

---

### MEDIAN

```dax
MEDIAN(<column>)
```

- **Description:** Returns the median (middle value) of a column.
- **Example:**

```dax
Median Order Value = MEDIAN(Sales[OrderTotal])
```

---

### STDEV.P / STDEV.S

```dax
STDEV.P(<column>)
STDEV.S(<column>)
```

- **Description:** Returns the standard deviation. `.P` is for an entire **population**; `.S` is for a **sample**.
- **Example:**

```dax
Sales Std Dev (Pop) = STDEV.P(Sales[SalesAmount])
Sales Std Dev (Sample) = STDEV.S(Sales[SalesAmount])
```

---

### VAR.P / VAR.S

```dax
VAR.P(<column>)
VAR.S(<column>)
```

- **Description:** Returns the variance. `.P` is for an entire **population**; `.S` is for a **sample**.
- **Example:**

```dax
Sales Variance = VAR.P(Sales[SalesAmount])
```

> **Exam Tip:** Use `.P` (population) when your data represents the entire dataset. Use `.S` (sample) when your data is a sample of a larger population.

---

## 9. Information Functions

Information functions test for conditions and return metadata about values, columns, or the filter context.

---

### ISBLANK

```dax
ISBLANK(<value>)
```

- **Description:** Returns TRUE if the value is BLANK.
- **Example:**

```dax
Has No Email = ISBLANK(Customer[Email])
```

> **Exam Tip:** BLANK in DAX is **not** the same as an empty string `""`. `ISBLANK("")` returns FALSE. Use `ISBLANK` to check for true DAX BLANK values.

---

### ISERROR

```dax
ISERROR(<value>)
```

- **Description:** Returns TRUE if the value is any error type.
- **Example:**

```dax
Division Check = ISERROR(Sales[Revenue] / Sales[Cost])
```

---

### ISNUMBER

```dax
ISNUMBER(<value>)
```

- **Description:** Returns TRUE if the value is a number.
- **Example:**

```dax
Is Numeric = ISNUMBER(Input[Value])
```

---

### ISTEXT

```dax
ISTEXT(<value>)
```

- **Description:** Returns TRUE if the value is text.
- **Example:**

```dax
Is Text Field = ISTEXT(Input[Value])
```

---

### USERPRINCIPALNAME

```dax
USERPRINCIPALNAME()
```

- **Description:** Returns the **User Principal Name** (UPN) of the current user. Typically an email address like `user@domain.com`.
- **Example:**

```dax
Current User = USERPRINCIPALNAME()
```

> **Exam Tip:** `USERPRINCIPALNAME` is the recommended function for **Row-Level Security (RLS)** rules. It works in Power BI Service, while `USERNAME` may return different formats depending on the environment.

---

### USERNAME

```dax
USERNAME()
```

- **Description:** Returns the username of the current user. Format varies by environment (`DOMAIN\user` on-premises, UPN in service).
- **Example:**

```dax
User Domain = USERNAME()
```

> **Exam Tip:** Prefer `USERPRINCIPALNAME()` over `USERNAME()` for RLS. `USERNAME` returns `DOMAIN\user` in some environments, which complicates security rules.

---

### Information Functions — Quick Reference

| Function | Checks For | Returns |
|---|---|---|
| **ISBLANK** | BLANK value | TRUE/FALSE |
| **ISERROR** | Any error | TRUE/FALSE |
| **ISNUMBER** | Numeric value | TRUE/FALSE |
| **ISTEXT** | Text value | TRUE/FALSE |
| **USERPRINCIPALNAME** | Current user UPN | Text (email) |
| **USERNAME** | Current user name | Text |
| **HASONEVALUE** | Single value in context | TRUE/FALSE |
| **ISFILTERED** | Direct filter on column | TRUE/FALSE |

---

## 10. Iterator Functions

Iterators evaluate an expression **row by row** across a table. They end in `X` (except `CONCATENATEX` and `RANKX` which appear in other categories as well). Iterators are essential when the calculation depends on row-level values.

> **Exam Tip:** Understand the difference between `SUM(Table[Column])` and `SUMX(Table, expression)`. `SUM` works on a single column; `SUMX` can evaluate a row-by-row expression (e.g., `Quantity * Price`).

---

### SUMX

```dax
SUMX(<table>, <expression>)
```

- **Description:** Evaluates an expression for each row in a table and returns the sum of all results.
- **Example:**

```dax
Total Revenue = SUMX(
    Sales,
    Sales[Quantity] * Sales[UnitPrice]
)
```

> **Exam Tip:** `SUMX` is the go-to function when you need to multiply columns before summing. `SUM` cannot reference two columns.

---

### AVERAGEX

```dax
AVERAGEX(<table>, <expression>)
```

- **Description:** Evaluates an expression for each row and returns the average.
- **Example:**

```dax
Avg Line Total = AVERAGEX(
    Sales,
    Sales[Quantity] * Sales[UnitPrice]
)
```

---

### MINX

```dax
MINX(<table>, <expression>)
```

- **Description:** Evaluates an expression for each row and returns the minimum value.
- **Example:**

```dax
Cheapest Line Item = MINX(Sales, Sales[Quantity] * Sales[UnitPrice])
```

---

### MAXX

```dax
MAXX(<table>, <expression>)
```

- **Description:** Evaluates an expression for each row and returns the maximum value.
- **Example:**

```dax
Most Expensive Order = MAXX(Sales, Sales[Quantity] * Sales[UnitPrice])
```

---

### COUNTX

```dax
COUNTX(<table>, <expression>)
```

- **Description:** Evaluates an expression for each row and counts the rows where the result is **not blank and is numeric**.
- **Example:**

```dax
Orders With Discount = COUNTX(
    Sales,
    IF(Sales[Discount] > 0, 1)
)
```

---

### COUNTAX

```dax
COUNTAX(<table>, <expression>)
```

- **Description:** Evaluates an expression for each row and counts the rows where the result is **not blank** (any data type).
- **Example:**

```dax
Rows With Notes = COUNTAX(Sales, Sales[Notes])
```

---

### RANKX

```dax
RANKX(<table>, <expression>, [<value>], [<order>], [<ties>])
```

- **Description:** Returns the rank of a row's value within a table. (Also listed under Statistical Functions.)
- **Example:**

```dax
Customer Rank = RANKX(
    ALL(Customer),
    [Total Sales],
    ,
    DESC,
    Dense
)
```

---

### CONCATENATEX

```dax
CONCATENATEX(<table>, <expression>, [<delimiter>])
```

- **Description:** Iterates over a table and concatenates results into a single string. (Also listed under Text Functions.)
- **Example:**

```dax
Selected Categories = CONCATENATEX(
    VALUES(Product[Category]),
    Product[Category],
    ", "
)
```

---

### PRODUCTX

```dax
PRODUCTX(<table>, <expression>)
```

- **Description:** Evaluates an expression for each row and returns the **product** (multiplication) of all results.
- **Example:**

```dax
Compound Growth = PRODUCTX(
    GrowthTable,
    1 + GrowthTable[GrowthRate]
)
```

---

### Iterator Functions Summary Table

| Function | Operation | Returns |
|---|---|---|
| **SUMX** | Sum of row-level expression | Scalar |
| **AVERAGEX** | Average of row-level expression | Scalar |
| **MINX** | Minimum of row-level expression | Scalar |
| **MAXX** | Maximum of row-level expression | Scalar |
| **COUNTX** | Count numeric non-blank results | Scalar |
| **COUNTAX** | Count non-blank results (any type) | Scalar |
| **RANKX** | Rank a value within a table | Scalar |
| **CONCATENATEX** | Concatenate row-level text | Text |
| **PRODUCTX** | Product of row-level expression | Scalar |

---

## Quick Reference — Function Count by Category

| Category | Count | Most Exam-Critical |
|---|---|---|
| Aggregation | 10 | `SUM`, `COUNTROWS`, `DISTINCTCOUNT` |
| Filter | 13 | `CALCULATE`, `ALL`, `ALLSELECTED`, `FILTER` |
| Table | 13 | `SUMMARIZECOLUMNS`, `ADDCOLUMNS`, `TOPN` |
| Time Intelligence | 16 | `TOTALYTD`, `SAMEPERIODLASTYEAR`, `DATEADD` |
| Logical | 9 | `IF`, `SWITCH`, `COALESCE` |
| Text | 14 | `FORMAT`, `CONCATENATEX` |
| Math | 11 | `DIVIDE` |
| Statistical | 8 | `RANKX`, `MEDIAN` |
| Information | 8 | `USERPRINCIPALNAME`, `ISBLANK` |
| Iterator | 9 | `SUMX`, `AVERAGEX`, `RANKX` |
| **Total** | **111** | |

> **Exam Tip:** Focus your study time on `CALCULATE`, `FILTER`, `ALL`/`ALLSELECTED`, `SUMX`, `SAMEPERIODLASTYEAR`, `TOTALYTD`, `DIVIDE`, `SWITCH`, and `RANKX`. These appear most frequently on the PL-300.
