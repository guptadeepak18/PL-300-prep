# Case Study 1: Contoso Retail Group — Retail Sales Analytics

> 📋 **Exam Domains Covered:** Prepare the Data | Model the Data | Visualize & Analyze | Deploy & Maintain
> 🕐 **Estimated Study Time:** 90 minutes | **Questions:** 12 scenario-based

---

## Table of Contents

1. [Company Background](#1-company-background)
2. [Data Sources and Description](#2-data-sources-and-description)
3. [Data Model Overview](#3-data-model-overview)
4. [Scenario Questions](#4-scenario-questions)
   - [Q1 – Connecting to Data Sources](#question-1--connecting-to-data-sources)
   - [Q2 – Cleaning Product Data](#question-2--cleaning-product-data)
   - [Q3 – Transforming Sales Data](#question-3--transforming-sales-data)
   - [Q4 – Building the Star Schema](#question-4--building-the-star-schema)
   - [Q5 – Creating a Date Table](#question-5--creating-a-date-table)
   - [Q6 – DAX Measures for Sales Analysis](#question-6--dax-measures-for-sales-analysis)
   - [Q7 – Year-over-Year Growth](#question-7--year-over-year-growth)
   - [Q8 – Choosing Visuals for Sales Dashboard](#question-8--choosing-visuals-for-sales-dashboard)
   - [Q9 – Drill-Through for Store Details](#question-9--drill-through-for-store-details)
   - [Q10 – Conditional Formatting](#question-10--conditional-formatting)
   - [Q11 – Row-Level Security for Regional Managers](#question-11--row-level-security-for-regional-managers)
   - [Q12 – Workspace and App Deployment](#question-12--workspace-and-app-deployment)
5. [Key Exam Concepts Summary](#5-key-exam-concepts-summary)

---

## 1. Company Background

**Contoso Retail Group** is a mid-size retail chain operating **120 stores** across four regions in the United States: **Northeast, Southeast, Midwest, and West**. The company sells consumer electronics, home appliances, and accessories through both physical stores and an online channel.

**Key business stakeholders:**

- **VP of Sales** — Needs a high-level dashboard showing revenue, profit, and growth trends
- **Regional Managers (4)** — Each manages 30 stores and needs visibility into their own region only
- **Merchandising Team** — Analyzes product category performance and inventory turnover
- **Finance Team** — Tracks budget vs. actual sales by quarter

The company currently tracks data in multiple systems and needs a unified Power BI solution to replace manual Excel reporting.

---

## 2. Data Sources and Description

| Source | System | Format | Refresh Frequency | Approximate Size |
|--------|--------|--------|--------------------|------------------|
| **Sales Transactions** | Azure SQL Database | Relational tables | Daily | ~5 million rows/year |
| **Product Catalog** | SQL Server (on-premises) | Relational tables | Weekly | ~8,000 products |
| **Store Directory** | SharePoint List | List with metadata | Monthly | 120 rows |
| **Budget Data** | Excel workbook | `.xlsx` on SharePoint | Quarterly | ~500 rows |
| **Promotions Calendar** | CSV file | `.csv` in shared folder | Ad hoc | ~200 rows |

### Sales Transactions Table (FactSales)

| Column | Data Type | Description |
|--------|-----------|-------------|
| TransactionID | Integer | Primary key |
| TransactionDate | Date | Date of sale |
| StoreID | Integer | Foreign key to DimStore |
| ProductID | Integer | Foreign key to DimProduct |
| Quantity | Integer | Units sold |
| UnitPrice | Decimal | Selling price per unit |
| DiscountAmount | Decimal | Discount applied |
| CustomerID | Integer | Foreign key to DimCustomer |

### Product Catalog Table (DimProduct)

| Column | Data Type | Description |
|--------|-----------|-------------|
| ProductID | Integer | Primary key |
| ProductName | Text | Product display name |
| Category | Text | Top-level category (e.g., "Laptops") |
| SubCategory | Text | Sub-category (e.g., "Gaming Laptops") |
| Brand | Text | Manufacturer brand |
| UnitCost | Decimal | Cost to Contoso |
| Status | Text | "Active", "Discontinued", "Pending" |

### Store Directory Table (DimStore)

| Column | Data Type | Description |
|--------|-----------|-------------|
| StoreID | Integer | Primary key |
| StoreName | Text | Display name |
| Region | Text | Northeast, Southeast, Midwest, West |
| State | Text | Two-letter state code |
| City | Text | City name |
| StoreType | Text | "Flagship", "Standard", "Outlet" |
| OpenDate | Date | Store opening date |
| ManagerEmail | Text | Regional manager email address |

### Budget Data Table (FactBudget)

| Column | Data Type | Description |
|--------|-----------|-------------|
| BudgetID | Integer | Primary key |
| FiscalYear | Integer | Budget year |
| FiscalQuarter | Text | Q1, Q2, Q3, Q4 |
| StoreID | Integer | Foreign key to DimStore |
| Category | Text | Product category |
| BudgetAmount | Decimal | Budgeted sales amount |

---

## 3. Data Model Overview

The data model follows a **star schema** design with one primary fact table and shared dimension tables.

```
                    ┌──────────────┐
                    │   DimDate    │
                    │──────────────│
                    │ DateKey (PK) │
                    │ Date         │
                    │ Year         │
                    │ Quarter      │
                    │ Month        │
                    │ MonthName    │
                    │ WeekOfYear   │
                    └──────┬───────┘
                           │ 1
                           │
           ┌───────────────┤
           │               │
     ┌─────┴────────┐  ┌──┴──────────────┐  ┌───────────────┐
     │  FactBudget  │  │   FactSales     │  │  DimProduct   │
     │──────────────│  │─────────────────│  │───────────────│
     │ BudgetID(PK) │  │TransactionID(PK)│  │ProductID (PK) │
     │ FiscalYear   │  │TransactionDate  │──│ProductName    │
     │ FiscalQuarter│  │StoreID (FK)     │  │Category       │
     │ StoreID (FK) │  │ProductID (FK)   │  │SubCategory    │
     │ Category     │  │Quantity         │  │Brand          │
     │ BudgetAmount │  │UnitPrice        │  │UnitCost       │
     └─────┬────────┘  │DiscountAmount   │  │Status         │
           │           │CustomerID (FK)  │  └───────────────┘
           │           └──────┬──────────┘
           │                  │
           │            ┌─────┴─────────┐
           └────────────┤   DimStore    │
                        │───────────────│
                        │StoreID (PK)   │
                        │StoreName      │
                        │Region         │
                        │State          │
                        │City           │
                        │StoreType      │
                        │OpenDate       │
                        │ManagerEmail   │
                        └───────────────┘
```

**Relationship summary:**

| From (Many) | To (One) | Cardinality | Cross-Filter |
|-------------|----------|-------------|--------------|
| FactSales[TransactionDate] | DimDate[Date] | Many-to-One | Single |
| FactSales[StoreID] | DimStore[StoreID] | Many-to-One | Single |
| FactSales[ProductID] | DimProduct[ProductID] | Many-to-One | Single |
| FactBudget[StoreID] | DimStore[StoreID] | Many-to-One | Single |
| FactBudget[FiscalYear] & [FiscalQuarter] | DimDate (inactive) | — | — |

> **Exam Tip:** When two fact tables share a dimension (like DimStore), both should connect to the same dimension table. Avoid creating separate dimension copies.

---

## 4. Scenario Questions

---

### Question 1 — Connecting to Data Sources

**The Contoso data team needs to connect Power BI Desktop to the following sources:**
- **Azure SQL Database** for sales transactions (refreshed daily)
- **On-premises SQL Server** for the product catalog (behind a firewall)
- **SharePoint Online list** for the store directory

**What connectivity mode and gateway configuration should be used for each source?**

---

#### ✅ Answer

| Source | Connectivity Mode | Gateway Required? | Details |
|--------|-------------------|-------------------|---------|
| Azure SQL Database | **Import** | No | Cloud-to-cloud connection; schedule daily refresh in the Power BI service |
| On-premises SQL Server | **Import** | **Yes — On-premises data gateway** | Gateway bridges on-premises network to Power BI service |
| SharePoint Online | **Import** | No | Direct cloud connection using organizational account |

**Key considerations:**

- **Import mode** is recommended because the dataset is moderate in size (~5M rows/year) and users need fast report performance
- **DirectQuery** could be considered for the Azure SQL source if near-real-time data is required, but at the cost of slower visuals
- The **on-premises data gateway** must be installed on a server within the corporate network that can reach the SQL Server instance
- Gateway must be registered in the Power BI service under **Settings > Manage gateways**

> **Exam Tip:** An on-premises data gateway is required for any on-premises data source when publishing to the Power BI service. Cloud sources (Azure SQL, SharePoint Online) do not require a gateway.

---

### Question 2 — Cleaning Product Data

**While profiling the DimProduct table, you discover the following data quality issues:**
1. The **Status** column contains inconsistent values: `"active"`, `"Active"`, `"ACTIVE"`, and `"active "`
2. **15 products** have `null` values in the **Category** column
3. The **ProductName** column contains leading/trailing spaces

**How should you resolve these issues in Power Query?**

---

#### ✅ Answer

Apply the following transformations in **Power Query Editor**:

**Step 1: Standardize the Status column**

```m
// Trim whitespace and capitalize properly
= Table.TransformColumns(PreviousStep, {
    {"Status", each Text.Proper(Text.Trim(_)), type text}
})
```

This converts all values like `"active"`, `"ACTIVE"`, and `"active "` to `"Active"`.

**Step 2: Replace nulls in Category**

```m
// Replace null Category with "Uncategorized"
= Table.ReplaceValue(PreviousStep, null, "Uncategorized", Replacer.ReplaceValue, {"Category"})
```

**Step 3: Trim ProductName**

```m
// Remove leading and trailing spaces from ProductName
= Table.TransformColumns(PreviousStep, {
    {"ProductName", Text.Trim, type text}
})
```

**Alternatively**, in the Power Query UI:
1. Select the **Status** column → **Transform** → **Format** → **Trim** → then **Capitalize Each Word**
2. Select **Category** → **Transform** → **Replace Values** → replace `null` with `"Uncategorized"`
3. Select **ProductName** → **Transform** → **Format** → **Trim**

> **Exam Tip:** Always profile your data using **Column Quality**, **Column Distribution**, and **Column Profile** in Power Query. Set "Column profiling based on entire data set" (not just top 1000 rows) for accurate profiling.

---

### Question 3 — Transforming Sales Data

**The sales transactions table contains 3 years of historical data (~15 million rows). The finance team only needs the last 2 years. Additionally, the TransactionDate column is stored as a text value in `YYYYMMDD` format (e.g., `"20230115"`).**

**How should you handle these transformations in Power Query to optimize performance?**

---

#### ✅ Answer

**Step 1: Filter rows at the source using query folding**

```m
let
    Source = Sql.Database("contoso-server.database.windows.net", "SalesDB"),
    FactSales = Source{[Schema="dbo", Item="FactSales"]}[Data],
    // Filter to last 2 years — this step folds to SQL
    FilteredRows = Table.SelectRows(FactSales, each [TransactionDate] >= "20220101")
in
    FilteredRows
```

**Step 2: Convert TransactionDate from text to Date**

```m
// Parse YYYYMMDD text to a proper Date type
= Table.TransformColumns(FilteredRows, {
    {"TransactionDate", each Date.From(
        Text.Combine({
            Text.Start(_, 4), "-",
            Text.Middle(_, 4, 2), "-",
            Text.End(_, 2)
        })
    ), type date}
})
```

**Performance optimizations:**

- **Query folding** pushes the date filter to SQL Server, reducing data transferred from ~15M to ~10M rows
- Verify folding by right-clicking the step → **View Native Query** — if this option is greyed out, folding has broken
- Place the filter step **before** any transformation that breaks folding
- Consider using **incremental refresh** for the sales table to only refresh new/changed data

> **Exam Tip:** Query folding is critical for performance. Operations like filtering, selecting columns, and basic aggregations typically fold. Operations like merging with non-SQL sources, adding custom columns with M functions, and pivot/unpivot may break folding.

---

### Question 4 — Building the Star Schema

**After loading all tables, you need to create relationships in the data model. The FactBudget table uses `FiscalYear` (Integer) and `FiscalQuarter` (Text, e.g., "Q1") instead of a date column. DimDate has corresponding Year and Quarter columns.**

**How should you model the relationship between FactBudget and DimDate?**

---

#### ✅ Answer

Since FactBudget does not have a date column that maps 1:1 to DimDate[Date], you have two options:

**Option A: Create a surrogate key (Recommended)**

Add a computed column to both tables that combines Year and Quarter:

In **DimDate** (calculated column in DAX):

```dax
DateQuarterKey = DimDate[Year] * 10 + DimDate[QuarterNumber]
```

In **FactBudget** (Power Query or DAX):

```dax
BudgetQuarterKey = FactBudget[FiscalYear] * 10 +
    SWITCH(
        FactBudget[FiscalQuarter],
        "Q1", 1,
        "Q2", 2,
        "Q3", 3,
        "Q4", 4
    )
```

Then create a **many-to-one** relationship from `FactBudget[BudgetQuarterKey]` to a deduplicated `DimDate[DateQuarterKey]`.

**Option B: Use a separate Budget Date dimension**

Create a **DimBudgetPeriod** table with distinct Year-Quarter combinations and link both FactBudget and DimDate to it.

**Best practice:**

- Avoid many-to-many relationships when possible
- Keep fact-to-dimension relationships as **many-to-one** with **single** cross-filter direction
- Mark **DimDate** as the official **Date table** using `Mark as Date Table`

> **Exam Tip:** Every fact table should connect to a date dimension. If a fact table lacks a proper date column, create a surrogate key or a bridge table. Always mark your date table to enable built-in time intelligence.

---

### Question 5 — Creating a Date Table

**The VP of Sales wants time intelligence measures (YTD, prior year comparison). You need to create a dedicated Date table that covers all transaction dates.**

**Write the DAX to generate a complete Date table.**

---

#### ✅ Answer

```dax
DimDate =
VAR MinDate = DATE(2021, 1, 1)
VAR MaxDate = DATE(2024, 12, 31)
RETURN
ADDCOLUMNS(
    CALENDAR(MinDate, MaxDate),
    "Year", YEAR([Date]),
    "QuarterNumber", QUARTER([Date]),
    "Quarter", "Q" & QUARTER([Date]),
    "MonthNumber", MONTH([Date]),
    "MonthName", FORMAT([Date], "MMMM"),
    "MonthShort", FORMAT([Date], "MMM"),
    "MonthYear", FORMAT([Date], "MMM YYYY"),
    "WeekOfYear", WEEKNUM([Date]),
    "DayOfWeek", WEEKDAY([Date]),
    "DayName", FORMAT([Date], "dddd"),
    "IsWeekend", IF(WEEKDAY([Date]) IN {1, 7}, TRUE(), FALSE()),
    "FiscalYear", IF(MONTH([Date]) >= 7, YEAR([Date]) + 1, YEAR([Date])),
    "FiscalQuarter",
        "FQ" & SWITCH(
            TRUE(),
            MONTH([Date]) IN {7,8,9}, "1",
            MONTH([Date]) IN {10,11,12}, "2",
            MONTH([Date]) IN {1,2,3}, "3",
            "4"
        )
)
```

After creating the table:

1. **Mark as Date Table** — right-click DimDate → `Mark as Date Table` → select the `Date` column
2. Create a **relationship** from `FactSales[TransactionDate]` to `DimDate[Date]` (many-to-one)
3. **Hide the Date column** in FactSales to encourage users to filter via DimDate
4. **Sort** `MonthName` by `MonthNumber` in the column properties

> **Exam Tip:** A proper Date table must have: (1) a contiguous range of dates with no gaps, (2) one row per day, (3) be marked as a Date table. The `CALENDAR()` or `CALENDARAUTO()` functions are the standard approach for generating date tables in DAX.

---

### Question 6 — DAX Measures for Sales Analysis

**The merchandising team needs the following metrics on their dashboard:**
1. **Total Revenue** — Sum of (Quantity × UnitPrice)
2. **Total Profit** — Revenue minus cost (UnitCost from DimProduct)
3. **Profit Margin %** — Profit as a percentage of revenue
4. **Average Transaction Value** — Revenue divided by distinct transaction count

**Write DAX measures for each.**

---

#### ✅ Answer

```dax
Total Revenue =
SUMX(
    FactSales,
    FactSales[Quantity] * FactSales[UnitPrice]
)
```

```dax
Total Cost =
SUMX(
    FactSales,
    FactSales[Quantity] * RELATED(DimProduct[UnitCost])
)
```

```dax
Total Profit =
[Total Revenue] - [Total Cost]
```

```dax
Profit Margin % =
DIVIDE(
    [Total Profit],
    [Total Revenue],
    0
)
```

```dax
Average Transaction Value =
DIVIDE(
    [Total Revenue],
    DISTINCTCOUNT(FactSales[TransactionID]),
    0
)
```

**Key points:**

- **`SUMX`** is an iterator that evaluates row-by-row — required here because Revenue = Quantity × UnitPrice (a row-level calculation)
- **`RELATED()`** pulls UnitCost from DimProduct across the many-to-one relationship
- **`DIVIDE()`** is preferred over the `/` operator because it handles division by zero gracefully
- **Profit Margin %** should be formatted as a percentage in the measure properties
- Measures are preferred over calculated columns for aggregations because they compute at query time and respect filter context

> **Exam Tip:** Use `SUMX` (iterator) when you need row-level calculations that multiply two columns. Use `SUM` only for simple single-column aggregation. Always use `DIVIDE()` instead of `/` to avoid errors.

---

### Question 7 — Year-over-Year Growth

**The VP of Sales wants to see revenue growth compared to the prior year and wants to display it as both an absolute difference and a percentage.**

**Write DAX measures for prior year revenue, YoY change, and YoY growth %.**

---

#### ✅ Answer

```dax
Revenue Prior Year =
CALCULATE(
    [Total Revenue],
    SAMEPERIODLASTYEAR(DimDate[Date])
)
```

```dax
YoY Revenue Change =
[Total Revenue] - [Revenue Prior Year]
```

```dax
YoY Growth % =
DIVIDE(
    [YoY Revenue Change],
    [Revenue Prior Year],
    BLANK()
)
```

**Alternative using `DATEADD`:**

```dax
Revenue Prior Year (Alt) =
CALCULATE(
    [Total Revenue],
    DATEADD(DimDate[Date], -1, YEAR)
)
```

**For Year-to-Date (YTD):**

```dax
Revenue YTD =
TOTALYTD(
    [Total Revenue],
    DimDate[Date]
)
```

```dax
Revenue YTD Prior Year =
CALCULATE(
    [Revenue YTD],
    SAMEPERIODLASTYEAR(DimDate[Date])
)
```

**Usage on a visual:**

| Month | Total Revenue | Revenue Prior Year | YoY Growth % |
|-------|--------------|-------------------|--------------|
| Jan 2024 | $1,200,000 | $1,050,000 | 14.3% |
| Feb 2024 | $980,000 | $920,000 | 6.5% |

> **Exam Tip:** Time intelligence functions (`SAMEPERIODLASTYEAR`, `DATEADD`, `TOTALYTD`, `DATESYTD`) require a properly marked Date table with a contiguous set of dates. These functions modify the **date filter context** — understand how `CALCULATE` changes context.

---

### Question 8 — Choosing Visuals for Sales Dashboard

**The VP of Sales wants a single-page dashboard showing:**
1. Total revenue, profit, and transaction count as headline numbers
2. Revenue trend over the past 24 months
3. Revenue breakdown by product category
4. A geographic view of sales by state
5. Top 10 stores by revenue

**Which visual types should you use for each requirement?**

---

#### ✅ Answer

| Requirement | Recommended Visual | Rationale |
|-------------|-------------------|-----------|
| Headline KPIs (revenue, profit, count) | **Card** or **Multi-row Card** | Best for displaying single aggregate values prominently |
| Revenue trend (24 months) | **Line Chart** | Shows trends over time; X-axis = Month, Y-axis = Revenue |
| Revenue by category | **Stacked Bar Chart** or **Treemap** | Bar chart for comparison; treemap for proportional view |
| Sales by state (geographic) | **Filled Map** or **Shape Map** | Filled Map for color-coded state-level geographic view |
| Top 10 stores | **Table** or **Horizontal Bar Chart** with Top N filter | Bar chart with visual-level Top N filter on Revenue (descending) |

**Implementing the Top 10 filter:**

1. Add a **Bar Chart** with `StoreName` on Y-axis and `[Total Revenue]` on X-axis
2. Open the **Filters pane** → drag `StoreName` to **visual-level filters**
3. Change filter type to **Top N** → set to **Top 10** by `[Total Revenue]`
4. Sort descending by revenue

**Dashboard layout best practices:**

- Place **KPI cards** at the top of the page
- Position the **line chart** (trend) below the KPIs spanning the full width
- Place the **bar chart**, **map**, and **top stores** in a row below
- Use consistent color palette aligned with Contoso branding
- Add a **slicer** for Year/Quarter to enable interactive filtering

> **Exam Tip:** Match the visual to the analytical purpose: **Cards** for KPIs, **Line charts** for trends over time, **Bar charts** for category comparisons, **Maps** for geographic data, **Tables** for detailed data. The exam tests your ability to choose the appropriate visual type.

---

### Question 9 — Drill-Through for Store Details

**Regional managers want to click on a store in any visual and navigate to a detailed Store Performance page showing that store's revenue, top products, and monthly trend.**

**How should you configure drill-through in Power BI?**

---

#### ✅ Answer

**Step 1: Create the target page**

1. Add a new report page named **"Store Details"**
2. Add visuals: Card (Revenue), Table (Top Products), Line Chart (Monthly Trend)

**Step 2: Configure drill-through**

1. On the **Store Details** page, drag `DimStore[StoreName]` to the **Drill-through filters** well in the Visualizations pane
2. Power BI automatically adds a **Back button** to the page
3. Optionally check **"Keep all filters"** to carry over slicer selections

**Step 3: Test the interaction**

1. Go to the main dashboard page
2. Right-click any data point tied to a store → **Drill through** → **Store Details**
3. The Store Details page opens, filtered to the selected store

**Additional configuration:**

- To pass **multiple drill-through fields**, add additional columns (e.g., `Region`, `StoreType`) to the drill-through well
- Use **cross-report drill-through** if the Store Details page is in a different report — enable this under **File > Options > Report settings**
- Format the **Back button** to be clearly visible (e.g., arrow icon in the top-left corner)

> **Exam Tip:** Drill-through filters the destination page based on the context of the data point you right-click. The drill-through field must exist on the target page. Cross-report drill-through requires both reports to be in the same workspace.

---

### Question 10 — Conditional Formatting

**The merchandising team wants the profit margin column in a table visual to display:**
- **Green** background when margin ≥ 20%
- **Yellow** background when margin is between 10% and 20%
- **Red** background when margin < 10%

**How do you configure this?**

---

#### ✅ Answer

**Method 1: Rules-based conditional formatting (Recommended)**

1. Select the **Table** visual containing the `[Profit Margin %]` measure
2. In the **Format** pane → **Cell elements** → select the `Profit Margin %` column
3. Toggle **Background color** to **On** → click **fx** (conditional formatting)
4. Set **Format style** to **Rules**
5. Configure the rules:

| Rule | Condition | Color |
|------|-----------|-------|
| 1 | Value ≥ 0.20 | 🟢 Green (#00B050) |
| 2 | Value ≥ 0.10 AND < 0.20 | 🟡 Yellow (#FFC000) |
| 3 | Value < 0.10 | 🔴 Red (#FF0000) |

**Method 2: DAX measure for color (Alternative)**

```dax
Margin Color =
SWITCH(
    TRUE(),
    [Profit Margin %] >= 0.20, "#00B050",
    [Profit Margin %] >= 0.10, "#FFC000",
    "#FF0000"
)
```

Then apply conditional formatting → **Format style: Field value** → select `[Margin Color]`.

> **Exam Tip:** Rules-based conditional formatting is configured in the visual format pane and does not require DAX. The DAX-based approach (field value) gives more flexibility for complex logic. Know both methods for the exam.

---

### Question 11 — Row-Level Security for Regional Managers

**Each of the 4 regional managers should only see data for stores in their own region. The manager's email address matches their Power BI login (e.g., `northeast_mgr@contoso.com`).**

**How do you implement Row-Level Security (RLS)?**

---

#### ✅ Answer

**Step 1: Create a security mapping table**

Create a table (or use existing DimStore columns) that maps user email to region:

| ManagerEmail | Region |
|-------------|--------|
| northeast_mgr@contoso.com | Northeast |
| southeast_mgr@contoso.com | Southeast |
| midwest_mgr@contoso.com | Midwest |
| west_mgr@contoso.com | West |

If using the existing `DimStore` table, the `ManagerEmail` column already contains this mapping.

**Step 2: Define RLS roles in Power BI Desktop**

1. Go to **Modeling** tab → **Manage Roles**
2. Create a role named **"Regional Manager"**
3. Add a DAX filter on the **DimStore** table:

```dax
[ManagerEmail] = USERPRINCIPALNAME()
```

This filter restricts DimStore rows to only those where the ManagerEmail matches the logged-in user. Because FactSales has a relationship to DimStore, the filter propagates automatically.

**Step 3: Test in Power BI Desktop**

1. **Modeling** tab → **View as** → check the **"Regional Manager"** role
2. Optionally enter a test email in **"Other user"** field
3. Verify that only the correct region's data appears

**Step 4: Assign users in the Power BI service**

1. Open the dataset in the Power BI service
2. Go to **Security** settings
3. Add each manager's email to the **"Regional Manager"** role

> **Exam Tip:** `USERPRINCIPALNAME()` returns the email of the currently logged-in user. RLS filters on a dimension table automatically propagate to related fact tables via relationships. Always test RLS using "View as Role" before publishing. The VP of Sales should **not** be added to any RLS role so they see all data.

---

### Question 12 — Workspace and App Deployment

**Contoso wants to deploy the reports with the following access:**
- VP of Sales and Finance team: Full access to all reports
- Regional Managers: Access to the dashboard with RLS applied
- Merchandising team: Access to product-specific reports only

**How should you configure workspaces and the Power BI app?**

---

#### ✅ Answer

**Workspace configuration:**

1. Create a workspace named **"Contoso Retail Analytics"**
2. Assign workspace roles:

| User/Group | Workspace Role | Justification |
|-----------|---------------|---------------|
| BI Developer | **Admin** | Full control including workspace settings |
| VP of Sales | **Member** | Can edit and publish content |
| Finance Team | **Viewer** | View-only access; RLS not needed (sees all data) |

**App configuration:**

1. Click **Create app** from the workspace
2. Configure **audience groups**:

| Audience | Included Content | Users |
|----------|-----------------|-------|
| **Executive** | All reports and dashboards | VP of Sales, Finance Team |
| **Regional** | Sales dashboard only | Regional Managers (with RLS) |
| **Merchandising** | Product performance reports | Merchandising team |

**Key considerations:**

- **App audiences** control which content each group sees within the app
- **RLS** controls which data each user sees within a report — it works independently of workspace roles
- Regional Managers should be added to the **RLS role** in the dataset security settings AND given app access
- Use **Azure AD security groups** for easier management instead of individual email assignments
- Enable **automatic installation** for the app so users find it in their Apps section

> **Exam Tip:** Workspace roles (Admin, Member, Contributor, Viewer) control access to the workspace. App audiences control which reports users see in the published app. RLS controls row-level data visibility. These three layers work together. Know the permissions of each workspace role.

---

## 5. Key Exam Concepts Summary

| Concept | Domain | Key Takeaway |
|---------|--------|-------------|
| **Gateway configuration** | Prepare Data | On-premises sources require a gateway; cloud sources do not |
| **Data profiling** | Prepare Data | Use Column Quality, Column Distribution, Column Profile on the full dataset |
| **Query folding** | Prepare Data | Filter early, verify with "View Native Query" |
| **Star schema** | Model Data | Fact tables connect to shared dimensions via many-to-one relationships |
| **Date table** | Model Data | Must be marked, contiguous, one row per day |
| **SUMX vs SUM** | Model Data | Use SUMX for row-level calculations; SUM for single-column aggregation |
| **DIVIDE()** | Model Data | Always use over `/` operator to handle division by zero |
| **Time intelligence** | Model Data | Requires a marked Date table; uses CALCULATE to modify filter context |
| **Visual selection** | Visualize | Match visual type to analytical purpose (trend, comparison, composition) |
| **Drill-through** | Visualize | Configure on target page; filters propagate from source data point |
| **Conditional formatting** | Visualize | Rules-based (UI) or field-value (DAX measure) approaches |
| **Row-Level Security** | Deploy & Maintain | USERPRINCIPALNAME() + dimension filter; propagates via relationships |
| **Workspace roles** | Deploy & Maintain | Admin > Member > Contributor > Viewer permissions hierarchy |
| **App audiences** | Deploy & Maintain | Control which content each user group sees in the published app |
