# Common DAX Patterns — PL-300 Exam Reference

> 📋 **Purpose:** Ready-to-use DAX patterns organized by scenario for the PL-300 exam
> 🕐 **Format:** Complete working examples with explanations and exam tips

---

## Table of Contents

1. [Basic Measures](#1-basic-measures)
2. [Percentage Patterns](#2-percentage-patterns)
3. [Year-over-Year Patterns](#3-year-over-year-patterns)
4. [Time Intelligence Patterns](#4-time-intelligence-patterns)
5. [Ranking Patterns](#5-ranking-patterns)
6. [Cumulative Totals](#6-cumulative-totals)
7. [ABC / Pareto Analysis](#7-abc--pareto-analysis)
8. [Dynamic Measures](#8-dynamic-measures)
9. [Semi-Additive Measures](#9-semi-additive-measures)
10. [Row-Level Security](#10-row-level-security)
11. [Virtual Relationships](#11-virtual-relationships)
12. [Error Handling](#12-error-handling)
13. [Budget vs Actuals](#13-budget-vs-actuals)

---

## 1. Basic Measures

Foundational measures that serve as building blocks for more complex patterns. Every Power BI model starts here.

---

### 1.1 Total Sales

**When to use:** Primary revenue measure used across all reports and visuals.

```dax
Total Sales =
SUM(Sales[SalesAmount])
```

- **How it works:** Sums the `SalesAmount` column across all rows visible in the current filter context. As slicers and filters change, the result updates automatically.

---

### 1.2 Total Revenue (Row-Level Calculation)

**When to use:** When revenue must be calculated from quantity and unit price at the row level.

```dax
Total Revenue =
SUMX(
    Sales,
    Sales[Quantity] * Sales[UnitPrice]
)
```

- **How it works:** `SUMX` iterates over each row in the `Sales` table, multiplies `Quantity × UnitPrice` for that row, then sums all results.

> **Exam Tip:** Use `SUMX` (an iterator) when the calculation requires values from multiple columns in the same row. `SUM` can only reference a single column.

---

### 1.3 Average Order Value

**When to use:** Measure the typical order size to identify trends and segment customers.

```dax
Avg Order Value =
AVERAGEX(
    VALUES(Sales[OrderID]),
    CALCULATE(SUM(Sales[SalesAmount]))
)
```

- **How it works:** `VALUES(Sales[OrderID])` creates a distinct list of orders. `AVERAGEX` iterates over each order, calculates its total using `CALCULATE`, then returns the average across all orders.

---

### 1.4 Customer Count

**When to use:** Count unique customers who made purchases in the current context.

```dax
Customer Count =
DISTINCTCOUNT(Sales[CustomerKey])
```

- **How it works:** Counts the number of distinct `CustomerKey` values in the filtered `Sales` table. Includes BLANK as one value if present.

> **Exam Tip:** `DISTINCTCOUNT` is a shorthand for `COUNTROWS(DISTINCT(column))`. It counts BLANK as one distinct value.

---

## 2. Percentage Patterns

Percentage calculations require manipulating the filter context to create the correct denominator. These patterns are heavily tested on the PL-300.

---

### 2.1 Percentage of Grand Total (ALL)

**When to use:** Show each row's contribution to the overall total, ignoring all slicers and filters.

```dax
% of Grand Total =
DIVIDE(
    SUM(Sales[SalesAmount]),
    CALCULATE(
        SUM(Sales[SalesAmount]),
        ALL(Sales)
    )
)
```

- **How it works:** The numerator is the current context sales. The denominator uses `ALL(Sales)` inside `CALCULATE` to remove every filter on the Sales table, giving the grand total.

---

### 2.2 Percentage of Visible Total (ALLSELECTED)

**When to use:** Show each row's contribution to the currently filtered/visible total. The denominator respects slicers and page filters but ignores the visual's own grouping.

```dax
% of Filtered Total =
DIVIDE(
    SUM(Sales[SalesAmount]),
    CALCULATE(
        SUM(Sales[SalesAmount]),
        ALLSELECTED()
    )
)
```

- **How it works:** `ALLSELECTED()` removes filters applied by the current visual (row/column grouping) but keeps filters from slicers, page filters, and report filters.

> **Exam Tip:** `ALLSELECTED` is the most commonly tested function for percentage calculations. Know the difference: `ALL` = grand total denominator, `ALLSELECTED` = visible total denominator.

---

### 2.3 Percentage of Parent Category

**When to use:** In a matrix visual, show a subcategory's share within its parent category.

```dax
% of Parent Category =
DIVIDE(
    SUM(Sales[SalesAmount]),
    CALCULATE(
        SUM(Sales[SalesAmount]),
        ALLEXCEPT(Sales, Product[Category])
    )
)
```

- **How it works:** `ALLEXCEPT(Sales, Product[Category])` removes all filters on the Sales table **except** the category filter. This gives the parent category total as the denominator.

> **Exam Tip:** Use `ALLEXCEPT` when you want to keep filters on specific columns and remove everything else. This is the inverse of `ALL(column)`.

---

### 2.4 Profit Margin Percentage

**When to use:** Calculate profitability as a ratio of profit to revenue.

```dax
Profit =
SUM(Sales[SalesAmount]) - SUM(Sales[CostAmount])

Margin % =
DIVIDE(
    [Profit],
    SUM(Sales[SalesAmount]),
    0
)
```

- **How it works:** The `Profit` measure subtracts cost from sales. `Margin %` divides profit by revenue using `DIVIDE` for safe division (returns 0 if revenue is zero).

---

## 3. Year-over-Year Patterns

Year-over-year (YoY) comparisons are among the most common business intelligence requirements and are heavily tested on the PL-300.

> **Exam Tip:** All time intelligence patterns require a proper Date table marked as a date table, with a contiguous date column that has no gaps and no duplicates.

---

### 3.1 Prior Year Sales

**When to use:** Compare current period performance to the same period one year ago.

```dax
Sales PY =
CALCULATE(
    SUM(Sales[SalesAmount]),
    SAMEPERIODLASTYEAR(DimDate[Date])
)
```

- **How it works:** `SAMEPERIODLASTYEAR` shifts the current date filter back by exactly one year. Whatever dates are in the current context (a day, month, quarter, or year), the same range from the prior year is applied.

---

### 3.2 Year-over-Year Growth (Absolute)

**When to use:** Show the absolute increase or decrease from the prior year.

```dax
Sales PY =
CALCULATE(
    SUM(Sales[SalesAmount]),
    SAMEPERIODLASTYEAR(DimDate[Date])
)

YoY Growth =
[Total Sales] - [Sales PY]
```

- **How it works:** Subtracts prior year sales from current sales. A positive result means growth; negative means decline.

---

### 3.3 Year-over-Year Growth Percentage

**When to use:** Express the YoY change as a percentage for trend analysis and KPI cards.

```dax
YoY Growth % =
VAR CurrentSales = [Total Sales]
VAR PriorYearSales = [Sales PY]
RETURN
    DIVIDE(
        CurrentSales - PriorYearSales,
        PriorYearSales
    )
```

- **How it works:** Uses `VAR` to store intermediate values for readability. Divides the difference by the prior year value. Returns BLANK (not an error) if prior year is zero.

> **Exam Tip:** Use `VAR` (variables) to improve readability and performance. Variables are evaluated **once** and can be reused, avoiding redundant calculations.

---

### 3.4 Year-over-Year with DATEADD

**When to use:** When you need flexible period shifting (not just one year).

```dax
Sales Same Period LY =
CALCULATE(
    SUM(Sales[SalesAmount]),
    DATEADD(DimDate[Date], -1, YEAR)
)

Sales Same Period LQ =
CALCULATE(
    SUM(Sales[SalesAmount]),
    DATEADD(DimDate[Date], -1, QUARTER)
)
```

- **How it works:** `DATEADD` shifts dates by the specified interval. Use `-1` for the prior period, `-2` for two periods back, etc. Supports `DAY`, `MONTH`, `QUARTER`, and `YEAR`.

> **Exam Tip:** `SAMEPERIODLASTYEAR(dates)` is equivalent to `DATEADD(dates, -1, YEAR)`. Use `DATEADD` when you need to shift by months, quarters, or custom intervals.

---

## 4. Time Intelligence Patterns

Extended time intelligence patterns for period-to-date, rolling windows, and moving averages.

---

### 4.1 Year-to-Date (YTD)

**When to use:** Accumulate values from the start of the year through the current date.

```dax
Sales YTD =
TOTALYTD(
    SUM(Sales[SalesAmount]),
    DimDate[Date]
)
```

- **How it works:** `TOTALYTD` expands the date filter to include all dates from January 1 through the last date in the current context.

**Fiscal year variant (June year-end):**

```dax
Sales Fiscal YTD =
TOTALYTD(
    SUM(Sales[SalesAmount]),
    DimDate[Date],
    "06/30"
)
```

> **Exam Tip:** If the organization uses a fiscal year, pass the year-end date as the last argument. For a fiscal year ending June 30, use `"06/30"`.

---

### 4.2 Quarter-to-Date (QTD)

**When to use:** Accumulate values from the start of the current quarter.

```dax
Sales QTD =
TOTALQTD(
    SUM(Sales[SalesAmount]),
    DimDate[Date]
)
```

---

### 4.3 Month-to-Date (MTD)

**When to use:** Accumulate values from the start of the current month.

```dax
Sales MTD =
TOTALMTD(
    SUM(Sales[SalesAmount]),
    DimDate[Date]
)
```

---

### 4.4 Rolling 12 Months

**When to use:** Smooth out seasonality by showing a continuous 12-month window that shifts with the current context.

```dax
Sales Rolling 12M =
CALCULATE(
    SUM(Sales[SalesAmount]),
    DATESINPERIOD(
        DimDate[Date],
        MAX(DimDate[Date]),
        -12,
        MONTH
    )
)
```

- **How it works:** `DATESINPERIOD` starts from the last date in the current context and goes back 12 months. This creates a sliding window that always covers exactly 12 months, regardless of which month the user selects.

> **Exam Tip:** `DATESINPERIOD` with a negative interval counts backward. This is the standard pattern for rolling period calculations. The start date should use `MAX(DimDate[Date])` to anchor to the end of the current context.

---

### 4.5 3-Month Moving Average

**When to use:** Smooth short-term fluctuations to reveal underlying trends.

```dax
Sales 3M Moving Avg =
AVERAGEX(
    DATESINPERIOD(
        DimDate[Date],
        MAX(DimDate[Date]),
        -3,
        MONTH
    ),
    CALCULATE(
        DIVIDE(
            SUM(Sales[SalesAmount]),
            DISTINCTCOUNT(DimDate[MonthKey])
        )
    )
)
```

**Simpler alternative using monthly granularity:**

```dax
Sales 3M Moving Avg =
VAR Last3Months =
    DATESINPERIOD(DimDate[Date], MAX(DimDate[Date]), -3, MONTH)
RETURN
    CALCULATE(
        DIVIDE(
            SUM(Sales[SalesAmount]),
            3
        ),
        Last3Months
    )
```

- **How it works:** Calculates the total sales over the last 3 months and divides by 3 to get the monthly average. The `DATESINPERIOD` function creates the rolling 3-month window.

---

### 4.6 YTD with Prior Year Comparison

**When to use:** Compare current YTD performance to the same YTD period in the prior year.

```dax
Sales YTD =
TOTALYTD(SUM(Sales[SalesAmount]), DimDate[Date])

Sales PYTD =
CALCULATE(
    TOTALYTD(SUM(Sales[SalesAmount]), DimDate[Date]),
    SAMEPERIODLASTYEAR(DimDate[Date])
)

YTD vs PYTD % =
DIVIDE(
    [Sales YTD] - [Sales PYTD],
    [Sales PYTD]
)
```

- **How it works:** `Sales PYTD` nests `TOTALYTD` inside a `CALCULATE` with `SAMEPERIODLASTYEAR` to first shift to the prior year, then accumulate YTD within that year.

---

## 5. Ranking Patterns

Ranking patterns use `RANKX` to order items by a measure. Critical for Top N analysis and competitive positioning.

---

### 5.1 Basic Rank

**When to use:** Rank products, customers, or stores by a measure value.

```dax
Product Rank =
RANKX(
    ALL(Product[ProductName]),
    [Total Sales],
    ,
    DESC,
    Dense
)
```

- **How it works:** `ALL(Product[ProductName])` provides the full list of products regardless of filters. `RANKX` evaluates `[Total Sales]` for each product and assigns a rank. `DESC` means the highest sales get rank 1. `Dense` ensures no gaps (1,2,2,3 instead of 1,2,2,4).

> **Exam Tip:** The first argument of `RANKX` determines the ranking universe. Use `ALL(column)` for an absolute rank or `ALLSELECTED(column)` for a rank within the current filter context.

---

### 5.2 Rank with Tied Values (Skip vs Dense)

**When to use:** Control how ties are displayed.

```dax
-- Skip ranking: 1, 2, 2, 4 (default)
Rank Skip =
RANKX(ALL(Product[ProductName]), [Total Sales], , DESC, Skip)

-- Dense ranking: 1, 2, 2, 3
Rank Dense =
RANKX(ALL(Product[ProductName]), [Total Sales], , DESC, Dense)
```

- **How it works:** `Skip` leaves gaps after ties (Olympic medal style). `Dense` has no gaps — the next unique value gets the next consecutive rank.

---

### 5.3 Top N Filtering

**When to use:** Show only the top N items in a visual or calculation.

```dax
Top 10 Product Sales =
SUMX(
    TOPN(
        10,
        VALUES(Product[ProductName]),
        [Total Sales],
        DESC
    ),
    [Total Sales]
)
```

- **How it works:** `TOPN` returns the top 10 products by sales. `SUMX` then sums the `[Total Sales]` for each of those products.

---

### 5.4 Dynamic Top N with "Other"

**When to use:** Show the Top N items and group everything else into an "Other" category.

```dax
Is Top 5 =
VAR CurrentProduct = SELECTEDVALUE(Product[ProductName])
VAR ProductRank =
    RANKX(
        ALL(Product[ProductName]),
        [Total Sales],
        ,
        DESC,
        Dense
    )
RETURN
    IF(ProductRank <= 5, CurrentProduct, "Other")
```

- **How it works:** Calculates the rank for the current product. If it's in the top 5, it shows the product name; otherwise, it labels it "Other." Use this as a grouping field in visuals.

> **Exam Tip:** Dynamic Top N with "Other" grouping is a common scenario question. Know how to combine `RANKX` with `IF` to create dynamic grouping.

---

## 6. Cumulative Totals

Running totals accumulate values over a sorted dimension (typically dates).

---

### 6.1 Running Total (Date-Based)

**When to use:** Show how a measure accumulates over time within a year or across all time.

```dax
Running Total =
CALCULATE(
    SUM(Sales[SalesAmount]),
    FILTER(
        ALL(DimDate[Date]),
        DimDate[Date] <= MAX(DimDate[Date])
    )
)
```

- **How it works:** `ALL(DimDate[Date])` removes the date filter to see all dates. `FILTER` then keeps only dates up to the current date in context. The result is a running sum from the earliest date to the current one.

> **Exam Tip:** Running totals use `FILTER` with `ALL` to create a custom date range. The `<=` condition accumulates all values up to the current row's date.

---

### 6.2 Running Total within Year

**When to use:** Reset the running total at the start of each year.

```dax
Running Total YTD =
CALCULATE(
    SUM(Sales[SalesAmount]),
    FILTER(
        ALL(DimDate[Date]),
        DimDate[Date] <= MAX(DimDate[Date])
            && YEAR(DimDate[Date]) = YEAR(MAX(DimDate[Date]))
    )
)
```

- **How it works:** Adds a year condition to the filter so the accumulation resets at each year boundary.

---

### 6.3 Running Average

**When to use:** Show the average of all values up to the current point.

```dax
Running Average =
VAR CurrentDate = MAX(DimDate[Date])
VAR RunningDates =
    FILTER(
        ALL(DimDate[Date]),
        DimDate[Date] <= CurrentDate
    )
VAR RunningTotal =
    CALCULATE(SUM(Sales[SalesAmount]), RunningDates)
VAR DayCount =
    CALCULATE(
        DISTINCTCOUNT(Sales[OrderDate]),
        RunningDates
    )
RETURN
    DIVIDE(RunningTotal, DayCount)
```

- **How it works:** Calculates both the running total and the count of distinct selling days, then divides to get the average. Uses `VAR` for clarity and to avoid recalculating the running dates filter.

---

## 7. ABC / Pareto Analysis

ABC analysis classifies items into categories (A, B, C) based on their cumulative contribution. Pareto analysis is the "80/20 rule."

---

### 7.1 Cumulative Percentage

**When to use:** Calculate each product's cumulative share of total sales to identify the vital few.

```dax
Cumulative % =
VAR CurrentProductSales = [Total Sales]
VAR CumulativeSales =
    SUMX(
        FILTER(
            ALL(Product[ProductName]),
            [Total Sales] >= CurrentProductSales
        ),
        [Total Sales]
    )
VAR GrandTotal =
    CALCULATE([Total Sales], ALL(Product[ProductName]))
RETURN
    DIVIDE(CumulativeSales, GrandTotal)
```

- **How it works:** For each product, sums the sales of all products with sales **greater than or equal to** the current product's sales. This builds a cumulative percentage when products are sorted by descending sales.

---

### 7.2 ABC Classification

**When to use:** Categorize products into A (top 70%), B (next 20%), and C (bottom 10%) based on revenue contribution.

```dax
ABC Category =
VAR CumPct = [Cumulative %]
RETURN
    SWITCH(
        TRUE(),
        CumPct <= 0.70, "A",
        CumPct <= 0.90, "B",
        "C"
    )
```

- **How it works:** Uses the `[Cumulative %]` measure to classify each product. Category A represents items contributing to the first 70% of sales, B the next 20%, and C the remaining 10%.

> **Exam Tip:** ABC analysis combines `RANKX` or cumulative percentages with `SWITCH(TRUE(), ...)`. The threshold values (70/90/100) are commonly used but can be customized.

---

### 7.3 Pareto Line (80/20 Visual)

**When to use:** Create a Pareto chart showing cumulative contribution alongside individual values.

```dax
Is Pareto 80% =
IF([Cumulative %] <= 0.80, "Top 80%", "Bottom 20%")
```

- **How it works:** Flags whether a product falls in the "vital few" (top 80% of cumulative sales) or the "trivial many." Use this as a legend or color-coding field in a combo chart.

---

## 8. Dynamic Measures

Dynamic measures allow users to switch between different calculations using slicers, enabling a single visual to show different metrics.

---

### 8.1 SWITCH with Slicer Selection

**When to use:** Let users choose which measure to display via a slicer.

**Step 1 — Create a disconnected table:**

```dax
Metric Selection =
DATATABLE(
    "MetricName", STRING,
    "MetricOrder", INTEGER,
    {
        {"Total Sales", 1},
        {"Total Cost", 2},
        {"Profit", 3},
        {"Order Count", 4},
        {"Avg Order Value", 5}
    }
)
```

**Step 2 — Create the dynamic measure:**

```dax
Selected Metric =
VAR SelectedName =
    SELECTEDVALUE('Metric Selection'[MetricName], "Total Sales")
RETURN
    SWITCH(
        SelectedName,
        "Total Sales", [Total Sales],
        "Total Cost", SUM(Sales[CostAmount]),
        "Profit", [Profit],
        "Order Count", COUNTROWS(Sales),
        "Avg Order Value", [Avg Order Value],
        [Total Sales]
    )
```

- **How it works:** A disconnected table (no relationship to the model) acts as a slicer source. `SELECTEDVALUE` reads the user's selection. `SWITCH` returns the corresponding measure.

> **Exam Tip:** Disconnected tables are **not related** to any other table in the model. They only interact through measures using `SELECTEDVALUE` or `VALUES`. This is a key concept for the PL-300.

---

### 8.2 Dynamic Format String

**When to use:** Change the format of the dynamic measure based on the selected metric.

```dax
Selected Metric Format =
VAR SelectedName =
    SELECTEDVALUE('Metric Selection'[MetricName], "Total Sales")
RETURN
    SWITCH(
        SelectedName,
        "Total Sales", FORMAT([Total Sales], "$#,##0"),
        "Total Cost", FORMAT(SUM(Sales[CostAmount]), "$#,##0"),
        "Profit", FORMAT([Profit], "$#,##0"),
        "Order Count", FORMAT(COUNTROWS(Sales), "#,##0"),
        "Avg Order Value", FORMAT([Avg Order Value], "$#,##0.00"),
        FORMAT([Total Sales], "$#,##0")
    )
```

- **How it works:** Returns a **text** value with the appropriate format for each metric. Note: since this returns text, it cannot be used in charts that require numeric axes.

---

### 8.3 Calculation Groups (Conceptual)

**When to use:** Apply reusable calculation modifications (like YTD, PY, YoY%) to any measure without duplicating code.

```dax
-- Calculation Group: Time Calculation
-- Calculation Item: YTD
CALCULATE(SELECTEDMEASURE(), DATESYTD(DimDate[Date]))

-- Calculation Item: PY
CALCULATE(SELECTEDMEASURE(), SAMEPERIODLASTYEAR(DimDate[Date]))

-- Calculation Item: YoY %
VAR CurrentValue = SELECTEDMEASURE()
VAR PYValue =
    CALCULATE(SELECTEDMEASURE(), SAMEPERIODLASTYEAR(DimDate[Date]))
RETURN
    DIVIDE(CurrentValue - PYValue, PYValue)
```

- **How it works:** Calculation groups are defined in Tabular Editor (not Power BI Desktop). `SELECTEDMEASURE()` is a placeholder that applies to whichever measure the user drags into the visual.

> **Exam Tip:** Calculation groups are created in external tools like Tabular Editor. They use `SELECTEDMEASURE()` to apply transformations to any base measure. Know this concept even if you don't need to write the code from scratch.

---

## 9. Semi-Additive Measures

Semi-additive measures (like account balances, inventory levels, and headcount) should **not** be summed across time. Instead, you take the value at a specific point in time.

> **Exam Tip:** Semi-additive measures are a frequently tested topic. The key insight is that balances should use `LASTDATE`, `LASTNONBLANK`, or `CLOSINGBALANCEMONTH` instead of `SUM`.

---

### 9.1 Last Balance (LASTDATE)

**When to use:** Get the account balance as of the last date with any data in the current context.

```dax
Current Balance =
CALCULATE(
    SUM(Account[Balance]),
    LASTDATE(DimDate[Date])
)
```

- **How it works:** `LASTDATE(DimDate[Date])` returns the last date in the current filter context. `CALCULATE` then evaluates the balance on that specific date only, instead of summing across all dates.

---

### 9.2 Last Non-Blank Balance (LASTNONBLANK)

**When to use:** Get the most recent balance that has a value, even if recent dates have no transactions.

```dax
Last Known Balance =
CALCULATE(
    SUM(Account[Balance]),
    LASTNONBLANK(
        DimDate[Date],
        CALCULATE(SUM(Account[Balance]))
    )
)
```

- **How it works:** `LASTNONBLANK` iterates backward through dates and finds the last date where `SUM(Account[Balance])` returns a non-blank value. This handles gaps in data gracefully.

> **Exam Tip:** `LASTNONBLANK` vs `LASTDATE`: Use `LASTNONBLANK` when there might be gaps in the data (e.g., weekends, holidays). Use `LASTDATE` when every date in the range has data.

---

### 9.3 Closing Balance with Built-In Function

**When to use:** Get the balance at the end of a month using the built-in time intelligence function.

```dax
Closing Monthly Balance =
CLOSINGBALANCEMONTH(
    SUM(Account[Balance]),
    DimDate[Date]
)
```

- **How it works:** `CLOSINGBALANCEMONTH` automatically finds the last date of the current month and evaluates the expression at that point.

---

### 9.4 Inventory Snapshot Pattern

**When to use:** Calculate inventory on hand from periodic snapshots stored in a fact table.

```dax
Inventory On Hand =
CALCULATE(
    SUM(Inventory[QuantityOnHand]),
    LASTNONBLANK(
        DimDate[Date],
        CALCULATE(COUNTROWS(Inventory))
    )
)

Avg Monthly Inventory =
AVERAGEX(
    VALUES(DimDate[MonthKey]),
    [Inventory On Hand]
)
```

- **How it works:** The first measure gets the latest snapshot. The second measure averages inventory across months by iterating over each month and evaluating the snapshot for that month.

---

## 10. Row-Level Security

Row-Level Security (RLS) restricts data access at the row level based on the logged-in user. This is a **critical** PL-300 topic.

> **Exam Tip:** RLS is defined in Power BI Desktop (Modeling → Manage Roles) and **enforced** in Power BI Service. You must assign users to roles in the Service for RLS to take effect.

---

### 10.1 Static RLS

**When to use:** Each role sees a fixed subset of data defined by a static filter expression.

```dax
-- Role: East Region
-- Table: Store
-- DAX Filter Expression:
Store[Region] = "East"
```

- **How it works:** Users assigned to the "East Region" role can only see rows in the `Store` table where `Region = "East"`. Due to relationship propagation, all related tables (Sales, etc.) are also filtered.

---

### 10.2 Dynamic RLS with USERPRINCIPALNAME

**When to use:** Automatically filter data based on the logged-in user without creating a separate role for each person.

**Step 1 — Create a security mapping table:**

| UserEmail | Region |
|---|---|
| alice@company.com | East |
| bob@company.com | West |
| carol@company.com | Central |

**Step 2 — Create a relationship** from `SecurityTable[Region]` to `Store[Region]`.

**Step 3 — Define the RLS role:**

```dax
-- Role: Dynamic Region Security
-- Table: SecurityTable
-- DAX Filter Expression:
SecurityTable[UserEmail] = USERPRINCIPALNAME()
```

- **How it works:** `USERPRINCIPALNAME()` returns the logged-in user's email. The filter on `SecurityTable` limits the visible regions to those assigned to the user. Relationships propagate this filter to `Store` and then to `Sales`.

> **Exam Tip:** Always use `USERPRINCIPALNAME()` (not `USERNAME()`) for dynamic RLS. It returns a consistent email format in Power BI Service. Test RLS using "View as Role" in Power BI Desktop.

---

### 10.3 Manager Hierarchy RLS

**When to use:** Managers should see their own data plus data of all their direct reports.

```dax
-- Role: Manager Access
-- Table: Employee
-- DAX Filter Expression:
VAR CurrentUser = USERPRINCIPALNAME()
VAR UserEmployeeID =
    LOOKUPVALUE(Employee[EmployeeID], Employee[Email], CurrentUser)
RETURN
    Employee[EmployeeID] = UserEmployeeID
        || Employee[ManagerID] = UserEmployeeID
```

- **How it works:** Finds the current user's `EmployeeID`, then allows rows where the employee is either the user themselves or a direct report (where `ManagerID` matches the user's ID).

---

## 11. Virtual Relationships

Virtual relationships apply filters between tables that are not physically connected in the model. Useful for role-playing dimensions and many-to-many scenarios.

---

### 11.1 TREATAS — Apply Filter from Unrelated Table

**When to use:** Filter one table using values from another table when there is no physical relationship.

```dax
Budget Sales Comparison =
CALCULATE(
    SUM(Sales[SalesAmount]),
    TREATAS(
        VALUES(Budget[ProductKey]),
        Sales[ProductKey]
    )
)
```

- **How it works:** `TREATAS` takes the `ProductKey` values from the `Budget` table's current filter context and applies them as a filter on `Sales[ProductKey]`, even though there is no relationship between the two tables.

> **Exam Tip:** `TREATAS` creates a virtual relationship at query time. It's useful when you can't or shouldn't create a physical relationship (e.g., multiple fact tables sharing a dimension).

---

### 11.2 USERELATIONSHIP — Activate Inactive Relationship

**When to use:** Activate an inactive relationship for a specific calculation. Common with role-playing date dimensions (OrderDate, ShipDate, DueDate all linked to the same DimDate table).

```dax
Sales by Ship Date =
CALCULATE(
    SUM(Sales[SalesAmount]),
    USERELATIONSHIP(Sales[ShipDate], DimDate[Date])
)
```

- **How it works:** The model has an active relationship on `Sales[OrderDate] → DimDate[Date]` and an **inactive** relationship on `Sales[ShipDate] → DimDate[Date]`. `USERELATIONSHIP` temporarily activates the inactive one for this calculation.

> **Exam Tip:** Only **one** active relationship can exist between two tables. Use `USERELATIONSHIP` in `CALCULATE` to activate an inactive relationship. This is the standard pattern for role-playing dimensions.

---

### 11.3 Many-to-Many with Bridge Table

**When to use:** Handle many-to-many relationships such as students-to-courses or customers-to-products.

```dax
-- Bridge table: StudentCourse (StudentKey, CourseKey)
-- Relationships:
--   Student[StudentKey] 1→* StudentCourse[StudentKey]
--   Course[CourseKey] 1→* StudentCourse[CourseKey]

Students Per Course =
CALCULATE(
    DISTINCTCOUNT(StudentCourse[StudentKey]),
    CROSSFILTER(StudentCourse[CourseKey], Course[CourseKey], BOTH)
)
```

- **How it works:** The bridge table resolves the many-to-many relationship. `CROSSFILTER` adjusts the filter direction for bi-directional filtering when needed.

---

## 12. Error Handling

Robust DAX measures handle errors and blank values gracefully to avoid confusing users with error messages.

---

### 12.1 Safe Division with DIVIDE

**When to use:** Any division operation where the denominator could be zero or BLANK.

```dax
-- BAD: Can produce Infinity error
Margin Bad = Sales[Revenue] / Sales[Cost]

-- GOOD: Returns 0 when Cost is zero
Margin Good = DIVIDE(Sales[Revenue], Sales[Cost], 0)

-- GOOD: Returns BLANK when Cost is zero (default)
Margin Blank = DIVIDE(Sales[Revenue], Sales[Cost])
```

- **How it works:** `DIVIDE` checks for zero or BLANK in the denominator and returns the alternate result (third argument). If no alternate is provided, it returns BLANK.

> **Exam Tip:** Always use `DIVIDE()` instead of `/` for division in measures. This is a best practice that appears on the exam.

---

### 12.2 IFERROR for Complex Expressions

**When to use:** Wrap any expression that might produce an error (not just division).

```dax
Safe Lookup =
IFERROR(
    LOOKUPVALUE(
        Product[ProductName],
        Product[ProductKey], Sales[ProductKey]
    ),
    "Unknown Product"
)
```

- **How it works:** If `LOOKUPVALUE` fails (e.g., no match or multiple matches), `IFERROR` catches the error and returns "Unknown Product."

---

### 12.3 COALESCE for Fallback Values

**When to use:** Return the first non-BLANK value from multiple expressions. Replaces nested `IF(ISBLANK(...))` chains.

```dax
Display Price =
COALESCE(
    Product[SalePrice],
    Product[ListPrice],
    Product[DefaultPrice],
    0
)
```

- **How it works:** Evaluates each argument left to right. Returns the first one that is not BLANK. If all are BLANK, returns 0.

---

### 12.4 BLANK Handling in Measures

**When to use:** Control what users see when a measure has no data.

```dax
Total Sales (No Blank) =
IF(
    ISBLANK(SUM(Sales[SalesAmount])),
    0,
    SUM(Sales[SalesAmount])
)

-- More concise using COALESCE:
Total Sales (No Blank) =
COALESCE(SUM(Sales[SalesAmount]), 0)
```

- **How it works:** Replaces BLANK with zero so visuals show "0" instead of empty cells. Use judiciously — sometimes BLANK is the correct behavior (it hides rows in matrix visuals).

> **Exam Tip:** Returning BLANK from a measure causes the row to be hidden in matrix visuals. Returning 0 keeps the row visible. Choose intentionally based on the business requirement.

---

## 13. Budget vs Actuals

Budget vs Actual analysis is a core business scenario that tests your ability to work with multiple fact tables and different granularities.

---

### 13.1 Basic Variance

**When to use:** Show the difference between actual performance and budget.

```dax
Total Budget = SUM(Budget[BudgetAmount])

Total Actuals = SUM(Sales[SalesAmount])

Variance = [Total Actuals] - [Total Budget]
```

- **How it works:** Subtracts budget from actuals. A positive variance means actual performance exceeded the budget (favorable). Negative means underperformance (unfavorable).

---

### 13.2 Variance Percentage

**When to use:** Express the variance as a percentage of budget for relative comparison.

```dax
Variance % =
DIVIDE(
    [Variance],
    [Total Budget],
    0
)
```

- **How it works:** Divides the variance by the budget amount. A result of 0.10 means 10% above budget.

---

### 13.3 Conditional Formatting Measures

**When to use:** Create measures that drive conditional formatting in visuals to highlight favorable/unfavorable variances.

```dax
Variance Color =
SWITCH(
    TRUE(),
    [Variance %] >= 0.05, "Green",
    [Variance %] >= 0, "Yellow",
    "Red"
)

Variance Icon =
VAR VarPct = [Variance %]
RETURN
    SWITCH(
        TRUE(),
        VarPct >= 0.05, "⬆",
        VarPct >= -0.05, "➡",
        "⬇"
    )
```

- **How it works:** Returns text values that can be used with conditional formatting rules or KPI visuals. Green/⬆ for strong performance, Yellow/➡ for on-target, Red/⬇ for underperformance.

---

### 13.4 Budget at Different Granularity

**When to use:** When budget data is at a higher granularity (e.g., monthly) than actuals (daily).

```dax
-- Budget stored at Month-Category level
-- Actuals stored at Day-Product level

Monthly Budget =
CALCULATE(
    SUM(Budget[BudgetAmount]),
    ALLEXCEPT(
        Budget,
        Budget[Year],
        Budget[Month],
        Budget[Category]
    )
)
```

- **How it works:** `ALLEXCEPT` keeps only the Budget table's granularity columns and removes any filters at a finer grain. This prevents the budget from being split across days or products.

> **Exam Tip:** When budget and actual tables have different granularities, ensure relationships are at the correct level. Use `ALLEXCEPT` or adjust the model to match granularities through a shared dimension.

---

### 13.5 Full Budget vs Actuals Report Measure Set

**When to use:** Complete set of measures for a budget variance report.

```dax
-- Base measures
Total Budget = SUM(Budget[BudgetAmount])
Total Actuals = SUM(Sales[SalesAmount])

-- Variance measures
Variance = [Total Actuals] - [Total Budget]
Variance % = DIVIDE([Variance], [Total Budget])

-- Achievement measure
Budget Achievement % = DIVIDE([Total Actuals], [Total Budget])

-- Status classification
Performance Status =
SWITCH(
    TRUE(),
    [Budget Achievement %] >= 1.05, "Exceeding",
    [Budget Achievement %] >= 0.95, "On Track",
    [Budget Achievement %] >= 0.80, "At Risk",
    ISBLANK([Budget Achievement %]), "No Data",
    "Behind"
)

-- YTD Budget comparison
Budget YTD = TOTALYTD([Total Budget], DimDate[Date])
Actuals YTD = TOTALYTD([Total Actuals], DimDate[Date])
Variance YTD = [Actuals YTD] - [Budget YTD]
Variance YTD % = DIVIDE([Variance YTD], [Budget YTD])
```

- **How it works:** This complete measure set provides everything needed for a budget variance report: base values, variances (absolute and %), achievement %, a status classifier, and YTD comparisons.

> **Exam Tip:** Budget vs Actual questions on the PL-300 often test: (1) handling different granularities between budget and actuals, (2) using the correct time intelligence for YTD budget, and (3) creating conditional formatting measures for visual indicators.

---

## Quick Reference — Pattern Index

| # | Pattern | Key Functions | Difficulty |
|---|---|---|---|
| 1 | Basic Measures | `SUM`, `SUMX`, `DISTINCTCOUNT` | ⭐ |
| 2 | Percentage Patterns | `ALL`, `ALLSELECTED`, `ALLEXCEPT`, `DIVIDE` | ⭐⭐ |
| 3 | Year-over-Year | `SAMEPERIODLASTYEAR`, `DATEADD` | ⭐⭐ |
| 4 | Time Intelligence | `TOTALYTD`, `DATESINPERIOD`, `CLOSINGBALANCEMONTH` | ⭐⭐ |
| 5 | Ranking | `RANKX`, `TOPN` | ⭐⭐ |
| 6 | Cumulative Totals | `FILTER`, `ALL`, running sum pattern | ⭐⭐⭐ |
| 7 | ABC / Pareto | `RANKX`, `SWITCH(TRUE())`, cumulative % | ⭐⭐⭐ |
| 8 | Dynamic Measures | `SWITCH`, `SELECTEDVALUE`, disconnected tables | ⭐⭐⭐ |
| 9 | Semi-Additive | `LASTDATE`, `LASTNONBLANK`, `CLOSINGBALANCEMONTH` | ⭐⭐⭐ |
| 10 | Row-Level Security | `USERPRINCIPALNAME`, filter expressions | ⭐⭐ |
| 11 | Virtual Relationships | `TREATAS`, `USERELATIONSHIP` | ⭐⭐⭐ |
| 12 | Error Handling | `DIVIDE`, `IFERROR`, `COALESCE` | ⭐ |
| 13 | Budget vs Actuals | `ALLEXCEPT`, variance calculations | ⭐⭐ |

> **Exam Tip:** Focus on patterns 1–5 and 9–10 for the highest exam coverage. Time intelligence, percentage calculations, semi-additive measures, and RLS are the most frequently tested DAX pattern categories on the PL-300.
