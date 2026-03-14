# Case Study 1: Contoso Retail Group — Retail Sales Visualization & Dashboard Guide

> 📊 **Focus Area:** Visualize and Analyze the Data | Dashboard Design | Step-by-Step Instructions
> 🕐 **Estimated Study Time:** 120 minutes | **Questions:** 15 visualization-focused
> 📋 **Companion to:** [Case Study 1 – Retail Sales Analytics](case-study-1-retail-sales.md)

---

## Table of Contents

1. [Dashboard Overview](#1-dashboard-overview)
2. [Visualization Questions](#2-visualization-questions)
   - [Q1 – Creating KPI Cards](#question-1--creating-kpi-cards)
   - [Q2 – Revenue Trend Line Chart](#question-2--revenue-trend-line-chart)
   - [Q3 – Revenue by Product Category](#question-3--revenue-by-product-category)
   - [Q4 – Geographic Sales Map](#question-4--geographic-sales-map)
   - [Q5 – Top/Bottom N Analysis](#question-5--topbottom-n-analysis)
   - [Q6 – Budget vs Actual Combo Chart](#question-6--budget-vs-actual-combo-chart)
   - [Q7 – Slicers and Cross-Page Filtering](#question-7--slicers-and-cross-page-filtering)
   - [Q8 – Drill-Through Page for Store Details](#question-8--drill-through-page-for-store-details)
   - [Q9 – Custom Tooltip Page](#question-9--custom-tooltip-page)
   - [Q10 – Bookmarks and Navigation Buttons](#question-10--bookmarks-and-navigation-buttons)
   - [Q11 – Conditional Formatting on Tables](#question-11--conditional-formatting-on-tables)
   - [Q12 – Q&A Visual Configuration](#question-12--qa-visual-configuration)
   - [Q13 – Key Influencers Visual](#question-13--key-influencers-visual)
   - [Q14 – Mobile Layout Design](#question-14--mobile-layout-design)
   - [Q15 – Creating a Theme and Consistent Formatting](#question-15--creating-a-theme-and-consistent-formatting)
3. [Complete Business Dashboard Design](#3-complete-business-dashboard-design)
4. [DAX Measures Reference](#4-dax-measures-reference)
5. [Key Exam Concepts Summary](#5-key-exam-concepts-summary)

---

## 1. Dashboard Overview

The Contoso Retail Group dashboard provides a three-page interactive report enabling the VP of Sales, Regional Managers, Merchandising Team, and Finance Team to monitor sales performance, product trends, and budget adherence across 120 stores.

**Dashboard pages:**

```
┌─────────────────────────────────────────────────────────────┐
│  📄 Page 1: Executive Sales Overview                        │
│  KPI cards, revenue trend, category breakdown, top stores   │
├─────────────────────────────────────────────────────────────┤
│  📄 Page 2: Product Performance                             │
│  Category treemap, product matrix, scatter analysis         │
├─────────────────────────────────────────────────────────────┤
│  📄 Page 3: Store & Regional Analysis                       │
│  Geographic map, regional bars, store drill-through         │
└─────────────────────────────────────────────────────────────┘
```

**Target audience and access:**

| Stakeholder | Pages Used | Access Level |
|-------------|-----------|--------------|
| VP of Sales | All 3 pages | Full access |
| Regional Managers | Page 1, Page 3 (filtered by region via RLS) | Region-only data |
| Merchandising Team | Page 1, Page 2 | Full access |
| Finance Team | Page 1 (Budget vs Actual focus) | Full access |

---

## 2. Visualization Questions

### Question 1 – Creating KPI Cards

**Scenario:** The VP of Sales wants to see four headline metrics at the top of the Executive Sales Overview page: Total Revenue, Total Profit, Transaction Count, and Average Order Value. Each card should show a conditional color — green when the value exceeds the prior year and red when it does not.

**Task:** Create four KPI card visuals with conditional formatting based on year-over-year performance.

#### ✅ Step-by-Step Solution

**Step 1: Create the DAX measures**

```dax
Total Revenue =
SUMX(
    FactSales,
    FactSales[Quantity] * FactSales[UnitPrice] - FactSales[DiscountAmount]
)
```

```dax
Total Profit =
SUMX(
    FactSales,
    (FactSales[UnitPrice] - RELATED(DimProduct[UnitCost])) * FactSales[Quantity]
        - FactSales[DiscountAmount]
)
```

```dax
Transaction Count =
COUNTROWS(FactSales)
```

```dax
Avg Order Value =
DIVIDE([Total Revenue], [Transaction Count], 0)
```

```dax
Revenue PY =
CALCULATE(
    [Total Revenue],
    SAMEPERIODLASTYEAR(DimDate[Date])
)
```

```dax
Revenue YoY Color =
IF([Total Revenue] > [Revenue PY], "#00B050", "#FF0000")
```

**Step 2: Insert a Card visual**

1. From the **Visualizations** pane, select the **Card** visual
2. Drag `[Total Revenue]` into the **Fields** well
3. In the **Format** pane → **Callout value** → set font size to **28** and display units to **Auto**

**Step 3: Apply conditional formatting**

1. Select the Card visual
2. **Format** pane → **Callout value** → **Color** → click **fx**
3. Set **Format style** to **Field value**
4. Select the `[Revenue YoY Color]` measure
5. Click **OK**

**Step 4: Repeat for all four cards**

| Card | Measure | Conditional Color Measure |
|------|---------|--------------------------|
| Total Revenue | `[Total Revenue]` | `[Revenue YoY Color]` |
| Total Profit | `[Total Profit]` | `[Profit YoY Color]` (same pattern) |
| Transaction Count | `[Transaction Count]` | `[Transactions YoY Color]` |
| Avg Order Value | `[Avg Order Value]` | `[AOV YoY Color]` |

**Step 5: Add subtitle labels**

1. **Format** pane → **Category label** → toggle **On**
2. This shows the measure name below the value
3. Alternatively, use a **Multi-row Card** to display all four KPIs in one visual

> **Exam Tip:** Card visuals display a single aggregate value. Use conditional formatting with a DAX measure (field value approach) for dynamic colors. Multi-row Cards show multiple values in one visual but offer less formatting control per value.

---

### Question 2 – Revenue Trend Line Chart

**Scenario:** The VP of Sales wants to see a 24-month revenue trend with this year vs. last year on the same chart, using a secondary Y-axis for year-over-year growth percentage.

**Task:** Build a combo chart showing monthly revenue for the current and prior year with a variance trend line.

#### ✅ Step-by-Step Solution

**Step 1: Create the time intelligence measures**

```dax
Revenue PY =
CALCULATE(
    [Total Revenue],
    SAMEPERIODLASTYEAR(DimDate[Date])
)
```

```dax
Revenue YoY % =
DIVIDE(
    [Total Revenue] - [Revenue PY],
    [Revenue PY],
    0
)
```

**Step 2: Insert a Line and Clustered Column Chart**

1. From the **Visualizations** pane, select **Line and Clustered Column Chart**
2. Configure the field wells:

| Field Well | Field |
|-----------|-------|
| **X-axis** | `DimDate[MonthName]` |
| **Column y-axis** | `[Total Revenue]`, `[Revenue PY]` |
| **Line y-axis** | `[Revenue YoY %]` |

**Step 3: Fix month sorting**

1. Select `DimDate[MonthName]` in the **Data** pane
2. **Column tools** → **Sort by column** → select `DimDate[MonthNumber]`
3. This ensures months display in calendar order (Jan → Dec), not alphabetical

**Step 4: Format the visual**

1. **Format** pane → **Columns** → set Current Year color to **blue (#4472C4)** and Prior Year to **grey (#A5A5A5)**
2. **Lines** → set YoY % line to **orange (#ED7D31)** with dashed style
3. **Y-axis (secondary)** → set format to **Percentage** with 1 decimal place
4. **Data labels** → toggle **On** for the line series only

**Step 5: Add a trend reference line**

1. Select the visual → **Analytics** pane → **Constant line** → **Add**
2. Set value to **0** on the secondary axis to create a zero baseline for YoY %

> **Exam Tip:** Combo charts (Line and Clustered Column) support two Y-axes, making them ideal for comparing absolute values with percentage changes. Always sort MonthName by MonthNumber — this is a common exam question. The secondary axis allows different scales.

---

### Question 3 – Revenue by Product Category

**Scenario:** The Merchandising Team wants to see revenue broken down by product category, with the ability to drill down from Category to SubCategory to individual products.

**Task:** Create a stacked bar chart with a drill-down hierarchy.

#### ✅ Step-by-Step Solution

**Step 1: Build the product hierarchy**

1. In the **Data** pane, expand `DimProduct`
2. Right-click `Category` → **New hierarchy** → rename to **Product Hierarchy**
3. Drag `SubCategory` onto the hierarchy (below Category)
4. Drag `ProductName` onto the hierarchy (below SubCategory)

**Step 2: Insert a Stacked Bar Chart**

1. Select **Stacked Bar Chart** from the Visualizations pane
2. Configure the fields:

| Field Well | Field |
|-----------|-------|
| **Y-axis** | `Product Hierarchy` (Category → SubCategory → ProductName) |
| **X-axis** | `[Total Revenue]` |
| **Legend** | (leave empty for single color, or add `DimDate[Year]` for year comparison) |

**Step 3: Enable drill-down controls**

1. The drill-down icons appear in the top-right of the visual header:
   - **Drill down** (single arrow down) — click a bar to drill into that category
   - **Go to next level** (double arrow down) — expands all items to the next level
   - **Drill up** (arrow up) — returns to the previous level
2. Click the **Drill down** toggle (single arrow) to enable click-to-drill behavior

**Step 4: Add data labels and formatting**

1. **Format** pane → **Data labels** → toggle **On**
2. Set **Display units** to **Thousands** or **Millions** as appropriate
3. **Sort** the visual descending by Total Revenue: click the ellipsis (…) → **Sort descending** → **Total Revenue**

**Step 5: Add a visual-level filter for minimum revenue**

1. In the **Filters** pane → drag `[Total Revenue]` to **Filters on this visual**
2. Set filter type to **Advanced filtering** → is greater than **$10,000**
3. This removes insignificant categories from the view

> **Exam Tip:** Hierarchies enable drill-down in visuals. Drill-down filters the visual to the selected item at the next level; "Go to next level" expands all items. The exam tests the difference between drill-down (interactive) and drill-through (page navigation). Visual-level Top N filters are separate from slicers.

---

### Question 4 – Geographic Sales Map

**Scenario:** The VP of Sales wants a geographic view showing sales by state across the US, where color intensity represents revenue volume and bubble size represents transaction count.

**Task:** Create a filled map visualization for state-level sales analysis.

#### ✅ Step-by-Step Solution

**Step 1: Verify the data category for geographic fields**

1. In the **Data** pane, select `DimStore[State]`
2. **Column tools** → **Data category** → set to **State or Province**
3. Select `DimStore[Country]` → set Data category to **Country/Region**

**Step 2: Insert a Filled Map**

1. Select **Filled Map** from the Visualizations pane
2. Configure the fields:

| Field Well | Field |
|-----------|-------|
| **Location** | `DimStore[State]` |
| **Legend** | `DimStore[Region]` (optional — colors by region) |
| **Tooltips** | `[Total Revenue]`, `[Transaction Count]`, `[Total Profit]` |

**Step 3: Configure color saturation**

1. Remove **Legend** if using color saturation instead
2. Drag `[Total Revenue]` to the **Color saturation** field well
3. **Format** pane → **Fill colors** → click **fx**
4. Set:
   - **Minimum** color: Light blue (#DEEBF7)
   - **Maximum** color: Dark blue (#08306B)
   - This creates a gradient where darker states have higher revenue

**Step 4: Add tooltips**

1. Drag `[Total Revenue]`, `[Total Profit]`, and `[Transaction Count]` to the **Tooltips** well
2. Hover over a state to see summary data

**Step 5: Alternative — Bubble Map**

1. If you need to show multiple dimensions (size + color), use a **Map** (bubble) visual instead:
   - **Location**: `DimStore[State]`
   - **Size**: `[Total Revenue]`
   - **Color saturation**: `[Profit Margin %]`

> **Exam Tip:** Set the correct **Data category** (State, City, Country) for geographic fields — this is required for maps to geocode correctly. Filled Maps color regions on a map; Bubble Maps plot dots with variable size. Use Latitude/Longitude for precise positioning when city names are ambiguous.

---

### Question 5 – Top/Bottom N Analysis

**Scenario:** The VP of Sales wants to see the top 10 stores by revenue and the bottom 5 stores by profit margin on the same dashboard page.

**Task:** Create two bar charts using visual-level Top N and Bottom N filters.

#### ✅ Step-by-Step Solution

**Step 1: Create supporting measures**

```dax
Profit Margin % =
DIVIDE([Total Profit], [Total Revenue], 0)
```

**Step 2: Build the Top 10 Stores chart**

1. Insert a **Clustered Bar Chart**
2. Configure:
   - **Y-axis**: `DimStore[StoreName]`
   - **X-axis**: `[Total Revenue]`
3. Open the **Filters** pane → expand the filter for `DimStore[StoreName]` under **Filters on this visual**
4. Change filter type to **Top N**
5. Set: **Show items — Top 10** → by value `[Total Revenue]`
6. Click **Apply filter**
7. Sort descending by `[Total Revenue]`

**Step 3: Build the Bottom 5 Stores chart**

1. Insert another **Clustered Bar Chart**
2. Configure:
   - **Y-axis**: `DimStore[StoreName]`
   - **X-axis**: `[Profit Margin %]`
3. Open the **Filters** pane → expand the filter for `DimStore[StoreName]`
4. Change filter type to **Top N**
5. Set: **Show items — Bottom 5** → by value `[Profit Margin %]`
6. Click **Apply filter**

**Step 4: Add conditional formatting to the bottom chart**

1. Select the Bottom 5 chart
2. **Format** pane → **Columns** → **Color** → click **fx**
3. **Format style**: Rules
4. Rule 1: If `[Profit Margin %]` < 0.10 → Red (#FF0000)
5. Rule 2: If `[Profit Margin %]` ≥ 0.10 → Yellow (#FFC000)

**Step 5: Add visual titles**

1. **Format** pane → **Title** → text: **"Top 10 Stores by Revenue"**
2. Repeat for the bottom chart: **"Bottom 5 Stores by Profit Margin"**

> **Exam Tip:** Top N filtering is applied at the visual level in the Filters pane, not through slicers. You can filter by Top or Bottom and specify the count. The "by value" field determines the ranking measure. Top N filters work with any visual type that has a categorical axis.

---

### Question 6 – Budget vs Actual Combo Chart

**Scenario:** The Finance Team needs a monthly view comparing actual revenue against budget, with a variance percentage trend line. They also want to instantly see which months exceeded or missed budget.

**Task:** Build a combo chart with budget vs actual columns and a variance line.

#### ✅ Step-by-Step Solution

**Step 1: Create the budget measures**

```dax
Total Budget =
SUM(FactBudget[BudgetAmount])
```

```dax
Budget Variance =
[Total Revenue] - [Total Budget]
```

```dax
Budget Variance % =
DIVIDE([Budget Variance], [Total Budget], 0)
```

**Step 2: Insert a Line and Clustered Column Chart**

1. Select **Line and Clustered Column Chart**
2. Configure the fields:

| Field Well | Field |
|-----------|-------|
| **X-axis** | `DimDate[MonthName]` (sorted by MonthNumber) |
| **Column y-axis** | `[Total Revenue]`, `[Total Budget]` |
| **Line y-axis** | `[Budget Variance %]` |

**Step 3: Format the columns**

1. **Format** pane → **Columns**:
   - `[Total Revenue]` color: **Blue (#4472C4)**
   - `[Total Budget]` color: **Grey (#A5A5A5)** with a dashed border pattern

**Step 4: Add a zero reference line**

1. Select the visual → **Analytics** pane
2. **Constant line** → **Add**
3. Set value to **0** for the secondary axis (variance %)
4. Set line style to **dashed**, color to **black**

**Step 5: Add conditional formatting to the variance line**

1. Create a color measure:

```dax
Variance Color =
IF([Budget Variance] >= 0, "#00B050", "#FF0000")
```

2. **Format** pane → **Lines** → **Color** → click **fx**
3. **Format style**: Field value → select `[Variance Color]`

> **Exam Tip:** Combo charts are the preferred visual for actual vs. budget comparisons with a variance overlay. Place absolute values (revenue, budget) on the primary column axis and percentages (variance %) on the secondary line axis. The exam often asks which visual best compares two measures with a trend.

---

### Question 7 – Slicers and Cross-Page Filtering

**Scenario:** All dashboard pages need consistent filtering by Year, Quarter, Region, and Product Category. Filters selected on one page should apply to all pages.

**Task:** Configure synced slicers across all three report pages.

#### ✅ Step-by-Step Solution

**Step 1: Add slicers to Page 1**

1. Insert a **Slicer** visual → drag `DimDate[Year]` into the **Field** well
2. **Format** pane → **Slicer settings** → **Style** → select **Dropdown**
3. Insert another Slicer → `DimDate[Quarter]` → Style: **Tile**
4. Insert another Slicer → `DimStore[Region]` → Style: **Tile**
5. Insert another Slicer → `DimProduct[Category]` → Style: **Dropdown**

**Step 2: Configure slicer options**

| Slicer | Style | Selection Type | Default |
|--------|-------|---------------|---------|
| Year | Dropdown | Single select | Current year |
| Quarter | Tile | Multi-select (Ctrl+click) | All |
| Region | Tile | Multi-select | All |
| Category | Dropdown | Multi-select | All |

1. For single-select: **Format** pane → **Selection** → toggle **Single select** on
2. For multi-select: leave Single select off (default allows multi-select)

**Step 3: Sync slicers across pages**

1. Select a slicer on Page 1
2. Go to **View** tab → **Sync slicers** (opens the sync slicers pane)
3. In the Sync slicers pane, check the **Sync** column for all three pages
4. Check the **Visible** column for pages where the slicer should appear
5. Repeat for each slicer

| Slicer | Sync Pages | Visible On |
|--------|-----------|------------|
| Year | Page 1, 2, 3 | Page 1, 2, 3 |
| Quarter | Page 1, 2, 3 | Page 1, 2, 3 |
| Region | Page 1, 3 | Page 1, 3 |
| Category | Page 1, 2 | Page 1, 2 |

**Step 4: Apply a slicer group (optional)**

1. Select all slicers on Page 1 → right-click → **Group** → **Group**
2. This allows you to move and resize all slicers together
3. Position the slicer group at the top of the page

**Step 5: Set default slicer values**

1. Select the Year slicer → choose the current year
2. Go to **Bookmarks** pane → click **Add** → name it "Default View"
3. In the bookmark options, check **Data** (to save filter selections) and **Current page**
4. Set this bookmark as the default by right-clicking → **Default bookmark**

> **Exam Tip:** Synced slicers ensure consistent filtering across pages. Configure in the **Sync slicers** pane (View tab). You can sync without making the slicer visible on every page. Bookmarks can store default slicer selections. The exam tests the difference between synced slicers and page-level vs. report-level filters.

---

### Question 8 – Drill-Through Page for Store Details

**Scenario:** Regional managers want to right-click on any store in a chart or map and navigate to a detailed Store Performance page showing that store's revenue breakdown, top products, and monthly sales trend.

**Task:** Create and configure a drill-through page for individual store analysis.

#### ✅ Step-by-Step Solution

**Step 1: Create the drill-through target page**

1. Add a new report page → rename to **"Store Details"**
2. **Format** pane (page level) → **Page information** → set **Page type** to standard

**Step 2: Configure the drill-through field**

1. On the **Store Details** page, locate the **Drill-through** section in the Visualizations pane
2. Drag `DimStore[StoreName]` into the **Drill-through filters** well
3. Power BI automatically adds a **Back** button to the page

**Step 3: Add visuals to the Store Details page**

| Visual | Type | Configuration |
|--------|------|---------------|
| Store Name Header | Card | `DimStore[StoreName]` — displays the filtered store |
| Revenue KPI | Card | `[Total Revenue]` |
| Profit KPI | Card | `[Total Profit]` |
| Monthly Trend | Line Chart | X: `DimDate[MonthName]`, Y: `[Total Revenue]` |
| Top 10 Products | Horizontal Bar Chart | Y: `DimProduct[ProductName]`, X: `[Total Revenue]` with Top 10 filter |
| Sales by Day of Week | Column Chart | X: `DimDate[DayName]`, Y: `[Transaction Count]` |
| Transaction Details | Table | `DimDate[Date]`, `DimProduct[ProductName]`, `FactSales[Quantity]`, `[Total Revenue]` |

**Step 4: Format the Back button**

1. Select the auto-created Back button
2. **Format** pane → **Button style** → **Icon** → choose a left-arrow icon
3. Position in the top-left corner
4. Set tooltip text to **"Back to dashboard"**

**Step 5: Test the drill-through**

1. Navigate to Page 1 (Executive Sales Overview)
2. Right-click any data point that includes store context (e.g., a bar in the Top 10 Stores chart)
3. Select **Drill through** → **Store Details**
4. Verify the page opens filtered to the selected store
5. Click the **Back** button to return

**Step 6: Keep all filters option**

1. On the Store Details page, in the **Drill-through filters** area, toggle **Keep all filters** to **On**
2. This carries over any slicer selections (Year, Quarter) from the source page

> **Exam Tip:** Drill-through is configured on the **target** page by adding fields to the drill-through well. It is triggered by right-clicking a data point on any other page. Cross-report drill-through works across reports in the same workspace. Always include a Back button for navigation.

---

### Question 9 – Custom Tooltip Page

**Scenario:** When users hover over a state on the geographic map, they want to see a mini-dashboard showing that state's revenue, profit margin, top 3 products, and a revenue trend sparkline.

**Task:** Create a custom tooltip page and assign it to the map visual.

#### ✅ Step-by-Step Solution

**Step 1: Create the tooltip page**

1. Add a new report page → rename to **"State Tooltip"**
2. Go to **Format** pane (page level) → **Canvas settings**:
   - **Type**: Tooltip
   - This automatically sets the page size to **320 × 240 pixels**
3. Under **Page information** → toggle **Allow use as tooltip** to **On**

**Step 2: Add mini-visuals to the tooltip page**

Because tooltip pages are small (320 × 240 px), use compact visuals:

| Visual | Type | Configuration |
|--------|------|---------------|
| State Name | Card | `DimStore[State]` — small font |
| Revenue | Card | `[Total Revenue]` — formatted as currency |
| Profit Margin | Card | `[Profit Margin %]` — formatted as percentage |
| Top 3 Products | Table | `DimProduct[ProductName]`, `[Total Revenue]` with Top 3 filter |
| Revenue Trend | Sparkline (in a Card) | Use the **Sparkline** feature in a Card visual |

**Step 3: Configure the sparkline**

1. Insert a **Card** visual on the tooltip page
2. Drag `[Total Revenue]` to the **Fields** well
3. In the **Fields** well, click the dropdown on `[Total Revenue]` → **Add a sparkline**
4. Set the sparkline **X-axis** to `DimDate[Date]`
5. This shows a mini trend line inside the card

**Step 4: Assign the tooltip page to the map**

1. Navigate to Page 1 → select the **Filled Map** visual
2. **Format** pane → **General** → **Tooltips**
3. Set **Type** to **Report page**
4. Set **Page** to **"State Tooltip"**

**Step 5: Test the tooltip**

1. Hover over any state on the map
2. The custom tooltip page appears showing the mini-dashboard for that state
3. Adjust visual sizes on the tooltip page if the layout is too cramped

> **Exam Tip:** Tooltip pages must have the page type set to **Tooltip** (320 × 240 px) and **Allow use as tooltip** enabled. Assign the tooltip to a visual via **Format > Tooltips > Report page**. Tooltip pages are filtered by the context of the hover point. This is a frequently tested feature in the PL-300.

---

### Question 10 – Bookmarks and Navigation Buttons

**Scenario:** The VP of Sales wants a navigation bar at the top of the report with buttons for each page, plus two preset views: "Current Year Overview" and "Year-over-Year Comparison."

**Task:** Create page navigation buttons and bookmark-based preset views.

#### ✅ Step-by-Step Solution

**Step 1: Create page navigation buttons**

1. On Page 1, go to **Insert** tab → **Buttons** → **Navigator** → **Page navigator**
2. Power BI automatically creates buttons for all visible pages
3. Alternatively, create custom buttons:
   - **Insert** → **Buttons** → **Blank**
   - Set button text: "Executive Overview", "Product Performance", "Store Analysis"

**Step 2: Configure button actions**

1. Select the "Product Performance" button
2. **Format** pane → **Button** → **Action** → toggle **On**
3. Set **Type** to **Page navigation**
4. Set **Destination** to **Page 2: Product Performance**
5. Repeat for each button

**Step 3: Style the navigation bar**

1. Select all navigation buttons → **Format** → **Button style**:
   - **Default state**: Background color **#F2F2F2**, font color **#333333**
   - **On hover**: Background color **#4472C4**, font color **#FFFFFF**
   - **Selected**: Background color **#2F5496**, font color **#FFFFFF**
2. Align buttons horizontally at the top of the page
3. Use **Format** → **Align** → **Distribute horizontally** for even spacing

**Step 4: Create bookmark-based preset views**

1. Set up the "Current Year Overview" view:
   - Set the Year slicer to the current year
   - Clear all other slicer selections
   - Go to **View** tab → **Bookmarks** pane → click **Add**
   - Rename the bookmark to **"Current Year Overview"**
   - Right-click the bookmark → ensure **Data** is checked (saves filter state)

2. Set up the "Year-over-Year Comparison" view:
   - Set the Year slicer to include current and prior year
   - Go to the Bookmarks pane → click **Add**
   - Rename to **"YoY Comparison"**

**Step 5: Link bookmarks to buttons**

1. Insert two additional buttons: "Current Year" and "YoY Comparison"
2. Select the "Current Year" button → **Format** → **Action**:
   - **Type**: Bookmark
   - **Bookmark**: Current Year Overview
3. Repeat for the "YoY Comparison" button

**Step 6: Copy navigation bar to all pages**

1. Select all navigation buttons → **Ctrl+C**
2. Navigate to Page 2 → **Ctrl+V**
3. Navigate to Page 3 → **Ctrl+V**
4. Verify button actions still work after pasting

> **Exam Tip:** Page navigation buttons use the **Page navigation** action type. Bookmarks save filter states, visual visibility, and page position. Combine bookmarks with buttons for guided analytics. The **Page navigator** (auto-generated) is the fastest way to add navigation. Know the difference between Data bookmarks (save filters) and Display bookmarks (save visual visibility).

---

### Question 11 – Conditional Formatting on Tables

**Scenario:** The Merchandising Team wants a product performance table with:
- Green/yellow/red background on profit margin
- Data bars on the revenue column
- Up/down arrow icons on the YoY growth column

**Task:** Apply multiple conditional formatting types to a table visual.

#### ✅ Step-by-Step Solution

**Step 1: Create the table visual**

1. Insert a **Table** visual
2. Add columns:
   - `DimProduct[Category]`
   - `DimProduct[SubCategory]`
   - `[Total Revenue]`
   - `[Total Profit]`
   - `[Profit Margin %]`
   - `[Revenue YoY %]`

**Step 2: Apply background color rules to Profit Margin**

1. Select the Table → **Format** pane → **Cell elements** → select **Profit Margin %**
2. Toggle **Background color** → click **fx**
3. Set **Format style** to **Rules**:

| Rule | Condition | Color |
|------|-----------|-------|
| 1 | Value ≥ 0.20 | 🟢 Green (#00B050) |
| 2 | Value ≥ 0.10 AND < 0.20 | 🟡 Yellow (#FFC000) |
| 3 | Value < 0.10 | 🔴 Red (#FF0000) |

4. Click **OK**

**Step 3: Add data bars to Total Revenue**

1. **Cell elements** → select **Total Revenue**
2. Toggle **Data bars** → click **fx**
3. Configure:
   - **Positive bar**: Blue (#4472C4)
   - **Negative bar**: Red (#FF0000)
   - **Bar direction**: Left-to-right
   - Toggle **Show bar only** to **Off** (show both bar and value)

**Step 4: Add icons to Revenue YoY %**

1. **Cell elements** → select **Revenue YoY %**
2. Toggle **Icons** → click **fx**
3. Configure:
   - **Icon style**: Arrows (three arrows)
   - **Icon layout**: Left of data

| Rule | Condition | Icon |
|------|-----------|------|
| 1 | Value ≥ 0.05 | 🟢 ↑ Green up arrow |
| 2 | Value ≥ -0.05 AND < 0.05 | 🟡 → Yellow right arrow |
| 3 | Value < -0.05 | 🔴 ↓ Red down arrow |

**Step 5: Format the table header and font**

1. **Format** pane → **Column headers**:
   - Background: Dark blue (#2F5496)
   - Font color: White (#FFFFFF)
   - Font size: 10, Bold
2. **Values** → Font size: 9, Alternate row color: Light grey (#F2F2F2)

> **Exam Tip:** Conditional formatting supports four types: background color, font color, data bars, and icons. Each is configured per column via the **Cell elements** section. Rules-based formatting uses UI-defined thresholds; field-value formatting references a DAX measure. The exam expects you to know all four formatting types and when each is appropriate.

---

### Question 12 – Q&A Visual Configuration

**Scenario:** The VP of Sales wants a natural language query box where users can type questions like "total revenue by region last quarter" and get instant chart results.

**Task:** Configure a Q&A visual with synonyms and suggested questions.

#### ✅ Step-by-Step Solution

**Step 1: Insert the Q&A visual**

1. From the Visualizations pane, select the **Q&A** visual
2. Resize it to span a reasonable width (e.g., half the page width)
3. The visual displays a text box where users type natural language questions

**Step 2: Test default behavior**

1. Click the Q&A visual and type: **"total revenue by region"**
2. Power BI interprets the query and generates an appropriate visual (likely a bar chart)
3. Try: **"show me monthly revenue trend for 2024"**
4. Try: **"what is the average order value by category"**

**Step 3: Add synonyms for better recognition**

1. Go to **Modeling** tab → **Q&A setup** (or click the gear icon in the Q&A visual)
2. Under the **Teach Q&A** tab:
   - Add synonym: **"sales"** → maps to `[Total Revenue]`
   - Add synonym: **"income"** → maps to `[Total Revenue]`
   - Add synonym: **"profit"** → maps to `[Total Profit]`
   - Add synonym: **"store location"** → maps to `DimStore[State]`
   - Add synonym: **"product type"** → maps to `DimProduct[Category]`

**Step 4: Add suggested questions**

1. In Q&A setup → **Suggest questions** tab
2. Add suggested questions:
   - "Total revenue by region"
   - "Top 10 products by revenue"
   - "Monthly revenue trend for 2024"
   - "Revenue by category and quarter"
   - "Which stores have the lowest profit margin?"

**Step 5: Review unrecognized terms**

1. In Q&A setup → **Review questions** tab
2. Review any questions that users have asked that were not understood
3. Add mappings for frequently used terms

> **Exam Tip:** The Q&A visual uses AI to interpret natural language queries. Synonyms improve recognition accuracy. Suggested questions guide users toward productive queries. Q&A setup is done through the **Modeling** tab or the visual's gear icon. The exam may test where to configure synonyms and how to improve Q&A accuracy.

---

### Question 13 – Key Influencers Visual

**Scenario:** The VP of Sales wants to understand what factors drive high-revenue transactions. Specifically, what product attributes, store characteristics, or customer segments are associated with transactions exceeding $500 in value?

**Task:** Configure a Key Influencers visual to analyze high-value transactions.

#### ✅ Step-by-Step Solution

**Step 1: Create a high-value flag measure or column**

```dax
High Value Transaction =
IF(
    FactSales[Quantity] * FactSales[UnitPrice] - FactSales[DiscountAmount] > 500,
    "High Value",
    "Standard"
)
```

> **Note:** Create this as a **calculated column** on FactSales, not a measure, because Key Influencers analyzes row-level data.

**Step 2: Insert the Key Influencers visual**

1. From the Visualizations pane, select **Key Influencers**
2. Configure the fields:

| Field Well | Field |
|-----------|-------|
| **Analyze** | `FactSales[High Value Transaction]` |
| **Explain by** | `DimProduct[Category]`, `DimProduct[Brand]`, `DimStore[Region]`, `DimStore[StoreType]`, `DimCustomer[Segment]`, `FactSales[Quantity]` |

**Step 3: Set the analysis target**

1. In the visual, the dropdown at the top shows: **"What influences High Value Transaction to be ___"**
2. Select **"High Value"** from the dropdown
3. The visual displays factors ranked by influence strength

**Step 4: Interpret the results**

The Key Influencers visual shows:
- **Left panel**: Ranked list of factors (e.g., "When Category is Electronics, the likelihood of High Value is 3.5x more likely")
- **Right panel**: A supporting chart showing the distribution

**Step 5: Switch to Top Segments view**

1. Click the **Top segments** tab at the top of the visual
2. This shows clusters of rows that share similar characteristics
3. Example segment: "Electronics + Premium customers + East region = 85% high-value transactions"

> **Exam Tip:** Key Influencers requires a categorical target in the **Analyze** field and potential explanatory fields in **Explain by**. It uses logistic regression and decision trees internally. The **Top segments** tab shows clusters. This visual is important for the "Identify patterns and trends" exam objective.

---

### Question 14 – Mobile Layout Design

**Scenario:** Regional managers frequently check the dashboard on their phones. The report needs a mobile-optimized layout for Page 1 (Executive Sales Overview).

**Task:** Create a mobile layout that prioritizes KPIs and key trends.

#### ✅ Step-by-Step Solution

**Step 1: Switch to mobile layout view**

1. Navigate to Page 1
2. Go to **View** tab → click **Mobile layout**
3. The canvas switches to a phone-shaped portrait view (typically 9:16 ratio)

**Step 2: Add visuals to the mobile canvas**

The mobile canvas shows all page visuals in a panel on the right. Drag visuals onto the phone canvas:

| Priority | Visual | Size on Mobile |
|----------|--------|---------------|
| 1 (top) | Year Slicer | Small — full width |
| 2 | KPI Cards (Revenue, Profit) | Medium — side by side |
| 3 | KPI Cards (Transactions, AOV) | Medium — side by side |
| 4 | Revenue Trend Line Chart | Large — full width |
| 5 | Revenue by Category Bar Chart | Large — full width |
| 6 | Top 10 Stores Table | Large — full width |

**Step 3: Optimize visual sizes**

1. Drag each visual onto the mobile canvas
2. Resize visuals to fit the phone width (typically stacked vertically)
3. KPI cards can be placed 2 per row
4. Charts should span full width for readability

**Step 4: Remove non-essential visuals**

1. Do NOT add the geographic map (too detailed for mobile)
2. Do NOT add large tables with many columns
3. Focus on the most actionable visuals

**Step 5: Test in the Power BI mobile app**

1. Publish the report to the Power BI service
2. Open the **Power BI Mobile** app on your phone
3. Navigate to the report — the mobile layout appears automatically
4. Verify that all slicers and interactions work correctly

> **Exam Tip:** Mobile layout is configured in **View > Mobile layout** in Power BI Desktop. It creates a separate layout that does not affect the desktop report. Visuals must be dragged from the panel — they are not automatically included. The Power BI mobile app detects and displays the mobile layout automatically. The exam tests where to create mobile layouts and best practices.

---

### Question 15 – Creating a Theme and Consistent Formatting

**Scenario:** Contoso Retail Group has brand guidelines specifying exact colors, fonts, and formatting. The dashboard must use a consistent custom theme across all pages.

**Task:** Create a Power BI JSON theme file with Contoso brand colors and apply it to the report.

#### ✅ Step-by-Step Solution

**Step 1: Define the Contoso brand colors**

| Purpose | Color Name | Hex Code |
|---------|-----------|----------|
| Primary | Contoso Blue | #1A3A5C |
| Secondary | Contoso Teal | #2E8B8B |
| Accent 1 | Contoso Orange | #E87722 |
| Accent 2 | Contoso Green | #4CAF50 |
| Neutral | Light Grey | #F5F5F5 |
| Text | Dark Grey | #333333 |

**Step 2: Create the JSON theme file**

Create a file named `contoso-theme.json`:

```json
{
    "name": "Contoso Retail",
    "dataColors": [
        "#1A3A5C",
        "#2E8B8B",
        "#E87722",
        "#4CAF50",
        "#FFC107",
        "#9C27B0",
        "#00BCD4",
        "#FF5722"
    ],
    "background": "#FFFFFF",
    "foreground": "#333333",
    "tableAccent": "#1A3A5C",
    "visualStyles": {
        "*": {
            "*": {
                "title": [{
                    "fontFamily": "Segoe UI Semibold",
                    "fontSize": 12,
                    "color": "#333333"
                }],
                "background": [{
                    "color": "#FFFFFF",
                    "transparency": 0
                }]
            }
        }
    }
}
```

**Step 3: Apply the theme to the report**

1. In Power BI Desktop, go to **View** tab → **Themes** → **Browse for themes**
2. Select the `contoso-theme.json` file
3. All visuals across all pages update to use the Contoso colors
4. New visuals automatically inherit the theme

**Step 4: Customize additional formatting**

1. **View** → **Themes** → **Customize current theme**
2. Adjust:
   - **Text**: Set default font to Segoe UI
   - **Cards and KPIs**: Set callout value color to Contoso Blue
   - **Page background**: Set to Light Grey (#F5F5F5)
   - **Visual border**: Toggle On, 1px, Light Grey (#E0E0E0)

**Step 5: Export and share the theme**

1. **View** → **Themes** → **Save current theme**
2. Save as `contoso-theme.json`
3. Share with other report authors for consistency
4. Apply the same theme to all Contoso reports

> **Exam Tip:** JSON themes control colors, fonts, backgrounds, and visual formatting across an entire report. Apply via **View > Themes > Browse for themes**. The `dataColors` array defines the color sequence for chart series. Custom themes ensure brand consistency without manual formatting. The exam may present a JSON theme snippet and ask what property controls a specific format.

---

## 3. Complete Business Dashboard Design

### Page 1: Executive Sales Overview

```
┌──────────────────────────────────────────────────────────────────┐
│ [🏠 Executive Overview] [📦 Products] [🏪 Stores & Regions]      │
│ [📅 Current Year ▼] [🔄 YoY Comparison]                         │
├──────────────────────────────────────────────────────────────────┤
│ [Slicer: Year ▼] [Slicer: Quarter ▪▪▪▪] [Slicer: Region ▪▪▪▪]  │
├──────────┬───────────┬────────────┬──────────────────────────────┤
│ Card:    │ Card:     │ Card:      │ Card:                        │
│ Revenue  │ Profit    │ Transact.  │ Avg Order                    │
│ $12.5M   │ $3.2M     │ 45,200     │ $276.55                      │
│ ▲ +8.5%  │ ▲ +5.2%   │ ▲ +12.1%   │ ▼ -3.2%                      │
├──────────┴───────────┴────────────┴──────────────────────────────┤
│                                                                  │
│  Line & Clustered Column Chart:                                  │
│  Monthly Revenue: Current Year vs Prior Year                     │
│  ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██                             │
│  ▓▓ ▓▓ ▓▓ ▓▓ ▓▓ ▓▓ ▓▓ ▓▓ ▓▓ ▓▓ ▓▓ ▓▓  + ── YoY % line        │
│  Jan Feb Mar Apr May Jun Jul Aug Sep Oct Nov Dec                 │
├───────────────────────────────┬──────────────────────────────────┤
│                               │                                  │
│  Stacked Bar Chart:           │  Clustered Bar Chart:            │
│  Revenue by Category          │  Top 10 Stores by Revenue        │
│  ████████████ Electronics     │  ████████████ Store A            │
│  ██████████ Home & Garden     │  ███████████ Store B             │
│  ████████ Clothing            │  ██████████ Store C              │
│  ██████ Sports                │  █████████ Store D               │
│  ████ Books                   │  ████████ Store E                │
│                               │                                  │
└───────────────────────────────┴──────────────────────────────────┘
```

**Visual field configuration:**

| Visual | Type | Fields | Format |
|--------|------|--------|--------|
| Revenue Card | Card | `[Total Revenue]` | Currency, 1 decimal, conditional color |
| Profit Card | Card | `[Total Profit]` | Currency, 1 decimal, conditional color |
| Transactions Card | Card | `[Transaction Count]` | Whole number, conditional color |
| AOV Card | Card | `[Avg Order Value]` | Currency, 2 decimals, conditional color |
| Revenue Trend | Line + Column | X: `DimDate[MonthName]`, Columns: `[Total Revenue]` & `[Revenue PY]`, Line: `[Revenue YoY %]` | Blue/grey columns, orange line |
| Category Revenue | Stacked Bar | Y: `DimProduct[Category]`, X: `[Total Revenue]` | Sorted descending, data labels |
| Top 10 Stores | Clustered Bar | Y: `DimStore[StoreName]`, X: `[Total Revenue]`, Top 10 filter | Sorted descending, data labels |

**Cross-filtering settings:**

1. Select the Category bar chart → **Format** → **Edit interactions**
2. Set the Top 10 Stores chart to **Filter** (not highlight)
3. Set the Revenue Trend chart to **Highlight**
4. This means clicking a category filters the stores list but only highlights the trend

---

### Page 2: Product Performance

```
┌──────────────────────────────────────────────────────────────────┐
│ [🏠 Executive Overview] [📦 Products] [🏪 Stores & Regions]      │
├──────────────────────────────────────────────────────────────────┤
│ [Slicer: Year ▼] [Slicer: Quarter ▪▪▪▪] [Slicer: Category ▼]   │
├──────────────────────────────┬───────────────────────────────────┤
│                              │                                   │
│  Treemap:                    │  Matrix:                          │
│  Revenue by Category →       │  Category | Revenue | Profit |    │
│  SubCategory                 │           |        | Margin |    │
│  ┌──────────┬──────┐        │  ─────────┼────────┼────────┤    │
│  │Electronics│Cloth.│        │  Electr.  │ $5.2M  │  22%   │    │
│  │          │      │        │  Home     │ $3.1M  │  28%   │    │
│  │          ├──────┤        │  Clothing │ $2.4M  │  35%   │    │
│  │          │Sports│        │  Sports   │ $1.2M  │  18%   │    │
│  └──────────┴──────┘        │  Books    │ $0.6M  │  42%   │    │
│                              │                                   │
├──────────────────────────────┼───────────────────────────────────┤
│                              │                                   │
│  Scatter Chart:              │  Column Chart:                    │
│  Revenue vs Profit Margin    │  Revenue by SubCategory           │
│     •  •                     │  ████                             │
│   •    •  •                  │  ████ ████                        │
│  •  •     •                  │  ████ ████ ████                   │
│  (bubble = transaction count)│  ████ ████ ████ ████             │
│                              │                                   │
└──────────────────────────────┴───────────────────────────────────┘
```

**Visual field configuration:**

| Visual | Type | Fields |
|--------|------|--------|
| Treemap | Treemap | Group: `DimProduct[Category]` → `DimProduct[SubCategory]`, Values: `[Total Revenue]` |
| Product Matrix | Matrix | Rows: `DimProduct[Category]`, Values: `[Total Revenue]`, `[Total Profit]`, `[Profit Margin %]`, `[Revenue YoY %]` |
| Scatter Chart | Scatter | X: `[Total Revenue]`, Y: `[Profit Margin %]`, Size: `[Transaction Count]`, Details: `DimProduct[SubCategory]` |
| SubCategory Revenue | Clustered Column | X: `DimProduct[SubCategory]`, Y: `[Total Revenue]`, Legend: `DimProduct[Category]` |

**Matrix conditional formatting:**

- `[Profit Margin %]`: Background color rules (green ≥ 20%, yellow ≥ 10%, red < 10%)
- `[Revenue YoY %]`: Icons (green up arrow ≥ 5%, yellow right arrow ≥ -5%, red down arrow < -5%)
- `[Total Revenue]`: Data bars (blue)

---

### Page 3: Store & Regional Analysis

```
┌──────────────────────────────────────────────────────────────────┐
│ [🏠 Executive Overview] [📦 Products] [🏪 Stores & Regions]      │
├──────────────────────────────────────────────────────────────────┤
│ [Slicer: Year ▼] [Slicer: Quarter ▪▪▪▪] [Slicer: Region ▪▪▪▪]  │
├──────────────────────────────┬───────────────────────────────────┤
│                              │                                   │
│  Filled Map:                 │  Clustered Bar Chart:             │
│  Revenue by State            │  Revenue by Region                │
│  (color intensity = revenue) │  ████████████ Northeast           │
│                              │  ██████████ Southeast             │
│  [Custom tooltip on hover]   │  █████████ Midwest                │
│                              │  ████████ West                    │
│                              │                                   │
├──────────────────────────────┼───────────────────────────────────┤
│                              │                                   │
│  Table: Store Performance    │  Column Chart:                    │
│  Store | Revenue | Margin |  │  Budget vs Actual by Region       │
│  ──────┼─────────┼────────│  │  ██ ██  ██ ██  ██ ██  ██ ██     │
│  A     │ $1.2M   │ 22%   │  │  NE    SE     MW    W            │
│  B     │ $1.1M   │ 19%   │  │  ██ = Actual  ▓▓ = Budget        │
│  [Right-click → Drill       │  │                                   │
│   through to Store Details]  │                                   │
│                              │                                   │
└──────────────────────────────┴───────────────────────────────────┘
```

**Visual field configuration:**

| Visual | Type | Fields |
|--------|------|--------|
| Filled Map | Filled Map | Location: `DimStore[State]`, Color saturation: `[Total Revenue]`, Tooltips: `[Total Revenue]`, `[Profit Margin %]` |
| Region Revenue | Clustered Bar | Y: `DimStore[Region]`, X: `[Total Revenue]` |
| Store Table | Table | `DimStore[StoreName]`, `DimStore[Region]`, `[Total Revenue]`, `[Total Profit]`, `[Profit Margin %]` with conditional formatting |
| Budget vs Actual | Clustered Column | X: `DimStore[Region]`, Y: `[Total Revenue]` & `[Total Budget]` |

---

## 4. DAX Measures Reference

### Sales Metrics

```dax
Total Revenue =
SUMX(
    FactSales,
    FactSales[Quantity] * FactSales[UnitPrice] - FactSales[DiscountAmount]
)
```

```dax
Total Profit =
SUMX(
    FactSales,
    (FactSales[UnitPrice] - RELATED(DimProduct[UnitCost])) * FactSales[Quantity]
        - FactSales[DiscountAmount]
)
```

```dax
Transaction Count =
COUNTROWS(FactSales)
```

```dax
Avg Order Value =
DIVIDE([Total Revenue], [Transaction Count], 0)
```

```dax
Profit Margin % =
DIVIDE([Total Profit], [Total Revenue], 0)
```

### Budget Metrics

```dax
Total Budget =
SUM(FactBudget[BudgetAmount])
```

```dax
Budget Variance =
[Total Revenue] - [Total Budget]
```

```dax
Budget Variance % =
DIVIDE([Budget Variance], [Total Budget], 0)
```

### Time Intelligence

```dax
Revenue PY =
CALCULATE(
    [Total Revenue],
    SAMEPERIODLASTYEAR(DimDate[Date])
)
```

```dax
Revenue YoY % =
DIVIDE(
    [Total Revenue] - [Revenue PY],
    [Revenue PY],
    0
)
```

```dax
Revenue YTD =
TOTALYTD([Total Revenue], DimDate[Date])
```

```dax
Revenue QTD =
TOTALQTD([Total Revenue], DimDate[Date])
```

```dax
Revenue MTD =
TOTALMTD([Total Revenue], DimDate[Date])
```

```dax
Revenue Rolling 12M =
CALCULATE(
    [Total Revenue],
    DATESINPERIOD(DimDate[Date], MAX(DimDate[Date]), -12, MONTH)
)
```

### Conditional Formatting Helpers

```dax
Revenue YoY Color =
IF([Total Revenue] > [Revenue PY], "#00B050", "#FF0000")
```

```dax
Profit YoY Color =
IF([Total Profit] > CALCULATE([Total Profit], SAMEPERIODLASTYEAR(DimDate[Date])),
    "#00B050", "#FF0000")
```

```dax
Variance Color =
IF([Budget Variance] >= 0, "#00B050", "#FF0000")
```

### Ranking Measures

```dax
Store Revenue Rank =
RANKX(
    ALL(DimStore[StoreName]),
    [Total Revenue],
    ,
    DESC,
    DENSE
)
```

```dax
Product Revenue Rank =
RANKX(
    ALL(DimProduct[ProductName]),
    [Total Revenue],
    ,
    DESC,
    DENSE
)
```

---

## 5. Key Exam Concepts Summary

| Concept | Domain | Key Takeaway |
|---------|--------|-------------|
| **Card visuals** | Visualize | Display single aggregate values; use conditional formatting for dynamic colors |
| **Combo charts** | Visualize | Line + Column chart for comparing measures on dual Y-axes (e.g., actuals vs budget with variance line) |
| **Drill-down hierarchies** | Visualize | Built by adding multiple fields to a hierarchy; controlled via visual header icons |
| **Top N filters** | Visualize | Configured at the visual level in the Filters pane; specify count and ranking measure |
| **Filled Map vs Bubble Map** | Visualize | Filled Map colors regions; Bubble Map plots sized dots; both need correct Data categories |
| **Synced slicers** | Visualize | Configured in View > Sync slicers; can sync without being visible on every page |
| **Drill-through** | Visualize | Configured on the target page; triggered by right-click; carries filter context |
| **Tooltip pages** | Visualize | Page type = Tooltip (320 × 240 px); assigned via visual Format > Tooltips |
| **Bookmarks** | Visualize | Save filter state and visual visibility; link to buttons for guided analytics |
| **Conditional formatting** | Visualize | Four types: background color, font color, data bars, icons; rules-based or field-value |
| **Q&A visual** | Visualize | Natural language queries; improve with synonyms; configure in Modeling > Q&A setup |
| **Key Influencers** | Visualize | Analyzes categorical outcomes; uses logistic regression; Explain by fields provide context |
| **Mobile layout** | Visualize | View > Mobile layout; separate layout; prioritize KPIs; auto-detected by mobile app |
| **JSON themes** | Visualize | Control colors, fonts, backgrounds; apply via View > Themes > Browse for themes |
| **Cross-filtering** | Visualize | Edit interactions to set Filter vs Highlight behavior between visuals |

---

*This dashboard guide is a companion to [Case Study 1 – Retail Sales Analytics](case-study-1-retail-sales.md). Complete both for full PL-300 exam preparation.*
