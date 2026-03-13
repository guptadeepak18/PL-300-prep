# Case Study 3: Adventure Works Finance — Financial Reporting Visualization & Dashboard Guide

> 📊 **Focus Area:** Visualize and Analyze the Data | Dashboard Design | Step-by-Step Instructions
> 🕐 **Estimated Study Time:** 120 minutes | **Questions:** 15 visualization-focused
> 📋 **Companion to:** [Case Study 3 – Financial Reporting & Budgeting](case-study-3-financial-reporting.md)

---

## Table of Contents

1. [Dashboard Overview](#1-dashboard-overview)
2. [Visualization Questions](#2-visualization-questions)
   - [Q1 – Financial KPI Cards with Variance Indicators](#question-1--financial-kpi-cards-with-variance-indicators)
   - [Q2 – Actuals vs Budget Combo Chart](#question-2--actuals-vs-budget-combo-chart)
   - [Q3 – Waterfall Chart for Variance by Cost Center](#question-3--waterfall-chart-for-variance-by-cost-center)
   - [Q4 – Income Statement Matrix with Hierarchy](#question-4--income-statement-matrix-with-hierarchy)
   - [Q5 – Conditional Formatting for Financial Tables](#question-5--conditional-formatting-for-financial-tables)
   - [Q6 – YTD vs Prior Year Trend Analysis](#question-6--ytd-vs-prior-year-trend-analysis)
   - [Q7 – Expense Breakdown Treemap](#question-7--expense-breakdown-treemap)
   - [Q8 – Rolling 12-Month Trend Line](#question-8--rolling-12-month-trend-line)
   - [Q9 – Gauge Visuals for Budget Utilization](#question-9--gauge-visuals-for-budget-utilization)
   - [Q10 – Drill-Through for Cost Center Detail](#question-10--drill-through-for-cost-center-detail)
   - [Q11 – Paginated Report for Board Reporting](#question-11--paginated-report-for-board-reporting)
   - [Q12 – Slicer Panel with Fiscal Year and Currency](#question-12--slicer-panel-with-fiscal-year-and-currency)
   - [Q13 – Bookmarks for Monthly Close Views](#question-13--bookmarks-for-monthly-close-views)
   - [Q14 – Small Multiples for Regional Comparison](#question-14--small-multiples-for-regional-comparison)
   - [Q15 – Custom Theme for Financial Reporting](#question-15--custom-theme-for-financial-reporting)
3. [Complete Business Dashboard Design](#3-complete-business-dashboard-design)
4. [DAX Measures Reference](#4-dax-measures-reference)
5. [Key Exam Concepts Summary](#5-key-exam-concepts-summary)

---

## 1. Dashboard Overview

The Adventure Works Finance dashboard provides the CFO, Regional Finance Directors, Cost Center Managers, and FP&A team with real-time visibility into financial performance, budget adherence, and variance analysis across 4 countries and 12 cost centers. The fiscal year runs July 1 – June 30.

**Dashboard pages:**

```
┌─────────────────────────────────────────────────────────────┐
│  📄 Page 1: Executive Financial Overview                    │
│  KPI cards, actuals vs budget trend, variance analysis      │
├─────────────────────────────────────────────────────────────┤
│  📄 Page 2: Income Statement & Expense Analysis             │
│  Matrix hierarchy, expense treemap, cost center breakdown   │
├─────────────────────────────────────────────────────────────┤
│  📄 Page 3: Regional & Trend Analysis                       │
│  Small multiples by country, rolling trends, forecasting    │
└─────────────────────────────────────────────────────────────┘
```

**Target audience and access:**

| Stakeholder | Pages Used | Access Level |
|-------------|-----------|--------------|
| CFO | All 3 pages | Full access across all entities |
| Regional Finance Directors (4) | All pages (filtered by country via RLS) | Country-only data |
| Cost Center Managers (12) | Page 1, Page 2 (filtered by cost center via RLS) | Cost-center-only data |
| FP&A | All 3 pages | Full access |
| External Auditors | Paginated report (separate) | Formatted statements only |

---

## 2. Visualization Questions

### Question 1 – Financial KPI Cards with Variance Indicators

**Scenario:** The CFO wants four prominent KPI cards at the top of the dashboard: Total Revenue, Total Expenses, Net Income, and Budget Variance %. Each card should show the current value with an arrow indicator and color (green for favorable, red for unfavorable).

**Task:** Create financial KPI cards with dynamic variance-based conditional formatting.

#### ✅ Step-by-Step Solution

**Step 1: Create the DAX measures**

```dax
Total Actuals =
SUM(FactActuals[Amount_USD])
```

```dax
Total Revenue =
CALCULATE(
    [Total Actuals],
    DimAccount[AccountType] = "Revenue"
)
```

```dax
Total Expenses =
CALCULATE(
    [Total Actuals],
    DimAccount[AccountType] IN {"COGS", "OpEx"}
)
```

```dax
Net Income =
[Total Revenue] - [Total Expenses]
```

```dax
Total Budget =
SUM(FactBudget[BudgetAmount])
```

```dax
Budget Variance =
[Total Actuals] - [Total Budget]
```

```dax
Budget Variance % =
DIVIDE([Budget Variance], [Total Budget], 0)
```

**Step 2: Create color measures (account-type aware)**

```dax
Revenue Variance Color =
IF(
    [Total Revenue] >= CALCULATE([Total Budget], DimAccount[AccountType] = "Revenue"),
    "#00B050",
    "#FF0000"
)
```

```dax
Expense Variance Color =
IF(
    [Total Expenses] <= CALCULATE([Total Budget], DimAccount[AccountType] IN {"COGS", "OpEx"}),
    "#00B050",
    "#FF0000"
)
```

```dax
Net Income Variance Color =
IF([Net Income] >= 0, "#00B050", "#FF0000")
```

**Step 3: Insert four Card visuals**

| Card | Measure | Color Measure | Notes |
|------|---------|--------------|-------|
| Revenue | `[Total Revenue]` | `[Revenue Variance Color]` | Green when above budget |
| Expenses | `[Total Expenses]` | `[Expense Variance Color]` | Green when below budget |
| Net Income | `[Net Income]` | `[Net Income Variance Color]` | Green when positive |
| Variance % | `[Budget Variance %]` | Rules-based | Green ≥ 0%, Red < 0% |

**Step 4: Apply conditional formatting**

1. Select Revenue Card → **Format** → **Callout value** → **Color** → **fx**
2. **Format style**: Field value → select `[Revenue Variance Color]`
3. Repeat for each card with the appropriate color measure

**Step 5: Add variance subtitle**

1. Use `[Budget Variance %]` as a tooltip or subtitle on each card
2. Alternatively, use a **KPI visual** (provides built-in trend and target indicators)

> **Exam Tip:** Financial KPIs require account-type-aware logic: for revenue, above budget is favorable (green); for expenses, below budget is favorable (green). Use DAX color measures with `IF()` for dynamic field-value conditional formatting. KPI visuals provide built-in trend indicators and targets.

---

### Question 2 – Actuals vs Budget Combo Chart

**Scenario:** The FP&A team needs a monthly view comparing actual spending against budget, with a variance percentage trend line, aligned to the fiscal calendar (July–June).

**Task:** Build a combo chart with fiscal month ordering on the X-axis.

#### ✅ Step-by-Step Solution

**Step 1: Create fiscal calendar measures**

```dax
Fiscal Month Label =
SELECTEDVALUE(DimDate[MonthName]) & " " &
FORMAT(SELECTEDVALUE(DimDate[FiscalYear]), "FY0")
```

**Step 2: Insert a Line and Clustered Column Chart**

| Field Well | Field |
|-----------|-------|
| **X-axis** | `DimDate[MonthName]` (sorted by `DimDate[FiscalMonthNumber]`) |
| **Column y-axis** | `[Total Actuals]`, `[Total Budget]` |
| **Line y-axis** | `[Budget Variance %]` |

**Step 3: Sort by fiscal month**

1. The fiscal year starts in July, so the X-axis must sort as: Jul, Aug, Sep, ..., May, Jun
2. Create a fiscal month sort column (if not already in DimDate):

```dax
FiscalMonthNumber =
IF(DimDate[MonthNumber] >= 7, DimDate[MonthNumber] - 6, DimDate[MonthNumber] + 6)
```

3. Select `DimDate[MonthName]` → **Column tools** → **Sort by column** → `FiscalMonthNumber`

**Step 4: Format the chart**

1. **Columns**: Actuals = Blue (#2F5496), Budget = Grey (#A5A5A5) with dashed border
2. **Line**: Variance % = Orange (#ED7D31), dashed style, markers on
3. **Secondary Y-axis**: Format as percentage
4. **Analytics** pane: Add a constant line at 0% on secondary axis

**Step 5: Add a fiscal year slicer**

1. Add a Slicer with `DimDate[FiscalYear]`
2. Format as dropdown, single-select

> **Exam Tip:** Fiscal calendars require custom sort orders. Use **Sort by column** to make MonthName sort by a fiscal month number (July = 1, August = 2, …). Combo charts with dual Y-axes are the recommended visual for budget vs. actual with variance overlay. Always label which Y-axis corresponds to which measure.

---

### Question 3 – Waterfall Chart for Variance by Cost Center

**Scenario:** The CFO wants to see how each cost center contributes to the total budget variance — which cost centers are favorable (under budget) and which are unfavorable (over budget).

**Task:** Create a waterfall chart showing variance breakdown by cost center.

#### ✅ Step-by-Step Solution

**Step 1: Create a cost center variance measure**

```dax
Cost Center Variance =
[Total Actuals] - [Total Budget]
```

**Step 2: Insert a Waterfall Chart**

1. Select **Waterfall Chart** from the Visualizations pane
2. Configure:
   - **Category**: `DimCostCenter[CostCenterName]`
   - **Y-axis**: `[Cost Center Variance]`

**Step 3: Configure sentiment colors**

1. **Format** pane → **Columns** → **Sentiment colors**:
   - **Increase** (unfavorable — actual exceeds budget): Red (#FF0000)
   - **Decrease** (favorable — actual below budget): Green (#00B050)
   - **Total**: Dark Blue (#2F5496)

**Step 4: Sort the waterfall**

1. Sort by `[Cost Center Variance]` descending → largest unfavorable variances appear first
2. This helps the CFO focus on the biggest issues immediately

**Step 5: Add data labels**

1. **Format** → **Data labels** → toggle **On**
2. Format as currency with abbreviations ($K or $M)
3. Position: Inside end

**Step 6: Add the total bar**

1. The waterfall automatically shows a **Total** bar at the end
2. This represents the net overall variance across all cost centers
3. **Format** → **Breakdown** → **Total** label: "Net Variance"

> **Exam Tip:** Waterfall charts show how individual components contribute positively or negatively to a total. Sentiment colors define positive (increase) and negative (decrease) bar colors. The total bar appears automatically. Waterfall charts are ideal for variance analysis and are commonly tested in financial reporting scenarios.

---

### Question 4 – Income Statement Matrix with Hierarchy

**Scenario:** The FP&A team needs a detailed income statement showing the chart of accounts hierarchy (Revenue → COGS → Gross Profit → Operating Expenses → Net Income) with monthly columns and expandable/collapsible account groups.

**Task:** Create a matrix visual with a flattened account hierarchy displaying the income statement.

#### ✅ Step-by-Step Solution

**Step 1: Flatten the account hierarchy (if parent-child)**

```dax
AccountPath = PATH(DimAccount[AccountID], DimAccount[ParentAccountID])
```

```dax
AccountLevel = PATHLENGTH(DimAccount[AccountPath])
```

```dax
Level1Account =
LOOKUPVALUE(
    DimAccount[AccountName],
    DimAccount[AccountID],
    PATHITEM(DimAccount[AccountPath], 1)
)
```

```dax
Level2Account =
IF(
    PATHLENGTH(DimAccount[AccountPath]) >= 2,
    LOOKUPVALUE(
        DimAccount[AccountName],
        DimAccount[AccountID],
        PATHITEM(DimAccount[AccountPath], 2)
    )
)
```

**Step 2: Build an account hierarchy**

1. In the Data pane, create a hierarchy: `Level1Account` → `Level2Account` → `AccountName`
2. This represents: Account Type → Account Group → Individual Account

**Step 3: Insert a Matrix visual**

| Field Well | Field |
|-----------|-------|
| **Rows** | Account Hierarchy (Level1 → Level2 → AccountName) |
| **Columns** | `DimDate[MonthName]` (sorted by FiscalMonthNumber) |
| **Values** | `[Total Actuals]`, `[Total Budget]`, `[Budget Variance]` |

**Step 4: Configure stepped layout**

1. **Format** pane → **Row headers** → **Stepped layout** → toggle **On**
2. Set indentation to **15px** per level
3. This creates a traditional income statement indentation

**Step 5: Add subtotals**

1. **Format** → **Row subtotals** → **On**
2. **Column subtotals** → **On**
3. Subtotals appear at each hierarchy level (e.g., Total Revenue, Total COGS)

**Step 6: Apply conditional formatting to Variance**

1. **Cell elements** → select `[Budget Variance]`
2. **Font color** → **fx** → Rules:
   - Value ≥ 0: Green (#00B050) — favorable
   - Value < 0: Red (#FF0000) — unfavorable

> **Exam Tip:** Matrix visuals with hierarchies are essential for financial statements. Use **stepped layout** for indentation. Parent-child hierarchies require `PATH()` and `PATHITEM()` functions to flatten. Subtotals aggregate at each hierarchy level. The exam frequently tests matrix configuration and hierarchy flattening.

---

### Question 5 – Conditional Formatting for Financial Tables

**Scenario:** The FP&A team wants the income statement matrix to display:
1. Variance values with red font for unfavorable, green for favorable (account-type aware)
2. Data bars on the Actuals column
3. Icons (arrows) on the Variance % column

**Task:** Apply account-type-aware conditional formatting to the financial matrix.

#### ✅ Step-by-Step Solution

**Step 1: Create an account-type-aware color measure**

```dax
Variance Color Smart =
VAR AccountType = SELECTEDVALUE(DimAccount[AccountType])
VAR Var = [Budget Variance]
RETURN
SWITCH(
    TRUE(),
    AccountType = "Revenue" && Var >= 0, "#00B050",
    AccountType = "Revenue" && Var < 0, "#FF0000",
    AccountType IN {"COGS", "OpEx"} && Var <= 0, "#00B050",
    AccountType IN {"COGS", "OpEx"} && Var > 0, "#FF0000",
    "#808080"
)
```

**Step 2: Apply font color to Variance**

1. Select the Matrix → **Format** → **Cell elements** → select `[Budget Variance]`
2. Toggle **Font color** → click **fx**
3. **Format style**: Field value → select `[Variance Color Smart]`

**Step 3: Add data bars to Actuals**

1. **Cell elements** → select `[Total Actuals]`
2. Toggle **Data bars** → click **fx**
3. Positive bar: Blue (#4472C4)
4. Show bar and value

**Step 4: Add icons to Variance %**

1. **Cell elements** → select `[Budget Variance %]`
2. Toggle **Icons** → click **fx**
3. Configure:
   - **Icon style**: Arrows
   - ≥ 5%: Green up arrow ↑
   - ≥ -5% and < 5%: Yellow right arrow →
   - < -5%: Red down arrow ↓
   - **Icon position**: Left of data

**Step 5: Verify account-type logic**

1. Revenue line items: positive variance (actual > budget) = Green ✓
2. Expense line items: positive variance (actual > budget) = Red ✓ (over budget)
3. Expense line items: negative variance (actual < budget) = Green ✓ (under budget)

> **Exam Tip:** Financial conditional formatting must be account-type aware: for revenue, exceeding budget is good (green); for expenses, exceeding budget is bad (red). Use a DAX measure with `SELECTEDVALUE(DimAccount[AccountType])` and `SWITCH(TRUE(), ...)` for smart coloring. This is a common exam scenario.

---

### Question 6 – YTD vs Prior Year Trend Analysis

**Scenario:** The CFO wants to compare year-to-date (YTD) revenue and expenses against the same period last fiscal year, with a cumulative trend chart.

**Task:** Create a YTD comparison chart using time intelligence functions.

#### ✅ Step-by-Step Solution

**Step 1: Create YTD measures with fiscal year support**

```dax
Revenue YTD =
TOTALYTD([Total Revenue], DimDate[Date], "6/30")
```

```dax
Revenue YTD PY =
CALCULATE(
    TOTALYTD([Total Revenue], DimDate[Date], "6/30"),
    SAMEPERIODLASTYEAR(DimDate[Date])
)
```

```dax
Revenue YTD Variance % =
DIVIDE(
    [Revenue YTD] - [Revenue YTD PY],
    [Revenue YTD PY],
    0
)
```

> **Note:** The `"6/30"` parameter in `TOTALYTD()` sets June 30 as the fiscal year-end date.

**Step 2: Insert a Line Chart**

| Field Well | Field |
|-----------|-------|
| **X-axis** | `DimDate[Date]` (Month granularity) |
| **Y-axis** | `[Revenue YTD]`, `[Revenue YTD PY]` |

**Step 3: Format the comparison**

1. Current YTD line: Solid blue (#2F5496), 3px width
2. Prior YTD line: Dashed grey (#A5A5A5), 2px width
3. Add data labels only at the final month (latest data point)

**Step 4: Add a YTD variance area fill**

1. Alternatively, use an **Area chart** with `[Revenue YTD]` and `[Revenue YTD PY]`
2. The gap between the two lines visually represents the variance
3. Apply transparency (40%) to see both areas

**Step 5: Add a reference card**

1. Place a Card visual next to the chart showing `[Revenue YTD Variance %]`
2. Apply conditional formatting: Green if ≥ 0%, Red if < 0%

> **Exam Tip:** `TOTALYTD()` accepts an optional fiscal year-end date parameter (e.g., `"6/30"` for June 30 year-end). `SAMEPERIODLASTYEAR()` shifts the date context back one year. Combining both creates YTD vs. prior year YTD comparisons. The exam tests fiscal year-end parameter in time intelligence functions.

---

### Question 7 – Expense Breakdown Treemap

**Scenario:** The CFO wants a visual showing how total expenses are distributed across account groups (COGS, Salaries, Marketing, Rent, etc.) with proportional sizing and the ability to drill down into individual accounts.

**Task:** Create a treemap visual for expense decomposition.

#### ✅ Step-by-Step Solution

**Step 1: Filter to expense accounts only**

1. Create a measure or use a visual-level filter:

```dax
Total Expenses Detail =
CALCULATE(
    [Total Actuals],
    DimAccount[AccountType] IN {"COGS", "OpEx"}
)
```

**Step 2: Insert a Treemap**

| Field Well | Field |
|-----------|-------|
| **Group** | `DimAccount[AccountGroup]` → `DimAccount[AccountName]` (hierarchy) |
| **Values** | `[Total Expenses Detail]` |

**Step 3: Add drill-down hierarchy**

1. Build a hierarchy in DimAccount: AccountGroup → AccountName
2. Assign the hierarchy to the **Group** well
3. Users can drill down from expense group (e.g., "Operating Expenses") to individual accounts

**Step 4: Format the treemap**

1. **Format** pane → **Data labels** → toggle **On**
   - **Label contents**: Category + Value
   - **Display units**: Thousands ($K)
2. **Colors**: Use the financial theme colors (blues and greens for major groups)

**Step 5: Add a visual-level filter**

1. Drag `DimAccount[AccountType]` to **Filters on this visual**
2. Select only **COGS** and **OpEx**
3. This ensures only expense accounts appear in the treemap

> **Exam Tip:** Treemaps display hierarchical data as nested rectangles proportional to their value. They are ideal for showing expense composition. Drill-down on treemaps works via the hierarchy assigned to the Group well. Use visual-level filters to restrict the data shown without affecting other visuals.

---

### Question 8 – Rolling 12-Month Trend Line

**Scenario:** The FP&A team wants to see a rolling 12-month revenue trend to smooth out monthly fluctuations and identify the underlying growth trajectory.

**Task:** Create a line chart with both monthly actuals and a rolling 12-month average.

#### ✅ Step-by-Step Solution

**Step 1: Create the rolling 12-month measure**

```dax
Revenue Rolling 12M =
CALCULATE(
    [Total Revenue],
    DATESINPERIOD(DimDate[Date], MAX(DimDate[Date]), -12, MONTH)
)
```

```dax
Revenue Rolling 12M Avg =
DIVIDE([Revenue Rolling 12M], 12, 0)
```

**Step 2: Insert a Line Chart**

| Field Well | Field |
|-----------|-------|
| **X-axis** | `DimDate[Date]` (Month granularity) |
| **Y-axis** | `[Total Revenue]`, `[Revenue Rolling 12M Avg]` |

**Step 3: Differentiate the lines**

1. Monthly Revenue line: Thin (#4472C4), 1.5px, with data markers
2. Rolling 12M Average line: Thick (#2F5496), 3px, smooth line, no markers

**Step 4: Add Analytics pane trend line**

1. Alternatively, use the built-in **Trend line**:
   - **Analytics** pane → **Trend line** → toggle **On**
   - Power BI fits a linear or polynomial trend line automatically
2. However, the DAX rolling average gives more control

**Step 5: Add forecast**

1. **Analytics** pane → **Forecast** → toggle **On**
2. Forecast length: **6 months** (or appropriate period)
3. Confidence interval: **95%**
4. The shaded forecast area extends the rolling trend

> **Exam Tip:** `DATESINPERIOD()` is the key function for rolling calculations — it creates a moving window of dates relative to the current filter context. Rolling averages smooth out noise to reveal trends. The Analytics pane provides built-in trend lines and forecasts (ETS algorithm) without requiring DAX.

---

### Question 9 – Gauge Visuals for Budget Utilization

**Scenario:** Each cost center manager wants to see their budget utilization as a gauge showing how much of their annual budget has been spent, with color zones for under/on/over budget.

**Task:** Create gauge visuals showing budget consumption percentage.

#### ✅ Step-by-Step Solution

**Step 1: Create budget utilization measures**

```dax
Budget Utilization % =
DIVIDE([Total Actuals], [Total Budget], 0)
```

```dax
Budget Remaining =
[Total Budget] - [Total Actuals]
```

**Step 2: Insert a Gauge visual**

1. Select **Gauge** from the Visualizations pane
2. Configure:
   - **Value**: `[Total Actuals]`
   - **Minimum value**: 0
   - **Maximum value**: `[Total Budget]`
   - **Target value**: `[Total Budget]` (shows a target marker)

**Step 3: Configure callout value**

1. **Format** pane → **Callout value** → set to display `[Budget Utilization %]`
2. Format as percentage with 1 decimal place

**Step 4: Set color zones (conditional formatting)**

1. The gauge visual supports conditional color on the fill:
   - **Format** → **Fill** → **Color** → click **fx**
   - Rules:
     - Value ≤ 0.80 (under 80% utilized): Green (#00B050)
     - Value > 0.80 AND ≤ 1.00 (80–100% utilized): Yellow (#FFC000)
     - Value > 1.00 (over budget): Red (#FF0000)

**Step 5: Use Small Multiples for multiple cost centers**

1. Alternatively, to show all 12 cost centers as individual gauges:
2. Add `DimCostCenter[CostCenterName]` to the **Small multiples** field well
3. This creates a grid of 12 mini-gauges, one per cost center

> **Exam Tip:** Gauge visuals show a single value against a target/maximum. Configure Value, Minimum, Maximum, and Target in the field wells. Conditional color on the gauge fill indicates status zones. The exam may ask when to use a gauge vs. a KPI visual — gauges show progress toward a target; KPI visuals show trends over time.

---

### Question 10 – Drill-Through for Cost Center Detail

**Scenario:** When the CFO clicks on a cost center in the waterfall chart or any other visual, they should navigate to a detailed Cost Center page showing that center's monthly actuals, top expense accounts, and budget utilization.

**Task:** Configure a drill-through page for cost center analysis.

#### ✅ Step-by-Step Solution

**Step 1: Create the target page**

1. Add a new report page → rename to **"Cost Center Details"**

**Step 2: Configure drill-through**

1. Drag `DimCostCenter[CostCenterName]` into the **Drill-through filters** well
2. Optionally add `DimDate[FiscalYear]` as a second drill-through field
3. Toggle **Keep all filters** → **On**

**Step 3: Add visuals to the detail page**

| Visual | Type | Configuration |
|--------|------|---------------|
| Cost Center Name | Card | `DimCostCenter[CostCenterName]` |
| Budget Utilization | Gauge | Value: `[Total Actuals]`, Max: `[Total Budget]`, Target: `[Total Budget]` |
| Monthly Trend | Line Chart | X: `DimDate[MonthName]`, Y: `[Total Actuals]` and `[Total Budget]` |
| Top 10 Accounts | Bar Chart | Y: `DimAccount[AccountName]`, X: `[Total Actuals]`, Top 10 filter |
| Account Detail Table | Table | `DimAccount[AccountName]`, `[Total Actuals]`, `[Total Budget]`, `[Budget Variance]`, `[Budget Variance %]` with conditional formatting |
| Quarterly Summary | Matrix | Rows: `DimDate[FiscalQuarter]`, Values: `[Total Actuals]`, `[Total Budget]`, `[Budget Variance]` |

**Step 4: Format the Back button**

1. Select the auto-created Back button
2. Position in the top-left corner
3. Set tooltip: "Return to Financial Overview"

**Step 5: Test the drill-through**

1. Go to Page 1 → right-click a cost center in the Waterfall chart
2. **Drill through** → **Cost Center Details**
3. Verify all visuals filter to the selected cost center
4. Click Back to return

> **Exam Tip:** Drill-through filters are configured on the target page. Multiple drill-through fields can be added to pass compound context (e.g., cost center + fiscal year). The **Keep all filters** option preserves slicer selections from the source page. Drill-through is triggered by right-clicking on a data point.

---

### Question 11 – Paginated Report for Board Reporting

**Scenario:** The CFO needs a formal income statement for monthly board meetings with pixel-perfect formatting, company logo, parameters for fiscal year and quarter, and automatic monthly email delivery as PDF.

**Task:** Design and configure a paginated report connected to the Power BI dataset.

#### ✅ Step-by-Step Solution

**Step 1: Choose the right tool**

| Requirement | Power BI Report | Paginated Report ✅ |
|------------|----------------|---------------------|
| Pixel-perfect layout | ❌ Responsive | ✅ Fixed layout |
| Company logo & headers | ⚠️ Limited | ✅ Full support |
| Parameters | ⚠️ Slicers | ✅ Native parameters |
| PDF/print fidelity | ⚠️ Variable | ✅ Designed for print |
| Email subscription with PDF | ✅ Supported | ✅ Supported + attached PDF |

**Step 2: Open Power BI Report Builder**

1. Download and install **Power BI Report Builder** (free)
2. Create a new report
3. Set data source: Connect to the **published Power BI dataset** in the service

**Step 3: Configure parameters**

1. Add parameter: `FiscalYear` (Integer, dropdown from query: `DISTINCT DimDate[FiscalYear]`)
2. Add parameter: `FiscalQuarter` (Text, multi-select, default = all quarters)
3. Add parameter: `CostCenter` (Text, multi-select, default = all cost centers)

**Step 4: Write the dataset query (DAX)**

```dax
EVALUATE
SUMMARIZECOLUMNS(
    DimAccount[AccountType],
    DimAccount[AccountGroup],
    DimAccount[AccountName],
    FILTER(DimDate, DimDate[FiscalYear] = @FiscalYear),
    "Actuals", [Total Actuals],
    "Budget", [Total Budget],
    "Variance", [Budget Variance],
    "Variance_Pct", [Budget Variance %]
)
ORDER BY
    DimAccount[AccountType],
    DimAccount[AccountGroup]
```

**Step 5: Design the report layout**

1. **Header**: Company logo (left), title "Income Statement" (center), fiscal year (right), print date
2. **Body**: Tablix with:
   - Row groups: AccountType → AccountGroup → AccountName
   - Columns: Actuals, Budget, Variance, Variance %
   - Subtotals at each group level
3. **Footer**: Page number ("Page X of Y"), confidentiality notice
4. **Page size**: Letter (8.5" × 11") portrait

**Step 6: Publish and create subscription**

1. Publish to the Power BI service workspace
2. Navigate to the paginated report → **Subscribe**
3. Configure:
   - Schedule: Monthly, 1st business day
   - Format: PDF attachment
   - Recipients: Board member email distribution list
   - Parameters: Current fiscal year, all quarters

> **Exam Tip:** Paginated reports are the correct choice for formal financial statements, board packages, and printed reports. They use **Power BI Report Builder** for authoring and connect to **published Power BI datasets**. They support native parameters, headers/footers, and page breaks. Email subscriptions can include PDF attachments.

---

### Question 12 – Slicer Panel with Fiscal Year and Currency

**Scenario:** The dashboard needs a filter panel that allows users to select the fiscal year, fiscal quarter, cost center, country, and reporting currency (USD, GBP, EUR, AUD). The currency slicer should dynamically convert all values.

**Task:** Create a comprehensive slicer panel with synced slicers and a currency conversion toggle.

#### ✅ Step-by-Step Solution

**Step 1: Add slicers to Page 1**

| Slicer | Field | Style | Selection |
|--------|-------|-------|-----------|
| Fiscal Year | `DimDate[FiscalYear]` | Dropdown | Single select |
| Fiscal Quarter | `DimDate[FiscalQuarter]` | Tile | Multi-select |
| Country | `DimCostCenter[Country]` | Tile | Multi-select |
| Cost Center | `DimCostCenter[CostCenterName]` | Dropdown | Multi-select |
| Currency | `CurrencySelector[Currency]` | Tile | Single select |

**Step 2: Create a currency selector table**

1. Create a disconnected table (no relationships) called **CurrencySelector**:

| Currency | Label |
|----------|-------|
| USD | US Dollar ($) |
| GBP | British Pound (£) |
| EUR | Euro (€) |
| AUD | Australian Dollar (A$) |

**Step 3: Create a currency conversion measure**

```dax
Total Actuals Converted =
VAR SelectedCurrency = SELECTEDVALUE(CurrencySelector[Currency], "USD")
RETURN
IF(
    SelectedCurrency = "USD",
    [Total Actuals],
    SUMX(
        FactActuals,
        FactActuals[Amount_Local] *
        LOOKUPVALUE(
            DimExchangeRates[Rate],
            DimExchangeRates[FromCurrency], FactActuals[CurrencyCode],
            DimExchangeRates[ToCurrency], SelectedCurrency,
            DimExchangeRates[Date], FactActuals[PostingDate]
        )
    )
)
```

**Step 4: Sync slicers across pages**

1. **View** → **Sync slicers**
2. Sync Fiscal Year, Fiscal Quarter, and Country across all three pages
3. Make Cost Center slicer visible only on Page 1 and Page 2

**Step 5: Use all converted measures**

1. Replace `[Total Actuals]` with `[Total Actuals Converted]` in all visuals
2. Repeat for `[Total Budget Converted]`, `[Budget Variance Converted]`

> **Exam Tip:** Disconnected tables (no relationships) with `SELECTEDVALUE()` create parameter-like behavior for what-if analysis or selection-driven measures. Currency conversion in DAX uses `LOOKUPVALUE()` to retrieve exchange rates. Synced slicers maintain consistent filtering across pages. The exam tests disconnected table patterns.

---

### Question 13 – Bookmarks for Monthly Close Views

**Scenario:** At month-end, the FP&A team needs to quickly switch between different analysis views: "Current Month Close", "QTD Summary", "YTD Cumulative", and "Full Year Budget Comparison." Each view shows different measures and time periods.

**Task:** Create bookmarks that change both the filter state and the visibility of certain visuals.

#### ✅ Step-by-Step Solution

**Step 1: Create the bookmarks**

1. **Current Month Close** view:
   - Set slicers to current fiscal month
   - Show monthly actuals vs budget visuals
   - Hide YTD and rolling trend visuals
   - **View** → **Bookmarks** → **Add** → name "Current Month Close"
   - Right-click → check both **Data** and **Display**

2. **QTD Summary** view:
   - Set slicers to current fiscal quarter
   - Show QTD measures in KPI cards
   - **Add bookmark** → name "QTD Summary"

3. **YTD Cumulative** view:
   - Show YTD trend line chart prominently
   - Show YTD vs PY comparison
   - **Add bookmark** → name "YTD Cumulative"

4. **Full Year Budget** view:
   - Clear month/quarter filters (show full year)
   - Show budget utilization gauges
   - **Add bookmark** → name "Full Year Budget"

**Step 2: Create selection buttons**

1. Insert four buttons at the top of the page
2. Label: "Month", "QTD", "YTD", "Full Year"
3. Link each button to its corresponding bookmark:
   - **Format** → **Action** → **Type**: Bookmark → select bookmark name

**Step 3: Style the buttons**

1. Use tab-like styling:
   - Active: Dark blue background, white text
   - Inactive: Light grey background, dark text
   - Hover: Medium blue background, white text

**Step 4: Test all views**

1. Click "Month" → verify only monthly close visuals appear
2. Click "YTD" → verify YTD trend and comparison appear
3. Click "Full Year" → verify all filters cleared and full-year gauges visible

> **Exam Tip:** Bookmarks can save three components independently: **Data** (filter/slicer state), **Display** (visual visibility on/off), and **Current page**. When a bookmark controls visual visibility (show/hide visuals), check the **Display** option. Combine Data + Display bookmarks for views that change both filters and layout.

---

### Question 14 – Small Multiples for Regional Comparison

**Scenario:** The CFO wants to compare the actuals vs. budget trend across all four countries (US, UK, Germany, Australia) side by side, with each country shown as a separate mini-chart with the same scale for easy comparison.

**Task:** Create a small multiples chart for cross-country comparison.

#### ✅ Step-by-Step Solution

**Step 1: Insert a Line Chart**

1. Select **Line Chart** from the Visualizations pane
2. Configure:
   - **X-axis**: `DimDate[MonthName]` (sorted by FiscalMonthNumber)
   - **Y-axis**: `[Total Actuals]`, `[Total Budget]`
   - **Small multiples**: `DimCostCenter[Country]`

**Step 2: The small multiples field**

1. Drag `DimCostCenter[Country]` to the **Small multiples** field well
2. Power BI creates a grid of mini-charts, one per country
3. Each mini-chart shares the same axes and scale for comparison

**Step 3: Configure the layout**

1. **Format** pane → **Small multiples** → **Layout**:
   - **Columns**: 2 (for a 2×2 grid with 4 countries)
   - **Rows per page**: 2
2. **Shared axis**: toggle **On** → all mini-charts use the same Y-axis scale
3. This makes it easy to compare magnitudes across countries

**Step 4: Format individual elements**

1. Line colors: Actuals = Blue, Budget = Grey (dashed)
2. Title: Each mini-chart automatically shows the country name
3. Grid lines: Light grey for readability

**Step 5: Alternative — use for cost centers**

1. Replace Country with `DimCostCenter[CostCenterName]` for a 12-panel view
2. Adjust layout: 4 columns × 3 rows
3. This creates a comprehensive cost center comparison dashboard

> **Exam Tip:** Small multiples split a single visual into a grid of mini-charts based on a categorical field. All mini-charts share the same axes for easy comparison. Enable **shared axis** to keep the same scale. Small multiples work with bar, column, line, and area charts. This feature is frequently tested in PL-300.

---

### Question 15 – Custom Theme for Financial Reporting

**Scenario:** Adventure Works Finance requires a professional theme with corporate colors, appropriate fonts for financial data, and consistent formatting across all reports shared with the board and auditors.

**Task:** Create a Power BI JSON theme for financial reporting.

#### ✅ Step-by-Step Solution

**Step 1: Define the financial reporting color palette**

| Purpose | Color Name | Hex Code |
|---------|-----------|----------|
| Primary | Corporate Navy | #1B2A4A |
| Secondary | Steel Blue | #4472C4 |
| Favorable | Finance Green | #00B050 |
| Unfavorable | Alert Red | #C00000 |
| Neutral | Warm Grey | #7F7F7F |
| Background | Off-White | #FAFAFA |
| Text | Dark Charcoal | #2D2D2D |

**Step 2: Create the JSON theme file**

```json
{
    "name": "Adventure Works Finance",
    "dataColors": [
        "#1B2A4A",
        "#4472C4",
        "#00B050",
        "#C00000",
        "#7F7F7F",
        "#FFC000",
        "#2E8B8B",
        "#ED7D31"
    ],
    "background": "#FAFAFA",
    "foreground": "#2D2D2D",
    "tableAccent": "#1B2A4A",
    "visualStyles": {
        "*": {
            "*": {
                "title": [{
                    "fontFamily": "Segoe UI Semibold",
                    "fontSize": 11,
                    "color": "#2D2D2D"
                }],
                "background": [{
                    "color": "#FFFFFF",
                    "transparency": 0
                }],
                "border": [{
                    "show": true,
                    "color": "#E0E0E0"
                }]
            }
        }
    }
}
```

**Step 3: Apply the theme**

1. **View** → **Themes** → **Browse for themes**
2. Select `adventure-works-finance-theme.json`
3. All visuals update to the corporate color scheme

**Step 4: Customize specific visual defaults**

1. **View** → **Themes** → **Customize current theme**:
   - **Table/Matrix**: Segoe UI, size 9 for data readability
   - **Headers**: Bold, Dark Navy (#1B2A4A), White text
   - **Grid**: Light borders for clean financial table look

**Step 5: Export and standardize**

1. **View** → **Themes** → **Save current theme**
2. Distribute the JSON file to all finance report authors
3. Apply consistently across all Adventure Works financial reports

> **Exam Tip:** JSON themes ensure brand and formatting consistency. The `dataColors` array defines the series color order. Financial themes should use professional, muted colors. The `visualStyles` object controls individual visual type defaults. Apply via **View > Themes**. The exam may show a JSON snippet and ask what it controls.

---

## 3. Complete Business Dashboard Design

### Page 1: Executive Financial Overview

```
┌──────────────────────────────────────────────────────────────────┐
│ [💰 Financial Overview] [📊 Income Statement] [🌍 Regional]      │
│ [Month] [QTD] [YTD] [Full Year]  ← bookmark view buttons        │
├──────────────────────────────────────────────────────────────────┤
│ [FY ▼] [Quarter ▪▪▪▪] [Country ▪▪▪▪] [Cost Center ▼] [$ ▪▪▪▪]  │
├───────────┬───────────┬────────────┬─────────────────────────────┤
│ Card:     │ Card:     │ Card:      │ Card:                       │
│ Revenue   │ Expenses  │ Net Income │ Variance %                  │
│ $125.4M   │ $98.2M    │ $27.2M     │ +3.2%                       │
│ ▲ Green   │ ▲ Green   │ ▲ Green    │ ▲ Green                     │
├───────────┴───────────┴────────────┴─────────────────────────────┤
│                                                                  │
│  Combo Chart: Monthly Actuals vs Budget (fiscal month order)     │
│  ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██  columns = A & B           │
│  ▓▓ ▓▓ ▓▓ ▓▓ ▓▓ ▓▓ ▓▓ ▓▓ ▓▓ ▓▓ ▓▓ ▓▓  + ── Variance % line   │
│  Jul Aug Sep Oct Nov Dec Jan Feb Mar Apr May Jun                 │
├───────────────────────────────┬──────────────────────────────────┤
│                               │                                  │
│  Waterfall Chart:             │  Gauge: Budget Utilization       │
│  Variance by Cost Center      │  [Small multiples by cost       │
│  █ ▄ █ ▂ █ ▃ █ ▅ █ Total     │   center or single gauge]       │
│  (Green = favorable,          │  85% utilized                    │
│   Red = unfavorable)          │  Target: 100%                    │
│                               │                                  │
└───────────────────────────────┴──────────────────────────────────┘
```

**Visual field configuration:**

| Visual | Type | Fields | Format |
|--------|------|--------|--------|
| Revenue Card | Card | `[Total Revenue]` | Currency, conditional color |
| Expenses Card | Card | `[Total Expenses]` | Currency, conditional color (green when under budget) |
| Net Income Card | Card | `[Net Income]` | Currency, conditional color |
| Variance % Card | Card | `[Budget Variance %]` | Percentage, conditional color |
| Actuals vs Budget | Line + Column | X: MonthName (fiscal sort), Cols: Actuals & Budget, Line: Variance % | Blue/grey columns, orange line |
| Variance Waterfall | Waterfall | Category: CostCenterName, Y: Variance | Green = favorable, Red = unfavorable |
| Budget Gauge | Gauge | Value: Total Actuals, Max: Total Budget, Target: Total Budget | Color zones |

---

### Page 2: Income Statement & Expense Analysis

```
┌──────────────────────────────────────────────────────────────────┐
│ [💰 Financial Overview] [📊 Income Statement] [🌍 Regional]      │
├──────────────────────────────────────────────────────────────────┤
│ [FY ▼] [Quarter ▪▪▪▪] [Country ▪▪▪▪] [Cost Center ▼]           │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Matrix: Income Statement                                        │
│  ┌────────────────────┬────────┬────────┬──────────┬──────────┐  │
│  │ Account            │ Actual │ Budget │ Variance │ Var %    │  │
│  ├────────────────────┼────────┼────────┼──────────┼──────────┤  │
│  │ + Revenue          │ $125M  │ $121M  │ 🟢 +$4M  │ 🟢↑ 3.2%│  │
│  │   Product Sales    │ $98M   │ $95M   │ 🟢 +$3M  │ 🟢↑ 3.1%│  │
│  │   Services         │ $27M   │ $26M   │ 🟢 +$1M  │ 🟢↑ 3.8%│  │
│  │ - COGS             │ $65M   │ $63M   │ 🔴 +$2M  │ 🔴↑ 3.2%│  │
│  │ = Gross Profit     │ $60M   │ $58M   │ 🟢 +$2M  │         │  │
│  │ - Operating Exp    │ $33M   │ $35M   │ 🟢 -$2M  │ 🟢↓ 5.7%│  │
│  │ = Net Income       │ $27M   │ $23M   │ 🟢 +$4M  │         │  │
│  └────────────────────┴────────┴────────┴──────────┴──────────┘  │
│                                                                  │
├──────────────────────────────┬───────────────────────────────────┤
│                              │                                   │
│  Treemap:                    │  Line Chart:                      │
│  Expense Breakdown           │  Rolling 12-Month Revenue         │
│  ┌──────────┬──────┐        │  ── Monthly (thin)                │
│  │  COGS    │Salary│        │  ── Rolling Avg (thick)           │
│  │          │      │        │  ▓▓ Forecast (shaded)             │
│  │          ├──────┤        │                                   │
│  │          │ Rent │        │                                   │
│  └──────────┴──────┘        │                                   │
│                              │                                   │
└──────────────────────────────┴───────────────────────────────────┘
```

---

### Page 3: Regional & Trend Analysis

```
┌──────────────────────────────────────────────────────────────────┐
│ [💰 Financial Overview] [📊 Income Statement] [🌍 Regional]      │
├──────────────────────────────────────────────────────────────────┤
│ [FY ▼] [Quarter ▪▪▪▪]                                           │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Small Multiples: Actuals vs Budget by Country                   │
│  ┌──────────────────┬──────────────────┐                        │
│  │ United States    │ United Kingdom   │                        │
│  │ ██ ▓▓ ██ ▓▓ ██  │ ██ ▓▓ ██ ▓▓ ██  │                        │
│  ├──────────────────┼──────────────────┤                        │
│  │ Germany          │ Australia        │                        │
│  │ ██ ▓▓ ██ ▓▓ ██  │ ██ ▓▓ ██ ▓▓ ██  │                        │
│  └──────────────────┴──────────────────┘                        │
│                                                                  │
├──────────────────────────────┬───────────────────────────────────┤
│                              │                                   │
│  Line Chart:                 │  Clustered Bar:                   │
│  YTD Revenue: Current vs PY  │  Net Income by Country            │
│  ── Current YTD (solid blue) │  ████████████ United States       │
│  ── Prior YTD (dashed grey)  │  █████████ United Kingdom         │
│  [Card: YTD Var: +3.2%]     │  ████████ Germany                 │
│                              │  ██████ Australia                 │
│                              │                                   │
└──────────────────────────────┴───────────────────────────────────┘
```

---

## 4. DAX Measures Reference

### Core Financial Metrics

```dax
Total Actuals =
SUM(FactActuals[Amount_USD])
```

```dax
Total Revenue =
CALCULATE([Total Actuals], DimAccount[AccountType] = "Revenue")
```

```dax
Total Expenses =
CALCULATE([Total Actuals], DimAccount[AccountType] IN {"COGS", "OpEx"})
```

```dax
Net Income =
[Total Revenue] - [Total Expenses]
```

```dax
Gross Profit =
[Total Revenue] - CALCULATE([Total Actuals], DimAccount[AccountType] = "COGS")
```

```dax
Gross Margin % =
DIVIDE([Gross Profit], [Total Revenue], 0)
```

### Budget Metrics

```dax
Total Budget =
SUM(FactBudget[BudgetAmount])
```

```dax
Budget Variance =
[Total Actuals] - [Total Budget]
```

```dax
Budget Variance % =
DIVIDE([Budget Variance], [Total Budget], 0)
```

```dax
Budget Utilization % =
DIVIDE([Total Actuals], [Total Budget], 0)
```

```dax
Budget Remaining =
[Total Budget] - [Total Actuals]
```

### Time Intelligence (Fiscal Year: July–June)

```dax
Revenue YTD =
TOTALYTD([Total Revenue], DimDate[Date], "6/30")
```

```dax
Revenue YTD PY =
CALCULATE(
    TOTALYTD([Total Revenue], DimDate[Date], "6/30"),
    SAMEPERIODLASTYEAR(DimDate[Date])
)
```

```dax
Revenue YTD Variance % =
DIVIDE([Revenue YTD] - [Revenue YTD PY], [Revenue YTD PY], 0)
```

```dax
Revenue QTD =
TOTALQTD([Total Revenue], DimDate[Date])
```

```dax
Revenue Rolling 12M =
CALCULATE(
    [Total Revenue],
    DATESINPERIOD(DimDate[Date], MAX(DimDate[Date]), -12, MONTH)
)
```

```dax
Revenue Rolling 12M Avg =
DIVIDE([Revenue Rolling 12M], 12, 0)
```

### Conditional Formatting Helpers

```dax
Variance Color Smart =
VAR AccountType = SELECTEDVALUE(DimAccount[AccountType])
VAR Var = [Budget Variance]
RETURN
SWITCH(
    TRUE(),
    AccountType = "Revenue" && Var >= 0, "#00B050",
    AccountType = "Revenue" && Var < 0, "#FF0000",
    AccountType IN {"COGS", "OpEx"} && Var <= 0, "#00B050",
    AccountType IN {"COGS", "OpEx"} && Var > 0, "#FF0000",
    "#808080"
)
```

```dax
Revenue Variance Color =
IF(
    [Total Revenue] >= CALCULATE([Total Budget], DimAccount[AccountType] = "Revenue"),
    "#00B050", "#FF0000"
)
```

```dax
Net Income Variance Color =
IF([Net Income] >= 0, "#00B050", "#FF0000")
```

### Currency Conversion

```dax
Total Actuals Converted =
VAR SelectedCurrency = SELECTEDVALUE(CurrencySelector[Currency], "USD")
RETURN
IF(
    SelectedCurrency = "USD",
    [Total Actuals],
    SUMX(
        FactActuals,
        FactActuals[Amount_Local] *
        LOOKUPVALUE(
            DimExchangeRates[Rate],
            DimExchangeRates[FromCurrency], FactActuals[CurrencyCode],
            DimExchangeRates[ToCurrency], SelectedCurrency,
            DimExchangeRates[Date], FactActuals[PostingDate]
        )
    )
)
```

---

## 5. Key Exam Concepts Summary

| Concept | Domain | Key Takeaway |
|---------|--------|-------------|
| **Financial KPI cards** | Visualize | Account-type-aware coloring: revenue above budget = green; expenses above budget = red |
| **Combo charts** | Visualize | Line + Column ideal for actuals vs budget with variance line on dual Y-axes |
| **Waterfall charts** | Visualize | Show individual contributions (positive/negative) to a total; sentiment colors for fav/unfav |
| **Matrix with hierarchy** | Visualize | Essential for income statements; use PATH()/PATHITEM() to flatten; stepped layout for indentation |
| **Conditional formatting** | Visualize | Account-type-aware DAX measure with SWITCH(TRUE()) for smart financial coloring |
| **TOTALYTD with fiscal year** | Model Data | Pass year-end date "6/30" as third parameter for fiscal year alignment |
| **Rolling calculations** | Model Data | DATESINPERIOD() creates a moving window; divide by period count for average |
| **Gauge visuals** | Visualize | Show value against target/maximum; color zones indicate budget utilization status |
| **Treemaps** | Visualize | Proportional nested rectangles for expense breakdown; supports drill-down hierarchies |
| **Drill-through** | Visualize | Configured on target page with cost center field; carries filter context from source |
| **Paginated reports** | Visualize | Power BI Report Builder; pixel-perfect for print/PDF; native parameters; email subscriptions |
| **Disconnected tables** | Model Data | Currency selector with SELECTEDVALUE(); no relationships; acts as user-driven parameter |
| **Small multiples** | Visualize | Split visual into grid of mini-charts by category; shared axis enables comparison |
| **Bookmarks** | Visualize | Save Data + Display state; combine with buttons for monthly close analysis views |
| **JSON themes** | Visualize | Corporate color consistency; dataColors array; visualStyles for visual-type defaults |

---

*This dashboard guide is a companion to [Case Study 3 – Financial Reporting & Budgeting](case-study-3-financial-reporting.md). Complete both for full PL-300 exam preparation.*
