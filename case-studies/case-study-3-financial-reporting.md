# Case Study 3: Adventure Works Finance — Financial Reporting & Budgeting

> 📋 **Exam Domains Covered:** Prepare the Data | Model the Data | Visualize & Analyze | Deploy & Maintain
> 🕐 **Estimated Study Time:** 90 minutes | **Questions:** 12 scenario-based

---

## Table of Contents

1. [Company Background](#1-company-background)
2. [Data Sources and Description](#2-data-sources-and-description)
3. [Data Model Overview](#3-data-model-overview)
4. [Scenario Questions](#4-scenario-questions)
   - [Q1 – Connecting to Financial Databases](#question-1--connecting-to-financial-databases)
   - [Q2 – Handling Currency Conversion](#question-2--handling-currency-conversion)
   - [Q3 – Transforming Chart of Accounts Hierarchy](#question-3--transforming-chart-of-accounts-hierarchy)
   - [Q4 – Modeling Multiple Fact Tables](#question-4--modeling-multiple-fact-tables)
   - [Q5 – Budget vs Actual Variance Measures](#question-5--budget-vs-actual-variance-measures)
   - [Q6 – Time Intelligence: YTD, QTD, Prior Year](#question-6--time-intelligence-ytd-qtd-prior-year)
   - [Q7 – Cumulative Budget Allocation](#question-7--cumulative-budget-allocation)
   - [Q8 – Financial Dashboard with Variance Analysis](#question-8--financial-dashboard-with-variance-analysis)
   - [Q9 – Matrix Visual with Conditional Formatting](#question-9--matrix-visual-with-conditional-formatting)
   - [Q10 – Paginated Reports for Formal Statements](#question-10--paginated-reports-for-formal-statements)
   - [Q11 – Deployment Pipeline and Workspaces](#question-11--deployment-pipeline-and-workspaces)
   - [Q12 – Row-Level Security by Cost Center](#question-12--row-level-security-by-cost-center)
5. [Key Exam Concepts Summary](#5-key-exam-concepts-summary)

---

## 1. Company Background

**Adventure Works Finance** is the finance division of Adventure Works, a multinational manufacturing and distribution company. The company operates in **4 countries**: United States (headquarters), United Kingdom, Germany, and Australia.

**Key financials:**

- Annual revenue: ~$500 million
- 12 cost centers across 4 countries
- Fiscal year: July 1 – June 30
- Reporting currencies: USD (primary), local currencies for each country

**Key business stakeholders:**

- **Chief Financial Officer (CFO)** — Needs consolidated financial overview across all entities
- **Regional Finance Directors (4)** — Manage budgets for their country
- **Cost Center Managers (12)** — Track spending against approved budgets
- **Financial Planning & Analysis (FP&A)** — Builds forecasts and variance analysis
- **External Auditors** — Require formatted financial statements (paginated reports)

**Business requirements:**

- Monthly budget vs. actual comparison at the cost center level
- Currency conversion to USD for consolidated reporting
- Time intelligence: YTD, QTD, prior year comparisons, and rolling 12-month trends
- Formal financial statements in paginated report format for board meetings
- Strict access controls — each cost center manager sees only their data

---

## 2. Data Sources and Description

| Source | System | Format | Refresh Frequency | Approximate Size |
|--------|--------|--------|--------------------|------------------|
| **General Ledger (Actuals)** | SAP via Azure Synapse | Relational views | Daily | ~2 million journal entries/year |
| **Budget Data** | Excel workbook | `.xlsx` on SharePoint | Monthly (revised quarterly) |  ~15,000 rows |
| **Chart of Accounts** | SQL Server (on-premises) | Relational table | Rarely changes | ~500 accounts |
| **Cost Center Master** | SQL Server (on-premises) | Relational table | Rarely changes | 12 rows |
| **Exchange Rates** | REST API (European Central Bank) | JSON | Daily | ~365 rows/year per currency |
| **Fiscal Calendar** | Custom Excel | `.xlsx` | Annual | ~366 rows |

### General Ledger Actuals (FactActuals)

| Column | Data Type | Description |
|--------|-----------|-------------|
| JournalEntryID | Integer | Primary key |
| PostingDate | Date | Date the entry was posted |
| AccountID | Text | Foreign key to DimAccount (e.g., "4100") |
| CostCenterID | Text | Foreign key to DimCostCenter (e.g., "CC-US-01") |
| Amount_Local | Decimal | Amount in local currency |
| CurrencyCode | Text | ISO currency code (USD, GBP, EUR, AUD) |
| Description | Text | Journal entry description |
| Source | Text | Sub-ledger source (AP, AR, GL, FA) |

### Budget Data (FactBudget)

| Column | Data Type | Description |
|--------|-----------|-------------|
| BudgetLineID | Integer | Primary key |
| FiscalYear | Integer | Budget year |
| FiscalMonth | Integer | Month number (1-12, where 1 = July) |
| AccountID | Text | Foreign key to DimAccount |
| CostCenterID | Text | Foreign key to DimCostCenter |
| BudgetAmount_USD | Decimal | Budget in USD |
| Version | Text | "Original", "Revised-Q1", "Revised-Q2", etc. |

### Chart of Accounts (DimAccount)

| Column | Data Type | Description |
|--------|-----------|-------------|
| AccountID | Text | Primary key (e.g., "4100") |
| AccountName | Text | Display name (e.g., "Product Revenue") |
| AccountType | Text | "Revenue", "COGS", "OpEx", "OtherIncome", "OtherExpense" |
| AccountGroup | Text | Grouping (e.g., "Sales Revenue", "Cost of Materials") |
| ParentAccountID | Text | Self-referencing for hierarchy |
| IsDetail | Boolean | True if lowest-level posting account |

### Cost Center Master (DimCostCenter)

| Column | Data Type | Description |
|--------|-----------|-------------|
| CostCenterID | Text | Primary key (e.g., "CC-US-01") |
| CostCenterName | Text | Display name |
| Country | Text | US, UK, DE, AU |
| RegionDirectorEmail | Text | Email of the regional finance director |
| ManagerEmail | Text | Email of the cost center manager |
| CurrencyCode | Text | Local currency for this cost center |

### Exchange Rates (DimExchangeRate)

| Column | Data Type | Description |
|--------|-----------|-------------|
| RateDate | Date | Date of the exchange rate |
| FromCurrency | Text | Source currency (e.g., "EUR") |
| ToCurrency | Text | Target currency ("USD") |
| ExchangeRate | Decimal | Conversion rate |

---

## 3. Data Model Overview

This model has **two fact tables** (Actuals and Budget) sharing the same dimensions.

```
                    ┌──────────────────┐
                    │    DimDate       │
                    │──────────────────│
                    │ Date (PK)        │
                    │ FiscalYear       │
                    │ FiscalMonth      │
                    │ FiscalQuarter    │
                    │ CalendarYear     │
                    │ CalendarMonth    │
                    │ MonthName        │
                    │ IsCurrent        │
                    └────────┬─────────┘
                             │ 1
              ┌──────────────┤
              │              │
     ┌────────┴────────┐  ┌─┴───────────────┐
     │  FactBudget     │  │  FactActuals    │
     │─────────────────│  │─────────────────│
     │BudgetLineID(PK) │  │JournalEntryID   │
     │FiscalYear       │  │PostingDate      │──→ DimDate
     │FiscalMonth      │  │AccountID        │──→ DimAccount
     │AccountID (FK)   │  │CostCenterID     │──→ DimCostCenter
     │CostCenterID(FK) │  │Amount_Local     │
     │BudgetAmount_USD │  │CurrencyCode     │──→ DimExchangeRate
     │Version          │  │Description      │
     └─────┬──────┬────┘  │Source           │
           │      │       └──┬──────────────┘
           │      │          │
     ┌─────┴──────┴──────────┴──────┐
     │        DimAccount            │
     │──────────────────────────────│
     │ AccountID (PK)               │
     │ AccountName                  │
     │ AccountType                  │
     │ AccountGroup                 │
     │ ParentAccountID (self-ref)   │
     └──────────────────────────────┘

     ┌──────────────────┐    ┌──────────────────────┐
     │ DimCostCenter    │    │  DimExchangeRate     │
     │──────────────────│    │──────────────────────│
     │CostCenterID (PK) │    │ RateDate             │
     │CostCenterName    │    │ FromCurrency         │
     │Country           │    │ ToCurrency           │
     │ManagerEmail      │    │ ExchangeRate         │
     │CurrencyCode      │    └──────────────────────┘
     └──────────────────┘
```

**Relationship summary:**

| From (Many) | To (One) | Key Column | Cardinality |
|-------------|----------|------------|-------------|
| FactActuals | DimDate | PostingDate → Date | Many-to-One |
| FactActuals | DimAccount | AccountID | Many-to-One |
| FactActuals | DimCostCenter | CostCenterID | Many-to-One |
| FactBudget | DimAccount | AccountID | Many-to-One |
| FactBudget | DimCostCenter | CostCenterID | Many-to-One |
| FactBudget | DimDate | (via FiscalYear + FiscalMonth key) | Many-to-One |

> **Exam Tip:** Financial models often have multiple fact tables (actuals, budget, forecast) sharing the same dimensions. Each fact table must have its own relationship to each shared dimension. This is a valid star schema — not a snowflake.

---

## 4. Scenario Questions

---

### Question 1 — Connecting to Financial Databases

**Adventure Works Finance needs to connect to:**
1. **Azure Synapse Analytics** for the general ledger (2M+ rows, refreshed daily)
2. **On-premises SQL Server** for chart of accounts and cost center master
3. **REST API** (European Central Bank) for daily exchange rates
4. **Excel on SharePoint** for budget data

**What connectivity modes and configurations should be used? Should you use Import or DirectQuery for Synapse?**

---

#### ✅ Answer

| Source | Connector | Mode | Gateway |
|--------|-----------|------|---------|
| Azure Synapse | Azure Synapse Analytics connector | **Import** | No |
| On-premises SQL Server | SQL Server connector | Import | **Yes — On-premises data gateway** |
| REST API (exchange rates) | Web connector / OData | Import | No (if public) |
| Excel on SharePoint | SharePoint folder / Excel connector | Import | No |

**Import vs. DirectQuery for Synapse — Analysis:**

| Factor | Import | DirectQuery |
|--------|--------|-------------|
| Query performance | ✅ Fast (in-memory) | ⚠️ Depends on Synapse query speed |
| Data freshness | Daily refresh is acceptable | Near-real-time |
| Model size | ~2M rows is well within Import limits | No size concern |
| DAX complexity | ✅ Full DAX support | ⚠️ Some DAX may be slow |
| Cost | Lower (data cached in Power BI) | Higher (each visual fires a Synapse query) |

**Recommendation: Import mode** — 2M rows is manageable, daily refresh meets requirements, and complex DAX calculations (currency conversion, time intelligence) perform better in Import mode.

**Connecting to the REST API for exchange rates:**

```m
let
    Source = Json.Document(
        Web.Contents("https://api.exchangerate.host/timeseries", [
            Query = [
                start_date = "2023-01-01",
                end_date = "2024-12-31",
                base = "USD",
                symbols = "GBP,EUR,AUD"
            ]
        ])
    ),
    Rates = Source[rates],
    ConvertedToTable = Record.ToTable(Rates)
in
    ConvertedToTable
```

> **Exam Tip:** Choose Import mode when: data volume is manageable, complex DAX is needed, refresh frequency is acceptable. Choose DirectQuery when: near-real-time data is required, data volume exceeds model limits, or source system already optimizes queries. Import is the default recommendation for financial reporting.

---

### Question 2 — Handling Currency Conversion

**All financial reporting must be in USD. The FactActuals table contains amounts in local currencies (USD, GBP, EUR, AUD). Exchange rates are available in the DimExchangeRate table.**

**How do you convert all amounts to USD?**

---

#### ✅ Answer

**Option A: Convert in Power Query (Recommended for static rates)**

```m
let
    Source = FactActuals,
    MergedRates = Table.NestedJoin(
        Source, {"CurrencyCode", "PostingDate"},
        DimExchangeRate, {"FromCurrency", "RateDate"},
        "ExRate", JoinKind.LeftOuter
    ),
    ExpandedRate = Table.ExpandTableColumn(MergedRates, "ExRate", {"ExchangeRate"}),
    AddUSD = Table.AddColumn(ExpandedRate, "Amount_USD", each
        if [CurrencyCode] = "USD" then [Amount_Local]
        else [Amount_Local] * [ExchangeRate],
        type number
    )
in
    AddUSD
```

**Option B: Convert in DAX (Recommended for dynamic rate selection)**

```dax
Amount USD =
VAR CurrentRate =
    LOOKUPVALUE(
        DimExchangeRate[ExchangeRate],
        DimExchangeRate[FromCurrency], FactActuals[CurrencyCode],
        DimExchangeRate[RateDate], FactActuals[PostingDate]
    )
RETURN
    IF(
        FactActuals[CurrencyCode] = "USD",
        FactActuals[Amount_Local],
        FactActuals[Amount_Local] * CurrentRate
    )
```

**As a measure (for total converted amount):**

```dax
Total Actuals USD =
SUMX(
    FactActuals,
    VAR CurrentRate =
        LOOKUPVALUE(
            DimExchangeRate[ExchangeRate],
            DimExchangeRate[FromCurrency], FactActuals[CurrencyCode],
            DimExchangeRate[RateDate], FactActuals[PostingDate]
        )
    RETURN
        IF(
            FactActuals[CurrencyCode] = "USD",
            FactActuals[Amount_Local],
            FactActuals[Amount_Local] * COALESCE(CurrentRate, 1)
        )
)
```

**Handling missing exchange rates:**

- `COALESCE(CurrentRate, 1)` defaults to a rate of 1 if no rate is found
- For a more robust approach, use the most recent available rate:

```dax
Latest Available Rate =
CALCULATE(
    LASTNONBLANK(DimExchangeRate[ExchangeRate], 1),
    FILTER(
        ALL(DimExchangeRate),
        DimExchangeRate[FromCurrency] = FactActuals[CurrencyCode] &&
        DimExchangeRate[RateDate] <= FactActuals[PostingDate]
    )
)
```

> **Exam Tip:** Currency conversion can be done in Power Query (at load time) or in DAX (at query time). Power Query is better for static conversion; DAX is better when users need to toggle between currencies or when rates change frequently. `LOOKUPVALUE` performs a point lookup and does not require a relationship.

---

### Question 3 — Transforming Chart of Accounts Hierarchy

**The DimAccount table has a parent-child hierarchy (ParentAccountID references AccountID). The finance team needs to see accounts organized as:**
- **Level 1:** Account Type (Revenue, COGS, OpEx)
- **Level 2:** Account Group (Sales Revenue, Service Revenue, Cost of Materials)
- **Level 3:** Detail Account (Product Revenue - North America)

**How do you flatten this hierarchy for use in Power BI visuals?**

---

#### ✅ Answer

**Step 1: Use PATH functions in DAX**

```dax
AccountPath = PATH(DimAccount[AccountID], DimAccount[ParentAccountID])
```

```dax
AccountDepth = PATHLENGTH(DimAccount[AccountPath])
```

```dax
Level1AccountID = PATHITEM(DimAccount[AccountPath], 1, TEXT)
```

```dax
Level2AccountID = PATHITEM(DimAccount[AccountPath], 2, TEXT)
```

```dax
Level3AccountID = PATHITEM(DimAccount[AccountPath], 3, TEXT)
```

**Step 2: Add account names for each level**

```dax
Level1Name =
LOOKUPVALUE(
    DimAccount[AccountName],
    DimAccount[AccountID],
    DimAccount[Level1AccountID]
)
```

```dax
Level2Name =
IF(
    ISBLANK(DimAccount[Level2AccountID]),
    BLANK(),
    LOOKUPVALUE(
        DimAccount[AccountName],
        DimAccount[AccountID],
        DimAccount[Level2AccountID]
    )
)
```

```dax
Level3Name =
IF(
    ISBLANK(DimAccount[Level3AccountID]),
    BLANK(),
    LOOKUPVALUE(
        DimAccount[AccountName],
        DimAccount[AccountID],
        DimAccount[Level3AccountID]
    )
)
```

**Step 3: Create the visual hierarchy**

1. In the Fields pane, drag `Level1Name`, `Level2Name`, `Level3Name` into a **hierarchy**
2. Use this hierarchy in a **Matrix** visual for drill-down financial statements

**Alternative: Flatten in Power Query**

```m
let
    Source = DimAccount,
    SelfJoin1 = Table.NestedJoin(Source, {"ParentAccountID"}, Source, {"AccountID"}, "Parent", JoinKind.LeftOuter),
    ExpandParent = Table.ExpandTableColumn(SelfJoin1, "Parent", {"AccountName", "ParentAccountID"}, {"ParentName", "GrandParentID"}),
    SelfJoin2 = Table.NestedJoin(ExpandParent, {"GrandParentID"}, Source, {"AccountID"}, "GrandParent", JoinKind.LeftOuter),
    ExpandGrandParent = Table.ExpandTableColumn(SelfJoin2, "GrandParent", {"AccountName"}, {"GrandParentName"})
in
    ExpandGrandParent
```

> **Exam Tip:** Parent-child hierarchies in financial data are common. Use `PATH()`, `PATHITEM()`, and `LOOKUPVALUE()` to flatten them into separate level columns. These columns can then be combined into a Power BI hierarchy for drill-down. The `PATHITEM` function uses 1-based indexing (1 = root).

---

### Question 4 — Modeling Multiple Fact Tables

**The data model has two fact tables (FactActuals and FactBudget) that share DimAccount and DimCostCenter. However, FactBudget uses FiscalYear + FiscalMonth instead of a date column for the time dimension.**

**How should you connect FactBudget to the Date dimension?**

---

#### ✅ Answer

**Approach: Create a FiscalPeriodKey in both tables**

**Step 1: Add a key column to DimDate**

```dax
FiscalPeriodKey = DimDate[FiscalYear] * 100 + DimDate[FiscalMonth]
```

**Step 2: Add the same key to FactBudget**

In Power Query:

```m
= Table.AddColumn(FactBudget, "FiscalPeriodKey", each [FiscalYear] * 100 + [FiscalMonth], Int64.Type)
```

**Step 3: Create a FiscalPeriod bridge table**

Since DimDate has one row per day but FactBudget has one row per fiscal month, create a bridge:

```dax
DimFiscalPeriod =
SUMMARIZE(
    DimDate,
    DimDate[FiscalYear],
    DimDate[FiscalMonth],
    DimDate[FiscalPeriodKey],
    DimDate[FiscalQuarter]
)
```

**Step 4: Set up relationships**

| From | To | Cardinality |
|------|----|-------------|
| DimDate[FiscalPeriodKey] | DimFiscalPeriod[FiscalPeriodKey] | Many-to-One |
| FactBudget[FiscalPeriodKey] | DimFiscalPeriod[FiscalPeriodKey] | Many-to-One |

**This allows:**
- FactActuals to connect to DimDate by PostingDate (daily granularity)
- FactBudget to connect to DimFiscalPeriod (monthly granularity)
- Both fact tables filter when users select a fiscal year, quarter, or month from DimDate

**Alternative simpler approach:**

Add a date column to FactBudget representing the first day of the fiscal month:

```m
= Table.AddColumn(FactBudget, "BudgetDate", each
    Date.AddMonths(#date([FiscalYear] - (if [FiscalMonth] <= 6 then 1 else 0), 7, 1), [FiscalMonth] - 1),
    type date
)
```

Then create a standard relationship: `FactBudget[BudgetDate]` → `DimDate[Date]` (many-to-one).

> **Exam Tip:** When fact tables have different granularities (daily actuals vs. monthly budget), you need to handle the date dimension carefully. Options include: (1) a bridge/summary date table, (2) adding a date column to the lower-granularity fact table, or (3) using inactive relationships with USERELATIONSHIP. The exam may test your ability to choose the right approach.

---

### Question 5 — Budget vs Actual Variance Measures

**FP&A needs the following measures:**
1. **Total Actuals** — sum of actual amounts in USD
2. **Total Budget** — sum of budget amounts (already in USD)
3. **Variance** — Actuals minus Budget
4. **Variance %** — Variance as a percentage of Budget
5. **Favorable/Unfavorable indicator** — for revenue, over-budget is favorable; for expenses, under-budget is favorable

---

#### ✅ Answer

```dax
Total Actuals =
[Total Actuals USD]
// Uses the currency-converted measure from Q2
```

```dax
Total Budget =
SUM(FactBudget[BudgetAmount_USD])
```

```dax
Variance =
[Total Actuals] - [Total Budget]
```

```dax
Variance % =
DIVIDE(
    [Variance],
    [Total Budget],
    BLANK()
)
```

**Favorable/Unfavorable logic:**

For financial reporting, whether a positive variance is good depends on the account type:
- **Revenue**: Actuals > Budget = Favorable (positive variance is good)
- **Expenses (COGS, OpEx)**: Actuals < Budget = Favorable (negative variance is good)

```dax
Variance Indicator =
VAR Var = [Variance]
VAR AccountType =
    SELECTEDVALUE(DimAccount[AccountType])
RETURN
SWITCH(
    TRUE(),
    // Revenue: positive variance is favorable
    AccountType = "Revenue" && Var > 0, "Favorable",
    AccountType = "Revenue" && Var < 0, "Unfavorable",
    // Expenses: negative variance is favorable
    AccountType IN {"COGS", "OpEx", "OtherExpense"} && Var < 0, "Favorable",
    AccountType IN {"COGS", "OpEx", "OtherExpense"} && Var > 0, "Unfavorable",
    "On Target"
)
```

**Variance with sign convention (expenses shown as positive):**

```dax
Variance Adjusted =
VAR Var = [Variance]
VAR AccountType = SELECTEDVALUE(DimAccount[AccountType])
RETURN
IF(
    AccountType IN {"COGS", "OpEx", "OtherExpense"},
    -Var,   // Flip sign so positive = favorable for expenses
    Var
)
```

**Net Income measure:**

```dax
Net Income Actuals =
CALCULATE([Total Actuals], DimAccount[AccountType] = "Revenue")
+ CALCULATE([Total Actuals], DimAccount[AccountType] = "OtherIncome")
- CALCULATE([Total Actuals], DimAccount[AccountType] = "COGS")
- CALCULATE([Total Actuals], DimAccount[AccountType] = "OpEx")
- CALCULATE([Total Actuals], DimAccount[AccountType] = "OtherExpense")
```

> **Exam Tip:** Budget vs. actual variance analysis is a core finance use case. The sign convention matters — revenue over-budget is favorable, but expense over-budget is unfavorable. Use `SELECTEDVALUE` to determine context (e.g., which account type is selected). `SWITCH(TRUE(), ...)` is a powerful pattern for multi-condition logic.

---

### Question 6 — Time Intelligence: YTD, QTD, Prior Year

**FP&A needs the following time-based measures:**
1. **Actuals YTD** (fiscal year-to-date, starting July 1)
2. **Budget YTD**
3. **Actuals QTD** (quarter-to-date)
4. **Prior Year Actuals** (same period last year)
5. **YoY Variance** (current year vs. prior year)
6. **Rolling 12-Month Actuals**

---

#### ✅ Answer

**Fiscal YTD (July 1 start):**

```dax
Actuals YTD =
TOTALYTD(
    [Total Actuals],
    DimDate[Date],
    "6/30"   // Fiscal year ends June 30
)
```

```dax
Budget YTD =
TOTALYTD(
    [Total Budget],
    DimDate[Date],
    "6/30"
)
```

**Quarter-to-Date:**

```dax
Actuals QTD =
TOTALQTD(
    [Total Actuals],
    DimDate[Date]
)
```

> **Note:** `TOTALQTD` uses calendar quarters by default. For fiscal quarters, use `CALCULATE` + `DATESQTD` with adjustments, or filter by fiscal quarter columns.

**Fiscal QTD alternative:**

```dax
Actuals Fiscal QTD =
CALCULATE(
    [Total Actuals],
    FILTER(
        ALL(DimDate),
        DimDate[FiscalYear] = MAX(DimDate[FiscalYear]) &&
        DimDate[FiscalQuarter] = MAX(DimDate[FiscalQuarter]) &&
        DimDate[Date] <= MAX(DimDate[Date])
    )
)
```

**Prior Year Actuals:**

```dax
Actuals Prior Year =
CALCULATE(
    [Total Actuals],
    SAMEPERIODLASTYEAR(DimDate[Date])
)
```

```dax
Actuals YTD Prior Year =
CALCULATE(
    [Actuals YTD],
    SAMEPERIODLASTYEAR(DimDate[Date])
)
```

**Year-over-Year Variance:**

```dax
YoY Variance =
[Total Actuals] - [Actuals Prior Year]
```

```dax
YoY Variance % =
DIVIDE(
    [YoY Variance],
    [Actuals Prior Year],
    BLANK()
)
```

**Rolling 12 Months:**

```dax
Rolling 12M Actuals =
CALCULATE(
    [Total Actuals],
    DATESINPERIOD(
        DimDate[Date],
        MAX(DimDate[Date]),
        -12,
        MONTH
    )
)
```

> **Exam Tip:** `TOTALYTD` accepts a year-end date parameter (`"6/30"`) for non-calendar fiscal years. `SAMEPERIODLASTYEAR` shifts the date filter back exactly one year. `DATESINPERIOD` creates a rolling window of dates. All time intelligence functions require a properly marked, contiguous Date table.

---

### Question 7 — Cumulative Budget Allocation

**Budget data is entered at the monthly level. FP&A wants to see cumulative budget (running total) across months within each fiscal year, resetting at the start of each new fiscal year.**

**Write a DAX measure for the cumulative budget.**

---

#### ✅ Answer

```dax
Cumulative Budget =
CALCULATE(
    [Total Budget],
    FILTER(
        ALL(DimDate),
        DimDate[FiscalYear] = MAX(DimDate[FiscalYear]) &&
        DimDate[Date] <= MAX(DimDate[Date])
    )
)
```

**Alternative using DATESYTD with fiscal year end:**

```dax
Cumulative Budget (Alt) =
CALCULATE(
    [Total Budget],
    DATESYTD(DimDate[Date], "6/30")
)
```

**For a running total that does NOT reset by fiscal year:**

```dax
Running Total Budget =
CALCULATE(
    [Total Budget],
    FILTER(
        ALL(DimDate),
        DimDate[Date] <= MAX(DimDate[Date])
    )
)
```

**Display in a visual:**

| Fiscal Month | Monthly Budget | Cumulative Budget |
|-------------|---------------|-------------------|
| Jul (M1) | $500,000 | $500,000 |
| Aug (M2) | $480,000 | $980,000 |
| Sep (M3) | $520,000 | $1,500,000 |
| Oct (M4) | $510,000 | $2,010,000 |

**Cumulative Variance (Actuals YTD vs Budget YTD):**

```dax
Cumulative Variance =
[Actuals YTD] - [Cumulative Budget]
```

```dax
Cumulative Variance % =
DIVIDE(
    [Cumulative Variance],
    [Cumulative Budget],
    BLANK()
)
```

> **Exam Tip:** Running totals in DAX use `CALCULATE` with a filter that includes all dates up to the current selection. `DATESYTD` with a year-end parameter is the cleanest approach for fiscal year cumulative totals. The pattern of `FILTER(ALL(DimDate), ...)` removes existing date filters and applies a new cumulative range.

---

### Question 8 — Financial Dashboard with Variance Analysis

**The CFO wants a single dashboard page showing:**
1. High-level KPIs: Total Revenue, Total Expenses, Net Income, Budget Variance
2. Monthly trend: Actuals vs Budget with variance overlay
3. Variance breakdown by cost center
4. Income statement summary (Revenue - COGS = Gross Profit - OpEx = Net Income)
5. Ability to switch between fiscal years

**Recommend the visual types and layout.**

---

#### ✅ Answer

| Requirement | Visual Type | Configuration |
|-------------|------------|---------------|
| KPI metrics | **Card** or **KPI visual** | Revenue, Expenses, Net Income with variance arrows |
| Monthly trend | **Combo chart** (Line + Clustered Column) | Columns = Actuals & Budget; Line = Variance % |
| Variance by cost center | **Waterfall chart** | Shows positive and negative contributions |
| Income statement | **Matrix visual** | Rows = Account hierarchy; Columns = Months |
| Year switcher | **Slicer** | Fiscal Year dropdown |

**Layout:**

```
┌─────────────────────────────────────────────────────────┐
│  [Slicer: Fiscal Year ▼]  [Slicer: Budget Version ▼]   │
├──────────────┬──────────────┬──────────────┬────────────┤
│ Card:        │ Card:        │ Card:        │ Card:      │
│ Revenue      │ Expenses     │ Net Income   │ Variance % │
│ $125M        │ $98M         │ $27M         │ +3.2%      │
├──────────────┴──────────────┴──────────────┴────────────┤
│                                                         │
│  Combo Chart: Monthly Actuals vs Budget                 │
│  ████ ████ ████ ████ ████ ████ ████ ████ ████ ████     │
│  ──────────── Variance % trend line ───────────────     │
│                                                         │
├───────────────────────────┬─────────────────────────────┤
│                           │                             │
│  Waterfall Chart:         │  Matrix: Income Statement   │
│  Variance by Cost Center  │  Revenue                    │
│  █ ▄ █ ▂ █ ▃ █ ▅ █       │    - COGS                   │
│                           │  = Gross Profit             │
│                           │    - OpEx                   │
│                           │  = Net Income               │
└───────────────────────────┴─────────────────────────────┘
```

**Combo chart configuration:**

- **Y-axis (columns):** `[Total Actuals]` and `[Total Budget]`
- **Y-axis (line):** `[Variance %]` on secondary axis
- **X-axis:** `DimDate[MonthName]` sorted by `MonthNumber`
- Use different colors for Actuals (blue) and Budget (grey dashed)

**Waterfall chart configuration:**

- **Category:** `DimCostCenter[CostCenterName]`
- **Values:** `[Variance]`
- The waterfall naturally shows positive (favorable) and negative (unfavorable) variances
- Green for positive, red for negative (configure in format pane)

**Matrix for Income Statement:**

- **Rows:** Account hierarchy (Level1 → Level2 → Level3)
- **Columns:** Fiscal Month or Fiscal Quarter
- **Values:** `[Total Actuals]`, `[Total Budget]`, `[Variance]`
- Enable **stepped layout** for hierarchy indentation
- Use **subtotals** for group-level aggregation

> **Exam Tip:** Combo charts (line + column) are ideal for comparing two measures (actuals vs budget) with a third trend line (variance %). Waterfall charts show how individual contributions lead to a total. Matrix visuals with hierarchies are essential for financial statements.

---

### Question 9 — Matrix Visual with Conditional Formatting

**The FP&A team wants the income statement matrix to show:**
1. Variance values with **red** font for unfavorable and **green** for favorable
2. **Data bars** on the Actuals column to show relative magnitude
3. **Icons** (arrows) on the Variance % column

**How do you configure this?**

---

#### ✅ Answer

**Step 1: Conditional formatting for Variance font color**

1. Select the Matrix visual
2. **Format** pane → **Cell elements** → select `[Variance]` column
3. Toggle **Font color** → click **fx**
4. Configure:
   - **Format style:** Rules
   - Rule 1: If value ≥ 0 → Green (#00B050)
   - Rule 2: If value < 0 → Red (#FF0000)

**Step 2: Data bars for Actuals**

1. **Format** pane → **Cell elements** → select `[Total Actuals]` column
2. Toggle **Data bars** → click **fx**
3. Configure:
   - Positive bar color: Blue (#4472C4)
   - Show bar only (hide values if desired, or show both)

**Step 3: Icons for Variance %**

1. **Format** pane → **Cell elements** → select `[Variance %]` column
2. Toggle **Icons** → click **fx**
3. Configure:
   - **Icon style:** Arrows (or triangles)
   - Rule 1: If value ≥ 0.05 → Green up arrow ↑
   - Rule 2: If value ≥ -0.05 and < 0.05 → Yellow right arrow →
   - Rule 3: If value < -0.05 → Red down arrow ↓
   - **Icon position:** Left of data

**DAX-based approach for more control:**

```dax
Variance Color =
IF(
    [Variance] >= 0,
    "#00B050",  // Green
    "#FF0000"   // Red
)
```

Then use **Format style: Field value** → select `[Variance Color]` measure.

**Account-type-aware coloring:**

```dax
Variance Color Smart =
VAR AccountType = SELECTEDVALUE(DimAccount[AccountType])
VAR Var = [Variance]
RETURN
SWITCH(
    TRUE(),
    // Revenue: positive variance = green
    AccountType = "Revenue" && Var >= 0, "#00B050",
    AccountType = "Revenue" && Var < 0, "#FF0000",
    // Expenses: negative variance = green (under budget)
    AccountType IN {"COGS", "OpEx"} && Var <= 0, "#00B050",
    AccountType IN {"COGS", "OpEx"} && Var > 0, "#FF0000",
    "#808080"  // Grey for neutral
)
```

> **Exam Tip:** Conditional formatting in Matrix visuals supports background color, font color, data bars, and icons. Rules-based formatting is configured in the UI; field-value formatting uses a DAX measure. For financial reports, use account-type-aware logic since favorable/unfavorable varies by account type.

---

### Question 10 — Paginated Reports for Formal Statements

**The CFO needs a formatted income statement report that:**
- Has a fixed layout suitable for printing and PDF export
- Includes company logo and headers
- Supports parameters (fiscal year, cost center)
- Is distributed monthly to the board via email subscription

**Should this be a Power BI report or a paginated report? How do you build it?**

---

#### ✅ Answer

This should be a **paginated report** because:

| Requirement | Power BI Report | Paginated Report |
|------------|----------------|------------------|
| Fixed print layout | ❌ Responsive layout | ✅ Pixel-perfect layout |
| Company logo/headers | ⚠️ Limited | ✅ Full header/footer support |
| Parameters | ⚠️ Uses slicers | ✅ Native parameter support |
| PDF export | ⚠️ May not match print layout | ✅ Designed for PDF/print |
| Email subscription | ✅ Supported | ✅ Supported with attached PDF |
| Interactivity | ✅ Full interactivity | ❌ Limited |

**Building the paginated report:**

1. **Tool:** Use **Power BI Report Builder** (free download) or build in the Power BI service
2. **Data source:** Connect to the published Power BI dataset (using the dataset as a data source)
3. **Parameters:**
   - `FiscalYear` (dropdown, default = current year)
   - `CostCenter` (multi-select, default = all)

**DAX query for the report dataset:**

```dax
EVALUATE
SUMMARIZECOLUMNS(
    DimAccount[AccountType],
    DimAccount[AccountGroup],
    DimAccount[AccountName],
    FILTER(DimDate, DimDate[FiscalYear] = @FiscalYear),
    "Actuals", [Total Actuals],
    "Budget", [Total Budget],
    "Variance", [Variance],
    "Variance_Pct", [Variance %]
)
ORDER BY
    DimAccount[AccountType],
    DimAccount[AccountGroup]
```

**Report layout:**

- **Header:** Company logo, report title "Income Statement", fiscal year parameter value, print date
- **Body:** Tablix (table) with row groups for AccountType → AccountGroup → AccountName
- **Footer:** Page number, confidentiality notice
- **Page size:** Letter (8.5" × 11") or A4

**Publishing and subscription:**

1. Publish the paginated report to the Power BI service workspace
2. Create a **subscription:**
   - Navigate to the report in the service
   - Click **Subscribe** → **Subscribe to this paginated report**
   - Set schedule: Monthly, first business day
   - Attach as PDF
   - Add board members as recipients

> **Exam Tip:** Use paginated reports when you need pixel-perfect formatting for printing, PDF export, or email subscriptions with attachments. Paginated reports can use a published Power BI dataset as their data source. They support native parameters, headers/footers, and page breaks. Power BI Report Builder is the authoring tool.

---

### Question 11 — Deployment Pipeline and Workspaces

**Adventure Works Finance wants a proper deployment process:**
- **Development** workspace for building and testing
- **Test/UAT** workspace for finance team review
- **Production** workspace for end users

**How should you configure the deployment pipeline?**

---

#### ✅ Answer

**Step 1: Create three workspaces**

| Workspace | Purpose | Users |
|-----------|---------|-------|
| **AW Finance - Dev** | Development and testing by BI team | BI Developers (Admin/Member) |
| **AW Finance - Test** | User acceptance testing | Finance Directors (Viewer), FP&A (Viewer) |
| **AW Finance - Prod** | Production for all users | All stakeholders via App |

**Step 2: Create a deployment pipeline**

1. In the Power BI service → **Deployment pipelines** → **Create pipeline**
2. Name: "AW Finance Pipeline"
3. **Assign workspaces** to each stage:
   - Development → AW Finance - Dev
   - Test → AW Finance - Test
   - Production → AW Finance - Prod

**Step 3: Configure deployment rules**

Deployment rules handle differences between environments (e.g., different data sources):

| Stage | Rule Type | Configuration |
|-------|-----------|---------------|
| Test | Data source rule | Point to UAT database |
| Production | Data source rule | Point to production database |
| Test | Parameter rule | Set refresh frequency to weekly |
| Production | Parameter rule | Set refresh frequency to daily |

**Step 4: Deploy content**

1. Develop and test in the **Dev** workspace
2. Click **Deploy to Test** → review changes → confirm
3. After UAT approval, click **Deploy to Production** → review → confirm
4. Publish the **App** from the Production workspace

**Pipeline permissions:**

| Role | Pipeline Access |
|------|----------------|
| Pipeline Admin | Create, configure, delete pipeline |
| Member (all stages) | Deploy between stages |
| Workspace Admin/Member | Modify content in respective workspace |

**Handling dataset parameters for environment switching:**

In Power Query, parameterize the server name:

```m
let
    ServerName = #"Server Parameter",
    DatabaseName = #"Database Parameter",
    Source = Sql.Database(ServerName, DatabaseName)
in
    Source
```

Then set different parameter values via deployment rules for each stage.

> **Exam Tip:** Deployment pipelines automate promotion across Dev → Test → Prod workspaces. Deployment rules allow different data source connections per stage. Only workspace Members/Admins can deploy. The pipeline does not copy workspace permissions — each workspace has its own access controls.

---

### Question 12 — Row-Level Security by Cost Center

**Each cost center manager should only see financial data for their assigned cost center. Regional finance directors should see all cost centers in their country. The CFO sees everything.**

**Implement a comprehensive RLS solution.**

---

#### ✅ Answer

**Step 1: Create a security access table**

```
SecurityAccess:
| UserEmail                    | CostCenterID |
|------------------------------|-------------|
| cc_us01_mgr@aw.com          | CC-US-01    |
| cc_us02_mgr@aw.com          | CC-US-02    |
| cc_us03_mgr@aw.com          | CC-US-03    |
| cc_uk01_mgr@aw.com          | CC-UK-01    |
| ...                          | ...         |
| us_director@aw.com           | CC-US-01    |
| us_director@aw.com           | CC-US-02    |
| us_director@aw.com           | CC-US-03    |
| uk_director@aw.com           | CC-UK-01    |
| uk_director@aw.com           | CC-UK-02    |
| uk_director@aw.com           | CC-UK-03    |
```

Regional directors have **multiple rows** — one for each cost center they manage.

**Step 2: Create the relationship**

- `SecurityAccess[CostCenterID]` → `DimCostCenter[CostCenterID]` (many-to-one)
- Set cross-filter direction to **Both** (bidirectional)

**Step 3: Define the RLS role**

```dax
// Filter on SecurityAccess table
[UserEmail] = USERPRINCIPALNAME()
```

**Step 4: Handle the CFO**

The CFO should **not** be added to the RLS role. Instead:
- Give the CFO a workspace role of **Admin** or **Member** — these bypass RLS
- Or give the CFO **Viewer** role but do not add them to the security role

**Alternative: Single role with CONTAINS check**

Instead of a separate security table, add the filter directly on DimCostCenter:

```dax
// On DimCostCenter table
VAR UserEmail = USERPRINCIPALNAME()
VAR AllowedCostCenters =
    FILTER(
        SecurityAccess,
        SecurityAccess[UserEmail] = UserEmail
    )
RETURN
    [CostCenterID] IN SELECTCOLUMNS(AllowedCostCenters, "CC", [CostCenterID])
```

**Testing matrix:**

| User | Expected Access | Verify |
|------|----------------|--------|
| cc_us01_mgr@aw.com | CC-US-01 only | ✅ Only US-01 data visible |
| us_director@aw.com | CC-US-01, CC-US-02, CC-US-03 | ✅ All US cost centers |
| cfo@aw.com | All cost centers | ✅ Not in RLS role, sees all |

> **Exam Tip:** Dynamic RLS with a security mapping table is the most flexible approach. Users with multiple access levels (like regional directors) simply have multiple rows. Bidirectional cross-filtering on the security relationship ensures filters propagate to fact tables. Workspace Admins and Members always bypass RLS.

---

## 5. Key Exam Concepts Summary

| Concept | Domain | Key Takeaway |
|---------|--------|-------------|
| **Import vs. DirectQuery** | Prepare Data | Import for moderate data with complex DAX; DirectQuery for real-time needs |
| **REST API connectors** | Prepare Data | Use Web.Contents with query parameters for API data |
| **Query folding** | Prepare Data | Filter early at the source; verify with View Native Query |
| **Currency conversion** | Model Data | Power Query for static rates; DAX LOOKUPVALUE for dynamic conversion |
| **Parent-child hierarchies** | Model Data | PATH(), PATHITEM(), LOOKUPVALUE() to flatten account hierarchies |
| **Multiple fact tables** | Model Data | Each fact table connects to shared dimensions independently |
| **Budget vs. Actual variance** | Model Data | Account-type-aware: revenue positive = favorable, expense positive = unfavorable |
| **Time intelligence** | Model Data | TOTALYTD with fiscal year-end, SAMEPERIODLASTYEAR, DATESINPERIOD |
| **DATESYTD with fiscal year** | Model Data | Pass year-end date (e.g., "6/30") to align with fiscal calendar |
| **Combo charts** | Visualize | Ideal for comparing actuals vs. budget with variance trend overlay |
| **Waterfall charts** | Visualize | Show individual contributions (positive/negative) to a total |
| **Conditional formatting** | Visualize | Rules-based (UI) or field-value (DAX) for colors, data bars, icons |
| **Paginated reports** | Visualize | Pixel-perfect layout for printing, PDF, parameters, and subscriptions |
| **Deployment pipelines** | Deploy & Maintain | Dev → Test → Prod with environment-specific data source rules |
| **Dynamic RLS** | Deploy & Maintain | Security mapping table with multiple rows per user for multi-access |
