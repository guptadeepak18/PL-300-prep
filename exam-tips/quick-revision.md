# Last-Night Revision Cheat Sheet

> **Exam:** PL-300 Microsoft Power BI Data Analyst | **Purpose:** Rapid Final Review
> **Usage:** Read through the night before or morning of your exam for a quick mental refresh.

---

## Table of Contents

1. [Domain 1 Quick Facts: Prepare the Data (25–30%)](#1-domain-1-quick-facts-prepare-the-data-2530)
2. [Domain 2 Quick Facts: Model the Data (25–30%)](#2-domain-2-quick-facts-model-the-data-2530)
3. [Domain 3 Quick Facts: Visualize and Analyze (25–30%)](#3-domain-3-quick-facts-visualize-and-analyze-2530)
4. [Domain 4 Quick Facts: Manage and Secure (15–20%)](#4-domain-4-quick-facts-manage-and-secure-1520)
5. [Key DAX Functions Quick Reference](#5-key-dax-functions-quick-reference)
6. [Key M Functions Quick Reference](#6-key-m-functions-quick-reference)
7. [Decision Trees](#7-decision-trees)
8. [Number Facts to Remember](#8-number-facts-to-remember)

---

## 1. Domain 1 Quick Facts: Prepare the Data (25–30%)

### 1.1 Storage Modes Comparison

| Feature | Import | DirectQuery | Live Connection | Composite |
|---|---|---|---|---|
| **Data Location** | In-memory (VertiPaq) | Source system | Source system (SSAS) | Mixed |
| **Performance** | ✅ Fastest | ⚠️ Depends on source | ⚠️ Depends on source | ✅ Good balance |
| **Data Freshness** | At last refresh | ✅ Real-time | ✅ Real-time | Mixed |
| **Transformation** | ✅ Full Power Query | ❌ Limited | ❌ None | ✅ Per-table choice |
| **Dataset Size** | 1 GB / 10 GB Premium | No limit | No limit | Mixed |
| **DAX Support** | ✅ Full | ⚠️ Partial | ⚠️ Limited to model | ✅ Full |
| **Data Modeling** | ✅ Full | ✅ Full | ❌ No changes | ✅ Full |
| **Offline Use** | ✅ Yes | ❌ No | ❌ No | Partial |

> **Exam Tip:** Composite models let you set storage mode **per table**. Use
> Import for dimensions (small, rarely changed) and DirectQuery for fact tables
> (large, needs freshness).

### 1.2 Privacy Levels

| Level | Description | Can Combine With |
|---|---|---|
| **Private** | Isolated — only visible within its query scope | Nothing |
| **Organizational** | Shared across organizational data sources | Organizational only |
| **Public** | No restrictions — visible to all data sources | Everything |

- Privacy levels are set **per data source** in Power Query
- Mismatched privacy levels cause the **"Formula.Firewall"** error
- Setting privacy to **None/Ignore** bypasses checks but may leak data

### 1.3 Query Folding

**Query folding** means Power Query pushes transformations back to the source
(translated to native SQL, OData, etc.).

**Operations that typically fold:**
- Filter rows, Remove columns, Sort, Rename columns
- Group by, Merge (joins), Top N rows
- Data type changes, Replace values

**Operations that typically break folding:**
- Add custom columns with M logic
- Pivot/Unpivot (depends on source)
- Merge queries from different sources
- Import from files (CSV, Excel) — no folding possible
- Adding an index column

> **Exam Tip:** Right-click a step in Power Query. If "View Native Query" is
> grayed out, that step **is not folding**. Steps after a non-folding step also
> won't fold.

### 1.4 Merge Types (Joins)

| Merge Type | Returns |
|---|---|
| **Left Outer** | All rows from left + matches from right |
| **Right Outer** | All rows from right + matches from left |
| **Full Outer** | All rows from both tables |
| **Inner** | Only matching rows from both |
| **Left Anti** | Left rows with **no** match in right |
| **Right Anti** | Right rows with **no** match in left |

- **Append** = stack tables vertically (UNION)
- **Merge** = join tables horizontally (JOIN)

### 1.5 Data Profiling Tools

| Tool | What It Shows |
|---|---|
| **Column Quality** | % Valid, Error, Empty for each column |
| **Column Distribution** | Distinct and Unique value counts |
| **Column Profile** | Full statistics — min, max, avg, std dev, value distribution chart |

- By default, profiling is based on the **top 1,000 rows**
- Change to **"entire dataset"** in the status bar for accurate profiling

### 1.6 Common M Functions to Know

```m
// Connecting to data
Sql.Database("server", "database")
Excel.Workbook(File.Contents("path"))
Csv.Document(File.Contents("path"))
Web.Contents("url")

// Transformations
Table.SelectRows(table, each [Column] > 100)
Table.RemoveColumns(table, {"Col1", "Col2"})
Table.RenameColumns(table, {{"Old", "New"}})
Table.AddColumn(table, "NewCol", each [A] + [B])
Table.TransformColumnTypes(table, {{"Col", type text}})
Table.Group(table, {"GroupCol"}, {{"Sum", each List.Sum([Values])}})

// Combining
Table.NestedJoin(left, "Key", right, "Key", "Joined", JoinKind.LeftOuter)
Table.Combine({table1, table2})

// Pivoting
Table.Pivot(table, List.Distinct(table[Col]), "Col", "Values")
Table.Unpivot(table, {"Col1", "Col2"}, "Attribute", "Value")
```

### 1.7 Parameters

- **Parameters** allow dynamic data source connections and filter values
- Created in Power Query via **Manage Parameters**
- Data types: Text, Decimal Number, True/False, Date, DateTime, etc.
- Used in **incremental refresh** (`RangeStart`, `RangeEnd` of type DateTime)
- Can be bound to **What-If parameters** in the model (creates a disconnected table)

---

## 2. Domain 2 Quick Facts: Model the Data (25–30%)

### 2.1 Star Schema Design Rules

- **Fact tables** store numeric measures and foreign keys (tall, narrow)
- **Dimension tables** store descriptive attributes (short, wide)
- Relationships flow from **dimension → fact** (one-to-many)
- Avoid **snowflake schemas** where possible — flatten dimensions
- Create a **dedicated Date table** — do not rely on auto date/time

### 2.2 Relationship Types and Cardinality

| Cardinality | Symbol | Usage |
|---|---|---|
| **One-to-Many** | 1:* | Standard — Dimension to Fact |
| **Many-to-One** | *:1 | Reverse direction of above |
| **One-to-One** | 1:1 | Rare — usually merge these tables |
| **Many-to-Many** | *:* | Bridge tables — use with caution |

- Active relationships: **only one** active relationship between two tables
- Inactive relationships: activated with `USERELATIONSHIP()` in DAX

### 2.3 Cross-Filter Direction

| Direction | Behavior | When to Use |
|---|---|---|
| **Single** | Filters flow from "one" to "many" side only | ✅ Default — use whenever possible |
| **Bidirectional** | Filters flow in both directions | ⚠️ Use sparingly — many-to-many bridges |

**Risks of bidirectional filtering:**
- Ambiguous filter paths
- Performance degradation
- Unexpected results in visuals

> **Exam Tip:** If a question asks about filtering from a fact table back to a
> dimension, the answer might involve `CROSSFILTER` in DAX rather than changing
> the relationship to bidirectional.

### 2.4 CALCULATE Filter Context Rules

`CALCULATE` is the most important DAX function. It evaluates an expression in
a **modified filter context**.

```dax
CALCULATE(<expression>, <filter1>, <filter2>, ...)
```

**How CALCULATE works:**
1. Copies the current filter context
2. Applies each filter argument (overriding existing filters on the same column)
3. Evaluates the expression in the new filter context

**Key behaviors:**
- Boolean filters like `Table[Column] = "Value"` are shorthand for `FILTER(ALL(Table[Column]), ...)`
- Multiple filters on **different columns** = AND logic
- Multiple filters on the **same column** = last one wins (override)
- Use `KEEPFILTERS` to intersect instead of override

```dax
-- Overrides any existing Country filter
Sales US = CALCULATE(SUM(Sales[Amount]), Sales[Country] = "US")

-- Preserves existing filter and intersects
Sales US Keep = CALCULATE(SUM(Sales[Amount]), KEEPFILTERS(Sales[Country] = "US"))
```

### 2.5 Time Intelligence Quick Reference

| Function | Returns |
|---|---|
| `DATESYTD(dates)` | Year-to-date dates |
| `DATESQTD(dates)` | Quarter-to-date dates |
| `DATESMTD(dates)` | Month-to-date dates |
| `SAMEPERIODLASTYEAR(dates)` | Same period shifted back 1 year |
| `DATEADD(dates, -1, YEAR)` | Shift dates back by interval |
| `PREVIOUSMONTH(dates)` | Previous full month |
| `PREVIOUSQUARTER(dates)` | Previous full quarter |
| `PREVIOUSYEAR(dates)` | Previous full year |
| `TOTALYTD(expr, dates)` | Running total year-to-date |
| `PARALLELPERIOD(dates, -1, YEAR)` | Entire parallel period shifted |

**Requirements for time intelligence:**
- A **contiguous date table** (no gaps) with one row per day
- The table must be **marked as a Date table** (Table tools → Mark as Date Table)
- The date column must have data type **Date** (not DateTime)

> **Exam Tip:** `SAMEPERIODLASTYEAR` returns the same date range shifted back
> one year. `PARALLELPERIOD` returns the **entire** period. For December 15,
> `SAMEPERIODLASTYEAR` returns Dec 15 last year; `PARALLELPERIOD(..., -1, MONTH)`
> returns all of November.

### 2.6 Calculated Column vs Measure

| Factor | Calculated Column | Measure |
|---|---|---|
| **Computed** | At data refresh | At query time |
| **Stored** | ✅ Yes — in the model | ❌ No — computed on the fly |
| **Row Context** | ✅ Has access to current row | ❌ No row context by default |
| **Filter Context** | ❌ Not affected | ✅ Responds to slicers/filters |
| **Use For** | Slicing, sorting, filtering, static values | Aggregations, KPIs, dynamic calcs |
| **Memory** | ⚠️ Increases model size | ✅ No storage impact |

**Rule of thumb:** If it needs to respond to user interaction (slicers, filters), use a **measure**. If it needs to be used as a column for sorting or relationships, use a **calculated column**.

### 2.7 Performance Optimization Tips

- Remove **unused columns** — smaller model = faster queries
- Avoid **high-cardinality columns** in dimensions (e.g., GUIDs, timestamps)
- Use **integers for keys** instead of strings
- Prefer **single-direction** cross-filter relationships
- Disable **Auto date/time** (File → Options) if you have your own Date table
- Use **aggregation tables** for large datasets
- Set fact table columns to **"hidden"** so report authors use measures instead
- Avoid calculated columns when a Power Query column would work

### 2.8 Role-Playing Dimensions and USERELATIONSHIP

A **role-playing dimension** is a single dimension table used in multiple
relationships (e.g., a Date table linked to Order Date and Ship Date).

```dax
-- Only one relationship is active; use USERELATIONSHIP for the inactive one
Ship Date Sales =
CALCULATE(
    SUM(Sales[Amount]),
    USERELATIONSHIP(Sales[ShipDate], 'Date'[Date])
)
```

- Only **one** relationship between two tables can be active
- Use `USERELATIONSHIP` inside `CALCULATE` to activate an inactive relationship
- This is a very common exam scenario

---

## 3. Domain 3 Quick Facts: Visualize and Analyze (25–30%)

### 3.1 Visual Selection Guide

| Scenario | Recommended Visual |
|---|---|
| Trends over time | **Line chart** or Area chart |
| Part-to-whole composition | **Stacked bar/column** or Pie/Donut (few categories) |
| Comparison across categories | **Clustered bar/column** chart |
| Correlation between variables | **Scatter plot** |
| Geographic data | **Map** or Filled map |
| Single KPI value | **Card** or Multi-row card |
| Progress toward target | **Gauge** or KPI visual |
| Hierarchical breakdown | **Treemap** or Decomposition tree |
| Data table display | **Table** or Matrix |
| Ranking | **Funnel** chart |
| Distribution | **Histogram** (binned column chart) |
| Flow / process | **Waterfall** chart (for additive changes) |

### 3.2 Conditional Formatting Options

| Format Type | Description |
|---|---|
| **Background color** | Cell background based on value |
| **Font color** | Text color based on value |
| **Data bars** | In-cell bars proportional to value |
| **Icons** | Indicator icons (arrows, circles, flags) |
| **Web URL** | Make cell values into clickable hyperlinks |

Formatting rules can be based on:
- **Color scale** (gradient between min and max)
- **Rules** (if value > X, then color = Y)
- **Field value** (reference another column for the color)

### 3.3 Bookmarks

| Bookmark Type | Scope | Created By |
|---|---|---|
| **Report bookmarks** | Shared with all users | Report author |
| **Personal bookmarks** | Private to each user | Report consumer |

**Bookmarks capture:**
- Current filters and slicers
- Visual visibility (show/hide)
- Drill state
- Sort order
- Spotlight state
- Page selection (optional)

**Common uses:**
- Toggle between visuals (show/hide with buttons)
- Reset filters to default state
- Create guided navigation / presentation mode

### 3.4 Drill-Through vs Drill-Down

| Feature | Drill-Through | Drill-Down |
|---|---|---|
| **Navigation** | Goes to a **different page** | Stays on the **same visual** |
| **Purpose** | Show detail for a specific value | Explore hierarchy levels |
| **Setup** | Add fields to the drill-through filter well | Add hierarchy to the visual axis |
| **Trigger** | Right-click → Drill through | Click +/- icons or double-click |
| **Back Button** | Auto-created on target page | Built into visual header |

### 3.5 Tooltip Pages

- Create a **new page** and set its type to **Tooltip** in Page Format settings
- Set page size to **Tooltip** (320×240 px default)
- On any visual, set the **Tooltip page** property to your tooltip page
- Tooltip pages can contain visuals, text, images, and shapes
- They respond to the **filter context** of the hovered data point

### 3.6 AI Visuals

| Visual | Purpose | Key Feature |
|---|---|---|
| **Q&A** | Natural language queries | Users type questions; visual is auto-generated |
| **Key Influencers** | Identify factors driving a metric | Shows top factors that increase/decrease a value |
| **Decomposition Tree** | Ad-hoc root cause analysis | Users expand nodes to drill into contributing factors |
| **Smart Narrative** | Auto-generated text summaries | Dynamic text that updates with data changes |

> **Exam Tip:** Know that **Q&A** relies on a well-structured model with clear
> table and column names. **Synonyms** can be added in the model to improve Q&A
> understanding.

### 3.7 Copilot Features

- **Narrative visual** powered by Copilot generates natural language summaries
- Copilot can suggest and **create report pages** from a prompt
- Copilot can generate **DAX measures** based on natural language descriptions
- Requires **Premium/Fabric capacity** and must be enabled by admin
- Data must meet compliance requirements to use Copilot

### 3.8 Paginated Reports

- Built with **Power BI Report Builder** (separate desktop app)
- Designed for **pixel-perfect, printable** layouts (invoices, statements)
- Support **parameters** for user-driven filtering
- Can be **exported** to PDF, Excel, Word, CSV, XML
- Hosted in the **Power BI Service** (Premium capacity required)
- Different from interactive reports — optimized for multi-page printing

---

## 4. Domain 4 Quick Facts: Manage and Secure (15–20%)

### 4.1 Workspace Roles

| Permission | Admin | Member | Contributor | Viewer |
|---|---|---|---|---|
| Update/delete workspace | ✅ | ❌ | ❌ | ❌ |
| Add/remove users | ✅ | ✅ (not Admin) | ❌ | ❌ |
| Publish content | ✅ | ✅ | ✅ | ❌ |
| Create/edit/delete content | ✅ | ✅ | ✅ | ❌ |
| Share items | ✅ | ✅ | ❌ | ❌ |
| View and interact | ✅ | ✅ | ✅ | ✅ |
| Configure data refresh | ✅ | ✅ | ✅ | ❌ |
| **Bypasses RLS?** | ✅ | ✅ | ✅ | ❌ |

> **Exam Tip:** Admins, Members, and Contributors all **bypass RLS** when
> viewing data in the workspace. Only **Viewers** are subject to RLS. This is
> one of the most tested security concepts.

### 4.2 Row-Level Security (RLS)

**Static RLS:**
```dax
-- Role: "US Sales" with filter
[Country] = "US"
```
Each role has a hardcoded filter expression. Users are assigned to roles manually.

**Dynamic RLS:**
```dax
-- Role: "Regional Sales" with filter
[SalesPersonEmail] = USERPRINCIPALNAME()
```
The filter uses `USERPRINCIPALNAME()` or `USERNAME()` to automatically scope
data to the logged-in user.

| Feature | Static RLS | Dynamic RLS |
|---|---|---|
| **Filter** | Hardcoded values | `USERPRINCIPALNAME()` or `USERNAME()` |
| **Maintenance** | Create a role per group | One role for all users |
| **Scalability** | ❌ Poor (many roles) | ✅ Good (one role) |
| **Requires** | User-role mapping table | Email/username column in data |

**Testing RLS:**
- In Power BI Desktop: Modeling → View as Roles
- In Service: Dataset Settings → Row-Level Security → Test as Role
- Always test with a **Viewer** account to confirm behavior

### 4.3 Gateway Types

| Gateway | Use Case | Managed By |
|---|---|---|
| **On-premises (Standard)** | Shared across users, multiple data sources | IT / Admin |
| **On-premises (Personal)** | Single user, personal use only | Individual user |
| **VNet Gateway** | Connect to Azure VNet resources securely | Admin (Premium/Fabric) |

- Standard gateways support **scheduled refresh** and **DirectQuery/Live**
- Personal gateways support **scheduled refresh only**
- VNet gateways eliminate the need for on-premises infrastructure for Azure
  data sources

### 4.4 Scheduled Refresh Limits

| License | Max Refreshes per Day | Minimum Interval |
|---|---|---|
| **Pro** | 8 | 30 minutes |
| **Premium Per User (PPU)** | 48 | 30 minutes |
| **Premium Capacity** | 48 | 30 minutes |
| **Fabric** | 48 | Varies |

- **Incremental refresh** can extend effective refresh by only refreshing new
  or changed data
- **Dataflows** have their own refresh schedule separate from datasets
- Refresh schedules are set in the **Power BI Service** (not Desktop)

### 4.5 Deployment Pipelines

Three stages: **Development** → **Test** → **Production**

- Used to manage the **lifecycle of Power BI content**
- Compare content between stages before deploying
- **Deployment rules** can change data source connections and parameters per stage
- Requires **Premium capacity** or Fabric
- Supports datasets, reports, dashboards, paginated reports, and dataflows

### 4.6 Sensitivity Labels

| Label | Intended Use |
|---|---|
| **Public** | No restrictions — anyone can access |
| **General** | Not intended for public but no special protection |
| **Confidential** | Sensitive business data — restricted access |
| **Highly Confidential** | Most sensitive — strictest controls |

- Applied to **reports, dashboards, datasets, and dataflows**
- Labels are **inherited** downstream (dataset → report → dashboard)
- **Exported files** retain the sensitivity label (Excel, PDF, PowerPoint)
- Mandatory labeling can be enabled by the **tenant admin**
- Integrated with **Microsoft Purview Information Protection**

### 4.7 Endorsement Types

| Type | Who Can Apply | Meaning |
|---|---|---|
| **Promoted** | Any workspace member/contributor | "Recommended for use" |
| **Certified** | Designated users (set by admin) | "Meets quality standards" |

- Endorsement helps users discover **trusted, authoritative content**
- Certified content shows a **badge** in the Power BI catalog
- The admin defines who can certify content in **Admin Portal → Certification**

---

## 5. Key DAX Functions Quick Reference

### Aggregation Functions

| Function | Description |
|---|---|
| `SUM(column)` | Sum of a column |
| `AVERAGE(column)` | Mean of a column |
| `MIN(column)` | Minimum value |
| `MAX(column)` | Maximum value |
| `COUNT(column)` | Count non-blank values |
| `COUNTA(column)` | Count non-blank (includes text) |
| `COUNTROWS(table)` | Count rows in a table |
| `DISTINCTCOUNT(column)` | Count unique values |

### Iterator Functions (X-functions)

| Function | Description |
|---|---|
| `SUMX(table, expression)` | Row-by-row sum of an expression |
| `AVERAGEX(table, expression)` | Row-by-row average |
| `MINX(table, expression)` | Row-by-row minimum |
| `MAXX(table, expression)` | Row-by-row maximum |
| `COUNTX(table, expression)` | Row-by-row count of non-blank results |
| `RANKX(table, expression)` | Rank values in a table |

### Filter Functions

| Function | Description |
|---|---|
| `CALCULATE(expr, filter...)` | Evaluate expression in modified filter context |
| `FILTER(table, condition)` | Return filtered table |
| `ALL(table/column)` | Remove all filters from table or column |
| `ALLEXCEPT(table, col...)` | Remove all filters except specified columns |
| `SELECTEDVALUE(col, alt)` | Return value if single selection, else alternate |
| `HASONEVALUE(column)` | TRUE if only one value in filter context |
| `VALUES(column)` | Return distinct values respecting filters |
| `KEEPFILTERS(filter)` | Intersect instead of override in CALCULATE |
| `REMOVEFILTERS(col)` | Alias for ALL — removes filters |

### Time Intelligence Functions

| Function | Description |
|---|---|
| `TOTALYTD(expr, dates)` | Year-to-date total |
| `TOTALQTD(expr, dates)` | Quarter-to-date total |
| `TOTALMTD(expr, dates)` | Month-to-date total |
| `SAMEPERIODLASTYEAR(dates)` | Shift dates back one year |
| `DATEADD(dates, n, interval)` | Shift dates by interval |
| `DATESYTD(dates)` | Return YTD date set |
| `PREVIOUSMONTH(dates)` | Return prior month dates |
| `PREVIOUSYEAR(dates)` | Return prior year dates |

### Table Functions

| Function | Description |
|---|---|
| `RELATEDTABLE(table)` | Get related rows from another table (one-to-many side) |
| `RELATED(column)` | Get value from related table (many-to-one side) |
| `USERELATIONSHIP(col1, col2)` | Activate an inactive relationship |
| `CROSSFILTER(col1, col2, dir)` | Change cross-filter direction in a measure |
| `TREATAS(table, column...)` | Apply table as filters on columns |

---

## 6. Key M Functions Quick Reference

| Function | Description |
|---|---|
| `Table.SelectRows` | Filter rows based on a condition |
| `Table.RemoveColumns` | Remove specified columns |
| `Table.RenameColumns` | Rename one or more columns |
| `Table.AddColumn` | Add a new computed column |
| `Table.TransformColumnTypes` | Change column data types |
| `Table.Group` | Group rows and aggregate |
| `Table.NestedJoin` | Merge (join) two tables |
| `Table.Combine` | Append (union) multiple tables |
| `Table.Pivot` | Pivot rows into columns |
| `Table.Unpivot` | Unpivot columns into rows |
| `Table.UnpivotOtherColumns` | Unpivot all except specified columns |
| `Table.FillDown` | Fill null values with value above |
| `Table.FillUp` | Fill null values with value below |
| `Table.Sort` | Sort table by specified columns |
| `Table.TransformColumns` | Transform values in a column |
| `Text.Combine` | Concatenate text values |
| `Text.Split` | Split text by delimiter |
| `Date.From` | Convert value to date |
| `Number.From` | Convert value to number |
| `List.Distinct` | Return unique values from a list |

> **Exam Tip:** You don't need to memorize exact M syntax, but you should
> recognize what these functions do when you see them in a code snippet.
> The exam often shows M code and asks what the result would be.

---

## 7. Decision Trees

### 7.1 When to Use Import vs DirectQuery

```
Do you need real-time data?
├── YES → Is the data source performant enough for direct queries?
│         ├── YES → Use DirectQuery
│         └── NO  → Use Composite Model (DirectQuery + Import aggregation tables)
└── NO  → Is the dataset under 1 GB (Pro) or 10 GB (Premium)?
          ├── YES → Use Import Mode ✅ (best performance)
          └── NO  → Can you reduce the dataset with filters/aggregations?
                    ├── YES → Use Import Mode with incremental refresh
                    └── NO  → Use DirectQuery or Composite Model
```

**Quick rule:** Default to **Import** unless you have a specific reason for
DirectQuery (real-time requirements, dataset too large, source restriction).

### 7.2 Calculated Column vs Measure

```
Does the value depend on filter context (slicers, cross-filters)?
├── YES → Use a MEASURE
└── NO  → Do you need it for:
          ├── Sorting another column → Calculated Column
          ├── Row-level categorization → Calculated Column
          ├── A relationship key → Calculated Column
          └── Slicing / filtering in visuals → Calculated Column
              (but consider adding it in Power Query instead)
```

**Quick rule:** If in doubt, use a **measure**. Only use calculated columns
when you explicitly need a stored, row-level value.

### 7.3 Merge vs Append

```
What do you need to combine?
├── Two tables with the SAME structure (same columns)?
│   └── Use APPEND (stacks rows vertically — like SQL UNION)
└── Two tables with DIFFERENT structures (need to match by key)?
    └── Use MERGE (joins horizontally — like SQL JOIN)
        ├── Need all rows from left table → Left Outer
        ├── Need only matching rows → Inner
        ├── Need rows NOT in the other table → Anti joins
        └── Need all rows from both → Full Outer
```

### 7.4 Where to Transform: Power Query vs DAX

```
Is the transformation needed before the data is loaded?
├── YES → Power Query (M)
│   Examples: cleaning nulls, splitting columns, merging tables,
│   changing types, removing duplicates, unpivoting
└── NO  → Is the calculation dependent on user interaction?
          ├── YES → DAX Measure
          │   Examples: YTD totals, filtered sums, rankings, KPIs
          └── NO  → DAX Calculated Column (or better: Power Query)
              Examples: concatenated names, category flags, age from DOB
```

> **Exam Tip:** The exam strongly favors **Power Query for data
> transformation** and **DAX for business logic / measures**. If a question
> asks "where should you perform this transformation?" and Power Query is an
> option, it is almost always the correct answer.

---

## 8. Number Facts to Remember

### 8.1 Refresh & Performance

| Fact | Value |
|---|---|
| Pro scheduled refreshes per day | **8** |
| Premium scheduled refreshes per day | **48** |
| Max dataset size (Pro / shared capacity) | **1 GB** |
| Max dataset size (Premium per capacity) | **10 GB** (configurable up to 400 GB) |
| Incremental refresh parameters | **RangeStart** and **RangeEnd** (DateTime type) |
| Max rows for data profiling (default) | **1,000** (change to "entire dataset") |
| DirectQuery max rows returned | **1,000,000** per query |

### 8.2 Workspace & Sharing

| Fact | Value |
|---|---|
| Workspace roles | **4** (Admin, Member, Contributor, Viewer) |
| Roles that bypass RLS | **3** (Admin, Member, Contributor) |
| Deployment pipeline stages | **3** (Development, Test, Production) |
| Endorsement types | **2** (Promoted, Certified) |
| Sensitivity label levels | **4** (Public, General, Confidential, Highly Confidential) |

### 8.3 Relationships & Modeling

| Fact | Value |
|---|---|
| Active relationships between two tables | **1** (use USERELATIONSHIP for others) |
| Cross-filter directions | **2** (Single, Bidirectional) |
| Cardinality types | **4** (1:1, 1:*, *:1, *:*) |
| Auto date/time tables | Created per **Date/DateTime column** (disable if not needed) |

### 8.4 Visual & Report Limits

| Fact | Value |
|---|---|
| Max visuals per report page | **Recommended ≤ 20** for performance |
| Tooltip page default size | **320 × 240 px** |
| Q&A visual requirement | Well-named columns + optional synonyms |
| Bookmark types | **2** (Report bookmarks, Personal bookmarks) |
| Drill-through filter well | Accepts **any field** as a filter target |

### 8.5 Gateway & Infrastructure

| Fact | Value |
|---|---|
| Gateway types | **3** (Standard, Personal, VNet) |
| Personal gateway supports | **Scheduled refresh only** (no DirectQuery) |
| Standard gateway supports | **Scheduled refresh + DirectQuery + Live** |
| Cloud data sources | **No gateway required** |
| On-premises data sources | **Gateway required** |

### 8.6 Exam Itself

| Fact | Value |
|---|---|
| Questions | **40–60** |
| Time | **120 minutes** |
| Passing score | **700 / 1000** |
| First retake wait | **24 hours** |
| Subsequent retake wait | **14 days** |
| Case studies | **2–3** with 4–5 questions each |
| Certification renewal | **Annual** (free online assessment) |

---

## Final Reminders

- **Default to Import mode** unless you need real-time data
- **CALCULATE** is the most important DAX function — understand filter context
- **Star schema** is always the recommended modeling pattern
- **Measures** are preferred over calculated columns for dynamic calculations
- **Power Query** is for data transformation; **DAX** is for business logic
- **RLS is bypassed** by Admin, Member, and Contributor workspace roles
- **Query folding** improves performance — keep transformations foldable
- A **contiguous Date table** is required for time intelligence functions
- **Never leave a question blank** — there is no penalty for guessing

---

*Review this sheet one final time before your exam. Good luck — you've got this!*
