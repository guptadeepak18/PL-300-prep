# Practice Database for PL-300 Exam Preparation

A collection of interrelated CSV datasets designed for practicing Power BI data modeling, DAX calculations, and Power Query transformations in preparation for the **Microsoft PL-300: Power BI Data Analyst** certification exam.

---

## 📁 Dataset Overview

| File | Rows | Description |
|------|------|-------------|
| `sales-data.csv` | 100 | Fact table — sales transactions (2023–2025) |
| `products-data.csv` | 30 | Dimension — product catalog across 5 categories |
| `stores-data.csv` | 10 | Dimension — retail store locations |
| `customers-data.csv` | 50 | Dimension — customer records |
| `dates-data.csv` | 1,096 | Dimension — date table (every day, 2023-01-01 to 2025-12-31) |
| `budget-data.csv` | 120 | Fact table — monthly budget and forecast figures |
| `employees-data.csv` | 40 | Dimension — employee / HR data with hierarchy |

---

## ⭐ Star Schema Design

The data follows a **star schema** pattern, which is the recommended model for Power BI:

```
                    ┌──────────────┐
                    │  dates-data  │
                    │  (Date Dim)  │
                    └──────┬───────┘
                           │ Date ↔ SaleDate
    ┌──────────────┐       │       ┌────────────────┐
    │products-data ├───────┼───────┤  sales-data     │
    │ (Product Dim)│ ProdID│       │  (Fact Table)   │
    └──────────────┘       │       └───┬────┬────────┘
                           │           │    │
    ┌──────────────┐       │           │    │
    │ stores-data  ├───────┘    StoreID│    │CustomerID
    │ (Store Dim)  │                   │    │
    └──────┬───────┘                   │    │
           │                           │    │
           │        ┌──────────────────┘    │
           │        │                       │
           │  ┌─────┴────────┐   ┌──────────┴─────┐
           │  │ budget-data  │   │customers-data   │
           │  │ (Fact Table) │   │ (Customer Dim)  │
           │  └──────────────┘   └─────────────────┘
           │
    ┌──────┴───────┐
    │employees-data│
    │  (HR Dim)    │
    └──────────────┘
```

### Relationships

| From (Many) | → | To (One) | Key Column | Cardinality |
|---|---|---|---|---|
| `sales-data` | → | `products-data` | `ProductID` | Many-to-One |
| `sales-data` | → | `stores-data` | `StoreID` | Many-to-One |
| `sales-data` | → | `customers-data` | `CustomerID` | Many-to-One |
| `sales-data` | → | `dates-data` | `SaleDate` ↔ `Date` | Many-to-One |
| `budget-data` | → | `stores-data` | `StoreID` | Many-to-One |
| `employees-data` | → | `stores-data` | `StoreID` | Many-to-One |
| `employees-data` | → | `employees-data` | `ManagerID` ↔ `EmployeeID` | Self-referencing |

---

## 🔧 Setting Up in Power BI Desktop

### Step 1: Import the Data

1. Open **Power BI Desktop**.
2. Click **Home → Get Data → Text/CSV**.
3. Navigate to the `practice-database` folder and select **all 7 CSV files** one at a time (or use **Folder** connector to load all at once).
4. For each file, verify the data preview looks correct and click **Load** (or **Transform Data** to inspect first).

### Step 2: Create Relationships

1. Go to **Model view** (the icon with connected boxes on the left sidebar).
2. Power BI may auto-detect some relationships. Verify or manually create each one:

   **Sales → Products:**
   - Drag `ProductID` from `sales-data` to `ProductID` in `products-data`
   - Cardinality: Many-to-One, Cross filter: Single

   **Sales → Stores:**
   - Drag `StoreID` from `sales-data` to `StoreID` in `stores-data`
   - Cardinality: Many-to-One, Cross filter: Single

   **Sales → Customers:**
   - Drag `CustomerID` from `sales-data` to `CustomerID` in `customers-data`
   - Cardinality: Many-to-One, Cross filter: Single

   **Sales → Dates:**
   - Drag `SaleDate` from `sales-data` to `Date` in `dates-data`
   - Cardinality: Many-to-One, Cross filter: Single

   **Budget → Stores:**
   - Drag `StoreID` from `budget-data` to `StoreID` in `stores-data`
   - Cardinality: Many-to-One, Cross filter: Single
   - ⚠️ This creates a second relationship to stores-data. Set it as **inactive** if you already have the Sales → Stores relationship active.

   **Employees → Stores:**
   - Drag `StoreID` from `employees-data` to `StoreID` in `stores-data`
   - Cardinality: Many-to-One, Cross filter: Single

   **Employees → Employees (Self-referencing):**
   - Drag `ManagerID` from `employees-data` to `EmployeeID` in the same table
   - Cardinality: Many-to-One

### Step 3: Mark the Date Table

1. Select `dates-data` in the **Model view** or **Data view**.
2. Go to **Table tools → Mark as date table**.
3. Choose the `Date` column as the date column.

### Step 4: Disable Auto Date/Time

1. Go to **File → Options and settings → Options → Current File → Data Load**.
2. Uncheck **Auto date/time** (avoids hidden date tables that conflict with your explicit one).

---

## 📋 Column Descriptions

### sales-data.csv (Fact Table)

| Column | Type | Description |
|--------|------|-------------|
| `SaleID` | Integer | Unique transaction identifier (1001–1100) |
| `SaleDate` | Date | Date of the sale (YYYY-MM-DD) |
| `ProductID` | Text | Foreign key → `products-data` |
| `StoreID` | Text | Foreign key → `stores-data` |
| `CustomerID` | Text | Foreign key → `customers-data` |
| `Quantity` | Integer | Number of units sold (1–20) |
| `UnitPrice` | Decimal | Price per unit at time of sale |
| `DiscountPercent` | Integer | Discount applied (0, 5, 10, 15, or 20) |
| `TotalAmount` | Decimal | `Quantity × UnitPrice × (1 - DiscountPercent/100)` |

### products-data.csv (Dimension)

| Column | Type | Description |
|--------|------|-------------|
| `ProductID` | Text | Unique product key (P001–P030) |
| `ProductName` | Text | Display name of the product |
| `Category` | Text | Top-level grouping (Electronics, Clothing, Home & Garden, Sports, Books) |
| `SubCategory` | Text | Detailed grouping within each category |
| `UnitCost` | Decimal | Cost to acquire/produce the product |
| `UnitPrice` | Decimal | Standard retail price |
| `Brand` | Text | Manufacturer or brand name |

### stores-data.csv (Dimension)

| Column | Type | Description |
|--------|------|-------------|
| `StoreID` | Text | Unique store key (S001–S010) |
| `StoreName` | Text | Descriptive store name |
| `City` | Text | City location |
| `State` | Text | US state |
| `Country` | Text | Country (all USA) |
| `Region` | Text | Geographic region (East, West, Central, South) |
| `StoreType` | Text | Store classification (Flagship, Standard, Outlet) |
| `OpenDate` | Date | Date the store opened |

### customers-data.csv (Dimension)

| Column | Type | Description |
|--------|------|-------------|
| `CustomerID` | Text | Unique customer key (C001–C050) |
| `FirstName` | Text | Customer first name |
| `LastName` | Text | Customer last name |
| `Email` | Text | Email address |
| `City` | Text | City of residence |
| `State` | Text | US state |
| `Country` | Text | Country (all USA) |
| `Segment` | Text | Customer tier (Premium, Standard, Budget) |
| `JoinDate` | Date | Date the customer first registered |

### dates-data.csv (Date Dimension)

| Column | Type | Description |
|--------|------|-------------|
| `Date` | Date | Calendar date (2023-01-01 to 2025-12-31) |
| `Year` | Integer | Calendar year |
| `Quarter` | Text | Calendar quarter (Q1–Q4) |
| `Month` | Integer | Month number (1–12) |
| `MonthName` | Text | Full month name (January–December) |
| `MonthShort` | Text | Three-letter abbreviation (Jan–Dec) |
| `DayOfWeek` | Integer | Day of week (1=Monday to 7=Sunday) |
| `DayName` | Text | Full day name (Monday–Sunday) |
| `DayShort` | Text | Three-letter abbreviation (Mon–Sun) |
| `WeekNumber` | Integer | ISO week number |
| `IsWeekend` | Boolean | TRUE if Saturday or Sunday |
| `FiscalYear` | Text | Fiscal year starting July 1 (e.g., FY2024) |
| `FiscalQuarter` | Text | Fiscal quarter (FQ1=Jul–Sep through FQ4=Apr–Jun) |

### budget-data.csv (Fact Table)

| Column | Type | Description |
|--------|------|-------------|
| `BudgetID` | Text | Unique budget entry key (B001–B120) |
| `Year` | Integer | Budget year |
| `Month` | Integer | Budget month (1–12) |
| `MonthName` | Text | Full month name |
| `StoreID` | Text | Foreign key → `stores-data` |
| `Category` | Text | Product category the budget is for |
| `BudgetAmount` | Decimal | Planned budget amount |
| `ForecastAmount` | Decimal | Revised forecast amount |

### employees-data.csv (Dimension)

| Column | Type | Description |
|--------|------|-------------|
| `EmployeeID` | Text | Unique employee key (E001–E040) |
| `FirstName` | Text | Employee first name |
| `LastName` | Text | Employee last name |
| `Email` | Text | Work email address |
| `Department` | Text | Department (Sales, Marketing, IT, Finance, HR, Operations) |
| `JobTitle` | Text | Role within the department |
| `HireDate` | Date | Date hired |
| `Salary` | Decimal | Annual salary |
| `ManagerID` | Text | Foreign key → `EmployeeID` (blank for top-level directors) |
| `StoreID` | Text | Assigned store (blank for HQ employees) |
| `PerformanceRating` | Integer | Annual rating (1=Needs Improvement to 5=Exceptional) |

---

## 🎯 Suggested Practice Exercises

### Beginner

1. **Basic Visuals** — Create a bar chart showing total sales by product category.
2. **Time Series** — Build a line chart of monthly sales over the 3-year period.
3. **Slicers** — Add slicers for Year, Quarter, Region, and Customer Segment. Observe how they filter visuals.
4. **Card Visuals** — Display KPIs: Total Revenue, Total Transactions, Average Order Value.
5. **Table Visual** — Show top 10 products by revenue with conditional formatting.

### Intermediate

6. **Year-over-Year Growth** — Calculate and visualize YoY sales growth using DAX time intelligence.
7. **Budget vs. Actual** — Compare actual sales to budget amounts by month and category.
8. **Customer Segmentation** — Analyze purchasing patterns across Premium, Standard, and Budget segments.
9. **Profit Margin Analysis** — Calculate profit using `UnitPrice - UnitCost` and create margin % measures.
10. **Store Performance Dashboard** — Rank stores by revenue, transaction count, and average discount given.

### Advanced

11. **Rolling Averages** — Create a 3-month rolling average of sales.
12. **Pareto Analysis** — Identify which 20% of products drive 80% of revenue.
13. **Employee Hierarchy** — Build an org chart or hierarchical slicer using the self-referencing ManagerID.
14. **Inactive Relationships** — Use `USERELATIONSHIP()` to activate the Budget → Store relationship in measures.
15. **Fiscal Year Reporting** — Create all reports using the fiscal calendar (FY starts July 1).
16. **What-If Parameters** — Add a discount adjustment parameter and recalculate projected revenue.
17. **Row-Level Security (RLS)** — Configure RLS so store managers only see their own store's data.
18. **Decomposition Tree** — Drill into sales by Region → Store → Category → Product.

---

## 💡 Tips for DAX Practice

### Essential Measures to Create

```dax
// Total Revenue
Total Revenue = SUM('sales-data'[TotalAmount])

// Total Cost (requires relationship to products)
Total Cost =
SUMX(
    'sales-data',
    'sales-data'[Quantity] * RELATED('products-data'[UnitCost])
)

// Gross Profit
Gross Profit = [Total Revenue] - [Total Cost]

// Gross Margin %
Gross Margin % = DIVIDE([Gross Profit], [Total Revenue], 0)

// Year-over-Year Growth
YoY Growth % =
VAR CurrentYear = [Total Revenue]
VAR PreviousYear = CALCULATE([Total Revenue], SAMEPERIODLASTYEAR('dates-data'[Date]))
RETURN DIVIDE(CurrentYear - PreviousYear, PreviousYear, 0)

// Budget Variance
Budget Variance =
[Total Revenue] - SUM('budget-data'[BudgetAmount])

// Running Total
Running Total =
CALCULATE(
    [Total Revenue],
    DATESYTD('dates-data'[Date])
)

// Average Order Value
Avg Order Value = AVERAGE('sales-data'[TotalAmount])

// Customer Count
Customer Count = DISTINCTCOUNT('sales-data'[CustomerID])
```

### Time Intelligence Functions to Practice

- `TOTALYTD()`, `TOTALQTD()`, `TOTALMTD()`
- `SAMEPERIODLASTYEAR()`, `PREVIOUSMONTH()`, `PREVIOUSQUARTER()`
- `DATEADD()`, `DATESBETWEEN()`, `DATESYTD()`
- `PARALLELPERIOD()`, `CLOSINGBALANCEMONTH()`

---

## 💡 Tips for Power Query Practice

### Transformations to Try

1. **Merge Queries** — Join `sales-data` with `products-data` on `ProductID` to bring in category and cost columns.
2. **Append Queries** — Create a combined fact table from sales and budget data.
3. **Unpivot Columns** — Unpivot the budget/forecast columns in `budget-data` to create an Amount and AmountType column.
4. **Custom Columns** — Add a calculated `Profit` column: `Quantity * (UnitPrice - UnitCost)`.
5. **Group By** — Aggregate sales by month and category in Power Query.
6. **Conditional Columns** — Create discount tier labels (No Discount, Low, Medium, High) based on `DiscountPercent`.
7. **Split Columns** — Split `Email` into username and domain parts in the customer table.
8. **Data Type Handling** — Ensure all date columns are typed as Date, numbers as appropriate numeric types.
9. **Filter Rows** — Practice removing rows based on conditions (e.g., exclude zero-quantity sales).
10. **Replace Values** — Standardize region names or fix data quality issues.

### Data Quality Checks

- Verify no orphaned foreign keys exist (all `ProductID`, `StoreID`, `CustomerID` values match their dimension tables)
- Check for duplicate primary keys in each dimension table
- Validate that `TotalAmount = Quantity × UnitPrice × (1 - DiscountPercent / 100)`

---

## 📝 Notes

- All dates use **YYYY-MM-DD** (ISO 8601) format for unambiguous parsing.
- The date dimension covers **2023-01-01 through 2025-12-31** (1,096 days).
- Fiscal year begins **July 1** (e.g., July 2023 through June 2024 = FY2024).
- Budget data covers a subset of stores (S001–S002) to practice scenarios where not all dimension members have fact data.
- Employee `ManagerID` is blank (not null) for the 6 department directors who report to no one — useful for practicing parent-child hierarchies.
- CSV files use **comma delimiters** with no quoting unless a value contains a comma.
