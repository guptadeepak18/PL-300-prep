# Domain 2: Model the Data (25–30%)

> **Exam Weight:** 25–30% | **Estimated Study Time:** 2 weeks

This domain covers designing a data model, writing DAX formulas, and optimising model performance. DAX (Data Analysis Expressions) is the most technically demanding part of the PL-300 exam.

---

## Table of Contents

1. [Design and Implement a Data Model](#1-design-and-implement-a-data-model)
2. [Create Model Calculations Using DAX](#2-create-model-calculations-using-dax)
3. [Optimize Model Performance](#3-optimize-model-performance)
4. [Common Exam Pitfalls](#4-common-exam-pitfalls)
5. [Key Terms Glossary](#5-key-terms-glossary)

---

## 1. Design and Implement a Data Model

### 1.1 Table and Column Properties

After loading data, configure properties in the **Model view** or **Data view**:

**Table Properties:**
- **Name** — rename for clarity (e.g., "DimProduct" → "Products")
- **Description** — shown in tooltips; use for documentation
- **Hidden** — hide from report view (useful for bridge/staging tables)
- **Storage Mode** — Import, DirectQuery, or Dual per-table

**Column Properties:**
- **Data Type** — set correctly (impacts storage and available operations)
- **Format** — display format (currency symbol, decimal places, date format)
- **Data Category** — critical for maps and time intelligence:

| Data Category | Column Use |
|---|---|
| Address, City, Country/Region, Continent, State or Province, Postal Code | Geographic columns for map visuals |
| Latitude, Longitude | Precise geographic coordinates |
| Web URL | Enables clickable hyperlinks in tables |
| Image URL | Renders images in table/matrix visuals |
| Barcode | Mobile scanning feature |

- **Summarize By** — default aggregation: Sum, Average, Min, Max, Count, Don't Summarize
- **Sort by Column** — sort one column by another (e.g., sort "MonthName" by "MonthNumber")
- **Hidden** — hide columns that are only used in relationships or DAX

> **Exam Tip:** Always set "Sort by Column" on month names and day names so they sort chronologically, not alphabetically.

### 1.2 Role-Playing Dimensions

A **role-playing dimension** is a single dimension table used multiple times in the fact table with different meanings.

**Example:** A Sales fact table has OrderDate, ShipDate, and DueDate — all referencing the same Date dimension.

**Implementation:**
1. Create **one active relationship** (typically to OrderDate)
2. Create **inactive relationships** to ShipDate and DueDate
3. Use **USERELATIONSHIP()** in DAX measures to activate an inactive relationship

```dax
// Measure using the active relationship (OrderDate)
Total Sales = SUM(Sales[SalesAmount])

// Measure using inactive relationship (ShipDate)
Shipped Sales =
CALCULATE(
    SUM(Sales[SalesAmount]),
    USERELATIONSHIP(Sales[ShipDate], 'Date'[Date])
)

// Measure using inactive relationship (DueDate)
Due Sales =
CALCULATE(
    SUM(Sales[SalesAmount]),
    USERELATIONSHIP(Sales[DueDate], 'Date'[Date])
)
```

> **Exam Tip:** Power BI allows only one **active** relationship between any two tables. All other relationships must be inactive.

### 1.3 Relationship Cardinality

| Cardinality | Symbol | Description | Example |
|---|---|---|---|
| **One-to-Many (1:M)** | 1 → * | Most common; dimension to fact | Products (1) → Sales (*) |
| **Many-to-One (M:1)** | * → 1 | Same as 1:M, different direction | Sales (*) → Products (1) |
| **One-to-One (1:1)** | 1 → 1 | Each row in A matches exactly one in B | Employee → EmployeeDetails |
| **Many-to-Many (M:M)** | * → * | Rows in both sides repeat | Students ↔ Courses |

**Many-to-Many Relationships:**
- Power BI supports M:M natively (since 2018)
- Typically resolved with a **bridge table**:
  - StudentCourses bridge: StudentID + CourseID
  - Students (1) → StudentCourses (*) ← Courses (1)
- Direct M:M relationships are valid but can cause ambiguity; use with caution
- Requires **bidirectional cross-filter** to work in most cases

### 1.4 Cross-Filter Direction

Controls how filters propagate across relationships:

| Direction | Behaviour | Use Case |
|---|---|---|
| **Single** | Filter flows from "one" side to "many" side | Standard star schema (recommended) |
| **Both (Bidirectional)** | Filters flow in both directions | M:M scenarios, counting across join |

> **Exam Tip:** Bidirectional cross-filtering can cause **ambiguity** and **circular dependency** errors. Use it only when necessary. In most star schemas, single-direction is correct and faster.

**When to Use Bidirectional:**
- M:M relationships via a bridge table
- When you need to filter fact tables from multiple dimensions that connect via a shared table
- Role-level security where the security table is on the "many" side

### 1.5 Building a Date Table

A proper Date (Calendar) table is required for time intelligence DAX functions.

**Requirements for a Date Table:**
1. Must contain a column of **Date data type** with no gaps (every date in range)
2. Must cover the **entire date range** of your fact tables
3. Must be **marked as a Date Table** in Power BI

**Mark as Date Table:**
- In Model view or Data view: right-click the table → **Mark as date table**
- Select the date column when prompted
- This allows Power BI to disable its built-in auto date/time tables for this column

**Creating a Date Table in DAX (Calculated Table):**
```dax
DateTable =
VAR StartDate = DATE(2020, 1, 1)
VAR EndDate = DATE(2025, 12, 31)
RETURN
ADDCOLUMNS(
    CALENDAR(StartDate, EndDate),
    "Year", YEAR([Date]),
    "QuarterNumber", QUARTER([Date]),
    "Quarter", "Q" & FORMAT([Date], "Q"),
    "MonthNumber", MONTH([Date]),
    "MonthName", FORMAT([Date], "MMMM"),
    "MonthShort", FORMAT([Date], "MMM"),
    "WeekNumber", WEEKNUM([Date]),
    "DayOfWeek", WEEKDAY([Date], 2),
    "DayName", FORMAT([Date], "dddd"),
    "IsWeekend", IF(WEEKDAY([Date], 2) > 5, TRUE(), FALSE()),
    "FiscalYear", IF(MONTH([Date]) >= 7, YEAR([Date]) + 1, YEAR([Date])),
    "FiscalQuarter", SWITCH(QUARTER([Date]), 1, "FQ3", 2, "FQ4", 3, "FQ1", 4, "FQ2")
)
```

> **Best Practice:** Disable **Auto date/time** globally (**File → Options → Data Load → uncheck "Auto date/time"**) and use your own Date table instead.

### 1.6 Calculated Columns vs Measures vs Calculated Tables

This is one of the most important concepts to understand for the exam.

| Feature | Calculated Column | Measure | Calculated Table |
|---|---|---|---|
| **Evaluated** | Row by row at refresh time | On demand when visual is rendered |  At refresh time |
| **Context** | Row context | Filter context | N/A |
| **Storage** | Stored in model (uses memory) | Not stored (computed at query time) | Stored in model |
| **Can use** | Column values in same row | Aggregations across filtered data | DAX table functions |
| **Available in** | Filter pane, slicers, rows | Values well of visuals | Model as a table |
| **Performance** | Increases model size | Preferred for aggregations | Increases model size |

**Calculated Column Example:**
```dax
// In the Sales table — computed per row
Profit = Sales[SalesAmount] - Sales[TotalCost]

// Concatenate columns
Full Name = Customers[FirstName] & " " & Customers[LastName]

// Using RELATED to bring in values from related table
ProductCategory = RELATED(Products[Category])
```

**Measure Example:**
```dax
// Aggregates based on current filter context
Total Sales = SUM(Sales[SalesAmount])

// Complex measure with filter modification
Margin % =
DIVIDE(
    SUM(Sales[SalesAmount]) - SUM(Sales[TotalCost]),
    SUM(Sales[SalesAmount]),
    0
)
```

> **Exam Tip:** Use **measures** for aggregations (Sum, Average, Count). Use **calculated columns** only for row-level attributes used in slicers or row-level logic.

---

## 2. Create Model Calculations Using DAX

### 2.1 DAX Fundamentals

DAX (Data Analysis Expressions) is the formula language for Power BI, Analysis Services, and Power Pivot.

**Key DAX Concepts:**
- **Filter Context** — the set of filters applied to the data (from slicers, rows, columns, report-level filters)
- **Row Context** — the current row being evaluated (only in calculated columns and iterators)
- **Context Transition** — CALCULATE converts row context to filter context

**DAX Syntax Rules:**
```dax
// Column reference: TableName[ColumnName]
// Measure reference: [MeasureName] (no table prefix needed but allowed)
// String values: "text"
// Comments: single-line (//) or multi-line (/* ... */)
// Variables: VAR ... RETURN
```

### 2.2 Aggregation Functions

```dax
// Basic aggregations
Total Sales = SUM(Sales[SalesAmount])
Avg Price = AVERAGE(Products[UnitPrice])
Max Qty = MAX(Sales[Quantity])
Min Qty = MIN(Sales[Quantity])
Order Count = COUNT(Sales[OrderID])          -- counts non-blank values
Row Count = COUNTROWS(Sales)                 -- counts all rows
Distinct Customers = DISTINCTCOUNT(Sales[CustomerID])

// Conditional count
Orders Over 1000 = COUNTIF is not in DAX — use:
Orders Over 1000 =
CALCULATE(COUNTROWS(Sales), Sales[SalesAmount] > 1000)
```

### 2.3 CALCULATE — The Most Important DAX Function

`CALCULATE` evaluates an expression in a **modified filter context**. It is the foundation of most advanced DAX measures.

**Syntax:**
```dax
CALCULATE(<expression>, <filter1>, <filter2>, ...)
```

**Examples:**
```dax
// Sales for a specific category (absolute filter)
Electronics Sales =
CALCULATE(
    SUM(Sales[SalesAmount]),
    Products[Category] = "Electronics"
)

// Remove all filters on a column (ALL)
All Product Sales =
CALCULATE(
    SUM(Sales[SalesAmount]),
    ALL(Products[Category])
)

// Remove all filters on the entire table
All Sales =
CALCULATE(
    SUM(Sales[SalesAmount]),
    ALL(Sales)
)

// % of Total (using ALL)
Sales % of Total =
DIVIDE(
    SUM(Sales[SalesAmount]),
    CALCULATE(SUM(Sales[SalesAmount]), ALL(Sales)),
    0
)

// Sales for current and previous year combined (ALLEXCEPT)
Category Sales =
CALCULATE(
    SUM(Sales[SalesAmount]),
    ALLEXCEPT(Products, Products[Category])
)

// Filter using KEEPFILTERS (adds to existing filter rather than replacing)
High Value Sales =
CALCULATE(
    SUM(Sales[SalesAmount]),
    KEEPFILTERS(Sales[SalesAmount] > 500)
)
```

**CALCULATE Filter Arguments:**
| Function | Description |
|---|---|
| `ALL(table)` | Removes all filters on all columns of the table |
| `ALL(table[column])` | Removes all filters on a specific column |
| `ALLEXCEPT(table, col1, col2)` | Removes all filters except specified columns |
| `ALLSELECTED()` | Removes filters from current query but respects visual-level filters |
| `KEEPFILTERS()` | Adds filter instead of replacing existing context |
| `REMOVEFILTERS()` | Same as ALL; newer, preferred syntax |
| `USERELATIONSHIP(col1, col2)` | Activates an inactive relationship |
| `CROSSFILTER(col1, col2, dir)` | Changes cross-filter direction in expression |

### 2.4 Time Intelligence Functions

Time intelligence functions require a properly marked Date table with no date gaps.

```dax
// Year-to-date (cumulative from Jan 1 to current date)
Sales YTD = TOTALYTD(SUM(Sales[SalesAmount]), 'Date'[Date])

// Quarter-to-date
Sales QTD = TOTALQTD(SUM(Sales[SalesAmount]), 'Date'[Date])

// Month-to-date
Sales MTD = TOTALMTD(SUM(Sales[SalesAmount]), 'Date'[Date])

// Custom fiscal year YTD (fiscal year ends June 30)
Sales Fiscal YTD = TOTALYTD(SUM(Sales[SalesAmount]), 'Date'[Date], "06/30")

// Same period last year
Sales LY = CALCULATE(SUM(Sales[SalesAmount]), SAMEPERIODLASTYEAR('Date'[Date]))

// Year-over-year growth
YoY Growth =
VAR CurrentSales = SUM(Sales[SalesAmount])
VAR LastYearSales = CALCULATE(SUM(Sales[SalesAmount]), SAMEPERIODLASTYEAR('Date'[Date]))
RETURN
DIVIDE(CurrentSales - LastYearSales, LastYearSales, 0)

// Previous year
Sales PY = CALCULATE(SUM(Sales[SalesAmount]), PREVIOUSYEAR('Date'[Date]))

// Previous month
Sales PM = CALCULATE(SUM(Sales[SalesAmount]), PREVIOUSMONTH('Date'[Date]))

// DATEADD — flexible time shifting
Sales 3 Months Ago =
CALCULATE(SUM(Sales[SalesAmount]), DATEADD('Date'[Date], -3, MONTH))

Sales 1 Year Ago =
CALCULATE(SUM(Sales[SalesAmount]), DATEADD('Date'[Date], -1, YEAR))

// Last 30 days rolling
Sales Last 30 Days =
CALCULATE(
    SUM(Sales[SalesAmount]),
    DATESINPERIOD('Date'[Date], MAX('Date'[Date]), -30, DAY)
)

// Running total from start
Running Total =
CALCULATE(
    SUM(Sales[SalesAmount]),
    DATESYTD('Date'[Date])
)
```

### 2.5 Iterator Functions (X Functions)

Iterator functions evaluate an expression **row by row** and then aggregate the results.

```dax
// SUMX: sum the result of an expression for each row
Total Revenue = SUMX(Sales, Sales[Quantity] * Sales[UnitPrice])

// AVERAGEX
Avg Order Value = AVERAGEX(
    VALUES(Sales[OrderID]),
    CALCULATE(SUM(Sales[SalesAmount]))
)

// COUNTX
Non-Zero Orders = COUNTX(Sales, IF(Sales[SalesAmount] > 0, 1, BLANK()))

// RANKX: rank products by sales
Product Rank =
RANKX(
    ALL(Products[ProductName]),
    [Total Sales],
    ,
    DESC,
    Dense
)

// MAXX / MINX
Largest Order = MAXX(Sales, Sales[SalesAmount])

// FILTER: returns a filtered table (used inside other functions)
High Value Customers =
CALCULATE(
    COUNTROWS(Customers),
    FILTER(Customers, [Total Sales] > 10000)
)
```

### 2.6 Logical and Conditional Functions

```dax
// IF
Sales Category =
IF([Total Sales] > 100000, "High", IF([Total Sales] > 50000, "Medium", "Low"))

// SWITCH (cleaner than nested IF)
Quarter Label =
SWITCH(
    'Date'[MonthNumber],
    1, "Q1", 2, "Q1", 3, "Q1",
    4, "Q2", 5, "Q2", 6, "Q2",
    7, "Q3", 8, "Q3", 9, "Q3",
    "Q4"
)

// SWITCH TRUE pattern (evaluate conditions)
Sales Tier =
SWITCH(
    TRUE(),
    [Total Sales] >= 1000000, "Platinum",
    [Total Sales] >= 500000, "Gold",
    [Total Sales] >= 100000, "Silver",
    "Bronze"
)

// IFERROR and ISERROR
Safe Division = IFERROR(DIVIDE([Numerator], [Denominator]), 0)

// ISBLANK
Has Sales = IF(ISBLANK([Total Sales]), "No Sales", "Has Sales")

// COALESCE (returns first non-blank)
First Non-Blank = COALESCE([Sales2024], [Sales2023], 0)
```

### 2.7 Statistical and Analytical Functions

```dax
// MEDIAN
Median Sales = MEDIAN(Sales[SalesAmount])

// PERCENTILE
Sales 90th Percentile = PERCENTILE.INC(Sales[SalesAmount], 0.9)

// Standard deviation
Sales StdDev = STDEV.P(Sales[SalesAmount])

// Variance
Sales Variance = VAR.P(Sales[SalesAmount])

// TOPN: returns top N rows of a table
Top 5 Products =
CALCULATE(
    SUM(Sales[SalesAmount]),
    TOPN(5, ALL(Products[ProductName]), [Total Sales])
)
```

### 2.8 Semi-Additive Measures

Semi-additive measures aggregate correctly in some dimensions but not others. The classic example is **inventory balance** or **account balance** — you sum across products but take the last value across time.

```dax
// Closing balance — LASTNONBLANK (for sparse data)
Closing Balance =
CALCULATE(
    SUM(Inventory[StockLevel]),
    LASTNONBLANK('Date'[Date], CALCULATE(SUM(Inventory[StockLevel])))
)

// Opening balance — FIRSTNONBLANK
Opening Balance =
CALCULATE(
    SUM(Inventory[StockLevel]),
    FIRSTNONBLANK('Date'[Date], CALCULATE(SUM(Inventory[StockLevel])))
)

// LASTDATE
End of Period Balance =
CALCULATE(
    SUM(Accounts[Balance]),
    LASTDATE('Date'[Date])
)
```

### 2.9 Variables in DAX

Variables improve readability, performance, and debuggability.

```dax
// Variables evaluated once; result reused multiple times
YoY Growth % =
VAR CurrentYear = SUM(Sales[SalesAmount])
VAR PriorYear =
    CALCULATE(
        SUM(Sales[SalesAmount]),
        SAMEPERIODLASTYEAR('Date'[Date])
    )
VAR Growth = CurrentYear - PriorYear
RETURN
DIVIDE(Growth, PriorYear, 0)
```

> **Exam Tip:** Variables are evaluated in the **filter context** at the point they are defined, not where RETURN is. This avoids context-transition issues in complex measures.

### 2.10 Quick Measures

Quick Measures are pre-built DAX templates available via **right-click on a table → New quick measure**.

Available categories:
- Aggregate per category (Average per category)
- Filters (Filtered value)
- Time intelligence (YTD total, MoM change, Running total)
- Totals (Rolling average, Weighted average)
- Mathematical operations
- Text (Star rating)

> **Exam Tip:** Quick Measures generate DAX code you can inspect and learn from. After creation, click the measure to see the underlying DAX.

### 2.11 Calculation Groups

Calculation groups are a Premium/PPU feature that allows reusing calculation logic across multiple measures.

**Key Concepts:**
- A **Calculation Group** appears as a table in the model
- It contains **Calculation Items** (e.g., "Actual", "YTD", "LY", "vs LY")
- Any base measure (e.g., [Total Sales], [Total Cost]) automatically gets a modified version for each calculation item
- Dramatically reduces the number of measures needed

**Calculation Item Examples:**
```dax
// Calculation Item: "YTD"
CALCULATE(SELECTEDMEASURE(), DATESYTD('Date'[Date]))

// Calculation Item: "vs LY"
VAR CurrentVal = SELECTEDMEASURE()
VAR LYVal = CALCULATE(SELECTEDMEASURE(), SAMEPERIODLASTYEAR('Date'[Date]))
RETURN DIVIDE(CurrentVal - LYVal, LYVal, 0)

// Calculation Item: "Running Total"
CALCULATE(SELECTEDMEASURE(), DATESYTD('Date'[Date]))
```

---

## 3. Optimize Model Performance

### 3.1 Removing Unnecessary Data

Before optimising, reduce the model footprint:

- **Remove unused columns** — every column consumes memory
- **Remove unused tables** — especially staging/helper tables (disable load)
- **Filter rows at source** — push row filters to query folding where possible
- **Reduce date/time precision** — if you only need dates, strip time component in Power Query
- **Split date/time columns** — store Date and Time separately; link fact table to Date dimension via Date column only

```m
// Remove time component from DateTime column (Power Query)
= Table.TransformColumns(Source, {{"OrderDateTime",
    each DateTime.Date(_), type date}})
```

### 3.2 Performance Analyzer

The Performance Analyzer (**View → Performance Analyzer**) records query durations for each visual:

| Metric | Description |
|---|---|
| **DAX query** | Time spent computing the DAX measure |
| **Visual display** | Time spent rendering the visual |
| **Other** | Other overhead (network, etc.) |

**Using Performance Analyzer:**
1. Open a report page
2. Click **Start recording**
3. Click **Refresh visuals**
4. Review each visual's breakdown
5. Click **Copy query** to get the DAX query for analysis in DAX Studio

> **Exam Tip:** High **DAX query** time indicates a measure optimisation problem. High **Visual display** time indicates a rendering problem (too many data points).

### 3.3 DAX Query View

Available in Power BI Desktop (**DAX Query** view, added 2023):
- Write and execute DAX queries directly in the model
- Test measures without building visuals
- Use `EVALUATE` to run table expressions

```dax
// Test a measure in DAX Query view
EVALUATE
SUMMARIZECOLUMNS(
    'Date'[Year],
    Products[Category],
    "Total Sales", [Total Sales],
    "YTD Sales", [Sales YTD]
)
```

### 3.4 Reducing Model Granularity

- **Aggregate fact tables** in Power Query if detailed rows are never needed
- Use **Aggregations** (Pro/Premium) to create pre-aggregated tables that Power BI uses automatically for common queries while keeping the detailed table for drill-down
- **Partition large tables** using Incremental Refresh (Premium)

### 3.5 DAX Optimisation Best Practices

| Practice | Reason |
|---|---|
| Use measures instead of calculated columns | Measures don't consume storage |
| Avoid bidirectional relationships where not needed | Bidirectional = more complex filter propagation = slower |
| Avoid complex FILTER() as CALCULATE argument; prefer simple predicates | Simple predicates fold to storage engine; FILTER() goes through formula engine |
| Use DIVIDE() instead of `/` operator | DIVIDE handles division-by-zero gracefully |
| Use VAR to avoid duplicate sub-expression evaluation | Variables computed once, reused many times |
| Avoid using DISTINCT() inside CALCULATE | Use VALUES() or ALL() instead |
| Avoid referencing measures in calculated columns | Causes performance issues due to context transition |
| Minimise use of calculated columns in large tables | Each column stored as a separate, uncompressed segment |

### 3.6 VertiPaq Storage Engine Optimisation

Power BI uses the **VertiPaq** columnar storage engine for Import mode:

- Data is stored **column by column**, not row by row
- Each column is **compressed** using dictionary encoding and run-length encoding
- **Low cardinality** columns compress better (e.g., Category with 5 values compresses extremely well)
- **High cardinality** columns (e.g., free text, GUIDs) compress poorly

**Tips for VertiPaq Optimisation:**
```
- Remove high-cardinality text columns not used in analysis
- Avoid storing both a Date and a DateKey (integer) in the same table
- Use integer keys in relationships (faster than text keys)
- Sort dimension tables by the key column for better compression
```

---

## 4. Common Exam Pitfalls

| Pitfall | Correct Understanding |
|---|---|
| Using FILTER() in CALCULATE for simple conditions | Use simple predicates: `CALCULATE([Measure], Table[Col] = "Value")` |
| Forgetting USERELATIONSHIP for inactive relationships | Without it, inactive relationships are never used |
| Using calculated column when measure is appropriate | Calculated columns inflate model size |
| Not marking the Date table | Time intelligence functions won't work correctly |
| Bidirectional relationships on all relationships | Causes ambiguity and performance issues |
| Not disabling auto date/time | Creates hidden date tables for every date column; wastes memory |
| ALL() vs ALLSELECTED() confusion | ALL() ignores slicers; ALLSELECTED() respects them |
| TOTALYTD with wrong fiscal year end | Must pass custom fiscal year-end date string |

---

## 5. Key Terms Glossary

| Term | Definition |
|---|---|
| **Filter Context** | Set of filters active when a measure is evaluated |
| **Row Context** | Current row being processed (in calculated columns/iterators) |
| **Context Transition** | CALCULATE converts row context to equivalent filter context |
| **VertiPaq** | Power BI's columnar in-memory storage engine |
| **Cardinality** | Number of distinct values in a column; also describes relationship types |
| **Role-Playing Dimension** | One dimension table used multiple times with different inactive relationships |
| **Semi-Additive Measure** | Aggregates differently across time (e.g., balance uses LASTDATE not SUM) |
| **Calculation Group** | Reusable DAX logic applied to multiple measures (Premium feature) |
| **CALCULATE** | Core DAX function that evaluates expressions in modified filter contexts |
| **DAX Studio** | Free external tool for DAX query testing and performance analysis |

---

## Quick Revision Summary

- **Calculated column** = row context, stored, uses RELATED() for related tables
- **Measure** = filter context, computed on demand, preferred for aggregations
- **CALCULATE** = modifies filter context; most important DAX function
- **ALL()** = removes all filters; ignores slicers
- **ALLSELECTED()** = removes report-level filters but respects slicer selections
- **Time intelligence** requires a properly marked continuous Date table
- **TOTALYTD / TOTALQTD / TOTALMTD** = cumulative aggregations
- **SAMEPERIODLASTYEAR / DATEADD** = period comparison
- **USERELATIONSHIP** = activate an inactive relationship inside CALCULATE
- **Bidirectional** = use sparingly; can cause ambiguity
- **Performance Analyzer** = identify slow visuals and slow DAX
- **Calculation Groups** = reusable calculation logic (Premium)
- **Mark as Date Table** = required for time intelligence to work correctly
