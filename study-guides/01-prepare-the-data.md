# Domain 1: Prepare the Data (25–30%)

> **Exam Weight:** 25–30% | **Estimated Study Time:** 2 weeks

This domain covers connecting to data sources, profiling and cleaning data, and transforming data using Power Query. Mastery of Power Query Editor and the M language is essential.

---

## Table of Contents

1. [Get or Connect to Data](#1-get-or-connect-to-data)
2. [Profile and Clean the Data](#2-profile-and-clean-the-data)
3. [Transform and Load the Data](#3-transform-and-load-the-data)
4. [Common Exam Pitfalls](#4-common-exam-pitfalls)
5. [Key Terms Glossary](#5-key-terms-glossary)

---

## 1. Get or Connect to Data

### 1.1 Data Source Categories

Power BI Desktop supports hundreds of data sources organised into four categories:

| Category | Examples |
|---|---|
| **File** | Excel (.xlsx, .xlsm), CSV/Text, XML, JSON, PDF, SharePoint Folder |
| **Database** | SQL Server, Oracle, MySQL, PostgreSQL, Azure SQL Database, Snowflake, Redshift |
| **Power Platform** | Power BI datasets, Dataflows, Common Data Service |
| **Online Services** | SharePoint Online, Dynamics 365, Salesforce, Google Analytics, Azure Blob Storage |
| **Other** | Web (HTML scraping), OData Feed, ODBC, OLEDB, Blank Query |

> **Exam Tip:** Know which category each common source belongs to. PDF is under **File**. Web is under **Other**.

### 1.2 Storage Modes: Import vs DirectQuery vs Live Connection vs Composite

This is one of the most heavily tested topics in Domain 1.

#### Import Mode
- Data is **copied** into Power BI's in-memory VertiPaq engine
- Fastest query performance (data in memory)
- Data is as fresh as the last refresh
- Maximum dataset size: 1 GB compressed (Pro), 100 GB (Premium)
- Supports all Power Query transformations and all DAX functions
- **Use when:** data doesn't change constantly; performance is critical

#### DirectQuery Mode
- Data is **never imported**; every visual sends a query to the source in real time
- Always shows current data
- Limited transformations in Power Query
- Not all DAX functions supported (no time intelligence without special config)
- Performance depends on the source database
- **Use when:** data is huge (> 1 GB), near real-time data is required, or governance requires data not leave the source

#### Live Connection
- Similar to DirectQuery but connects to an existing **Power BI dataset** or **Analysis Services model**
- No Power Query transformations available (model is fixed)
- No local model — all metadata comes from the remote model
- **Use when:** reusing a centralised certified dataset across multiple reports

#### Composite Models
- Combines **Import + DirectQuery** tables in a single model
- Some tables are imported (for performance) and others are DirectQuery (for freshness)
- Requires careful planning of cross-source relationships
- Supports Aggregations for performance optimization
- **Use when:** you need both performance and real-time data in the same model

| Feature | Import | DirectQuery | Live Connection | Composite |
|---|---|---|---|---|
| Data stored in Power BI | ✅ Yes | ❌ No | ❌ No | Partial |
| Real-time data | ❌ No | ✅ Yes | ✅ Yes | Partial |
| All DAX supported | ✅ Yes | ⚠️ Limited | ⚠️ Limited | Partial |
| All PQ transforms | ✅ Yes | ⚠️ Limited | ❌ No | Partial |
| Max performance | ✅ Fastest | ❌ Slowest | Medium | Medium |

### 1.3 Parameters

Parameters make queries dynamic and reusable — they're a key exam topic.

**Creating a Parameter:**
1. In Power Query Editor: **Home → Manage Parameters → New Parameter**
2. Set Name, Type, Default Value, Current Value
3. Reference the parameter in any query step

**Parameter Types:** Text, Decimal Number, Whole Number, Date/Time, Date, Time, Duration, Logical, Binary, Any

**Using Parameters in M Code:**
```m
// Define a parameter named "ServerName" with value "SQLPROD01"
// Reference it in a source step:
let
    Source = Sql.Database(ServerName, "AdventureWorks")
in
    Source
```

**What-If Parameters (in the data model, not Power Query):**
- Created via **Modeling → New Parameter**
- Generates a disconnected table and a measure
- Used to allow report users to adjust values dynamically (e.g., discount rate)

### 1.4 Data Source Credentials and Authentication

When connecting to data sources, Power BI prompts for credentials. Authentication methods vary by source:

| Method | When Used |
|---|---|
| **Anonymous** | Public web pages, open OData feeds |
| **Windows** | On-premises SQL Server with Windows Auth |
| **Basic** | Username/password for databases, web APIs |
| **Organizational Account** | Microsoft 365, Azure AD–protected services |
| **API Key** | Web services requiring API key in header |
| **OAuth2** | Third-party services (Google, Salesforce) |

> **Exam Tip:** Credentials are stored per data source URL. If a URL changes, credentials must be re-entered. Manage credentials in **File → Options → Data Source Settings**.

### 1.5 Privacy Levels

Privacy levels control how Power Query combines data from different sources (query folding and mashup engine behaviour). This is a frequently tested topic.

| Privacy Level | Description | When to Use |
|---|---|---|
| **None** | No privacy setting applied | Single-source environments only |
| **Public** | Data can be shared with any source; non-sensitive | Publicly available data (web, open datasets) |
| **Organizational** | Can be shared within the organisation only | Internal SharePoint, internal databases |
| **Private** | Data cannot be shared with other sources | Sensitive PII, financial data, credentials |

**How Privacy Levels Work in Practice:**
- When combining a **Private** source with a **Public** source, Power BI shows a warning
- Setting levels incorrectly can cause data leakage between queries
- To combine sources without errors, levels must be compatible or you must enable **"Ignore Privacy Levels"** (not recommended in production)

**Setting Privacy Levels:**
- Per data source: **File → Options → Privacy** → set level per source
- Global setting: **File → Options → Global → Privacy** → can set to "Always ignore"

---

## 2. Profile and Clean the Data

### 2.1 Data Profiling Tools in Power Query

Power Query Editor provides three profiling views under the **View** tab:

#### Column Quality
Shows three bars per column:
- **Valid** (green) — percentage of rows with no errors or null
- **Error** (red) — percentage of rows with errors
- **Empty** (grey) — percentage of rows with null or blank values

> **Exam Tip:** "Empty" includes both null values AND empty strings.

#### Column Distribution
- Shows a histogram of value distribution
- Displays **Distinct** count (unique values including nulls) and **Unique** count (values that appear exactly once)
- Useful for spotting skew, outliers, and identifying potential key columns

#### Column Profile
- Shows detailed statistics: Min, Max, Average, Standard Deviation, Count, Distinct Count, Null Count
- Shows a value distribution chart
- By default, profiling is based on the **top 1,000 rows** — change to entire dataset via the status bar: **"Column profiling based on top 1000 rows"** → click to switch to **"entire dataset"**

> **Exam Tip:** For accurate profiling, always switch to "entire dataset" before making decisions.

### 2.2 Identifying and Resolving Data Quality Issues

#### Handling Null Values
```m
// Replace nulls with a specific value
= Table.ReplaceValue(Source, null, 0, Replacer.ReplaceValue, {"SalesAmount"})

// Remove rows where a specific column is null
= Table.SelectRows(Source, each [CustomerID] <> null)

// Fill null values down (forward fill)
= Table.FillDown(Source, {"Category"})

// Fill null values up (back fill)
= Table.FillUp(Source, {"Category"})
```

#### Handling Errors
```m
// Replace errors with null
= Table.ReplaceErrorValues(Source, {{"Price", null}})

// Remove rows with errors in any column
= Table.RemoveRowsWithErrors(Source)

// Remove rows with errors in specific columns
= Table.RemoveRowsWithErrors(Source, {"Quantity", "Price"})
```

#### Removing Duplicates
```m
// Remove duplicate rows based on all columns
= Table.Distinct(Source)

// Remove duplicates based on specific columns (keeps first occurrence)
= Table.Distinct(Source, {"OrderID"})
```

#### Trimming and Cleaning Text
```m
// Remove leading and trailing spaces
= Table.TransformColumns(Source, {{"CustomerName", Text.Trim}})

// Remove all non-printable characters
= Table.TransformColumns(Source, {{"Notes", Text.Clean}})

// Both trim and clean
= Table.TransformColumns(Source, {{"Name", each Text.Trim(Text.Clean(_))}})
```

### 2.3 Column-Level Data Statistics

Understanding column statistics helps make modelling decisions:

| Statistic | Meaning | Relevance |
|---|---|---|
| **Count** | Total row count | Data volume |
| **Distinct** | Unique values including nulls | Cardinality indicator |
| **Unique** | Values appearing exactly once | Identifies natural keys |
| **Empty** | Null + blank count | Data quality |
| **Error** | Error count | Data quality |
| **Min / Max** | Value range | Outlier detection |
| **Average** | Mean value | Central tendency |

### 2.4 Resolving Inconsistent Data

**Standardising Text Values:**
```m
// Capitalise first letter of each word
= Table.TransformColumns(Source, {{"City", Text.Proper}})

// Convert to uppercase
= Table.TransformColumns(Source, {{"CountryCode", Text.Upper}})

// Convert to lowercase
= Table.TransformColumns(Source, {{"Email", Text.Lower}})

// Replace specific values
= Table.ReplaceValue(Source, "UK", "United Kingdom", Replacer.ReplaceText, {"Country"})
```

**Fixing Date Formats:**
```m
// Parse text dates to date type
= Table.TransformColumns(Source, {{"OrderDate", each Date.From(DateTimeZone.From(_)), type date}})
```

---

## 3. Transform and Load the Data

### 3.1 Column Data Types

Setting correct data types is critical for both performance and accuracy.

| Power BI Type | Description | Examples |
|---|---|---|
| **Whole Number** | 64-bit integer | Counts, IDs |
| **Decimal Number** | Floating point (64-bit) | Prices, measurements |
| **Fixed Decimal Number** | Currency (4 decimal places) | Financial amounts |
| **Text** | Unicode string | Names, codes |
| **True/False** | Boolean | Flags |
| **Date** | Date only | Birthdate, OrderDate |
| **Time** | Time only | ShiftStart |
| **Date/Time** | Combined | Timestamps |
| **Date/Time/Timezone** | With UTC offset | Cross-timezone data |
| **Duration** | Time span | Elapsed time |
| **Binary** | Raw bytes | File contents |

```m
// Change column type in M code
= Table.TransformColumnTypes(Source, {
    {"OrderDate", type date},
    {"Quantity", Int64.Type},
    {"UnitPrice", type number}
})
```

> **Exam Tip:** **Fixed Decimal Number** is preferred for currency — it avoids floating-point rounding errors.

### 3.2 Creating Custom Columns

**Custom Column (using M formula):**
```m
// In Power Query: Add Column → Custom Column
// Example: Calculate line total
= [Quantity] * [UnitPrice] * (1 - [Discount])
```

**Conditional Column (UI-based):**
- Add Column → Conditional Column
- Equivalent M code:
```m
= Table.AddColumn(Source, "SizeCategory", each
    if [Units] >= 1000 then "Large"
    else if [Units] >= 100 then "Medium"
    else "Small"
)
```

**Column from Examples:**
- Add Column → Column from Examples
- Type example values; Power BI infers the M transformation
- Great for common text extractions (first name from full name, year from date)

### 3.3 Grouping and Aggregating Rows

Group By is used to aggregate data at a higher grain:

```m
// Basic Group By: sum sales by Category
= Table.Group(Source, {"Category"}, {
    {"TotalSales", each List.Sum([SalesAmount]), type number},
    {"OrderCount", each Table.RowCount(_), Int64.Type}
})

// Advanced: multiple keys and multiple aggregations
= Table.Group(Source, {"Year", "Category"}, {
    {"TotalSales", each List.Sum([SalesAmount]), type number},
    {"AvgPrice", each List.Average([UnitPrice]), type number},
    {"MaxQty", each List.Max([Quantity]), type number}
})
```

### 3.4 Pivot and Unpivot

**Pivot** — Converts rows to columns (summarises data):
```m
// Pivot the "Quarter" column, using SalesAmount values, summing aggregation
= Table.Pivot(Source, List.Distinct(Source[Quarter]), "Quarter", "SalesAmount", List.Sum)
```

**Unpivot** — Converts columns to rows (normalises data):
```m
// Unpivot Q1, Q2, Q3, Q4 columns into rows
= Table.UnpivotOtherColumns(Source, {"Year", "Category"}, "Quarter", "SalesAmount")

// Unpivot specific columns only
= Table.Unpivot(Source, {"Q1", "Q2", "Q3", "Q4"}, "Quarter", "SalesAmount")
```

> **Exam Tip:** Use **Unpivot** when data arrives in "wide" format (one column per quarter/month) but you need it in "tall" format for proper modelling.

### 3.5 Merge Queries (Joins)

Merge combines two queries based on matching column(s) — equivalent to SQL JOIN.

| Merge Type | SQL Equivalent | Description |
|---|---|---|
| **Left Outer** | LEFT JOIN | All rows from left; matching from right |
| **Right Outer** | RIGHT JOIN | All rows from right; matching from left |
| **Full Outer** | FULL OUTER JOIN | All rows from both tables |
| **Inner** | INNER JOIN | Only matching rows |
| **Left Anti** | LEFT EXCLUDING JOIN | Left rows with no match in right |
| **Right Anti** | RIGHT EXCLUDING JOIN | Right rows with no match in left |

```m
// Left Outer Merge: add product details to sales
= Table.NestedJoin(
    Sales, {"ProductID"},
    Products, {"ProductID"},
    "ProductDetails",
    JoinKind.LeftOuter
)

// Expand the merged table column
= Table.ExpandTableColumn(
    MergedTable,
    "ProductDetails",
    {"ProductName", "Category", "UnitCost"}
)
```

> **Exam Tip:** After a Merge, you must **Expand** the nested table column to access the joined columns. Merging on multiple keys requires selecting both columns before merging.

### 3.6 Append Queries (Union)

Append stacks rows from two or more queries — equivalent to SQL UNION ALL.

```m
// Append two tables (must have compatible column names)
= Table.Combine({SalesNorth, SalesSouth})

// Append with mismatched columns — missing columns filled with null
= Table.Combine({Table1, Table2, Table3})
```

**When to Use Append vs Merge:**
- **Append** → Adding more rows (same structure, different data)
- **Merge** → Adding more columns (lookup/join)

### 3.7 Designing Fact and Dimension Tables

Understanding fact and dimension tables is foundational for modelling:

**Fact Table Characteristics:**
- Contains **measurable, numeric data** (sales amounts, quantities)
- Has **foreign keys** referencing dimension tables
- Has many rows (high grain)
- Should be as narrow as possible
- Examples: Sales, Orders, Transactions, Events

**Dimension Table Characteristics:**
- Contains **descriptive attributes** (names, categories, hierarchies)
- Has a **primary key** (unique per row)
- Fewer rows than fact tables
- Examples: Products, Customers, Dates, Employees, Stores

**Star Schema vs Snowflake Schema:**

| Feature | Star Schema | Snowflake Schema |
|---|---|---|
| Dimension normalisation | Denormalised (flat) | Normalised (multiple tables) |
| Query performance | Faster (fewer joins) | Slower (more joins) |
| Storage | More redundant data | Less redundant |
| Recommended for Power BI | ✅ Yes | ⚠️ Usually flatten first |

> **Best Practice:** Flatten snowflake schemas into star schemas during Power Query transformations before loading into the model.

### 3.8 Managing Keys

**Surrogate Keys vs Natural Keys:**
- **Natural Key** — business key from the source (e.g., CustomerID from CRM)
- **Surrogate Key** — system-generated integer key added for performance

**Adding an Index Column (Surrogate Key):**
```m
// Add 1-based index column
= Table.AddIndexColumn(Source, "ProductKey", 1, 1, Int64.Type)
```

### 3.9 Query Folding

Query Folding is the ability of Power Query to translate M transformations back into the native query language of the data source (e.g., SQL). This is critical for performance.

**Transformations that typically fold to SQL:**
- Filter rows, Remove columns, Sort, Group By, Merge (join), Rename columns

**Transformations that break folding:**
- Adding index columns, Custom M functions, Complex conditional logic, Accessing binary data

**How to Check Query Folding:**
- Right-click a step in Applied Steps
- If **"View Native Query"** is available (not greyed out), that step folds
- If greyed out, folding has been broken at or before that step

> **Exam Tip:** Keep query folding steps **before** non-folding steps in the Applied Steps order to maximise folding.

### 3.10 Reference vs Duplicate Queries

| Feature | Reference | Duplicate |
|---|---|---|
| Creates a new query that | Points to the existing query | Copies all steps independently |
| Impact of changing source | Reference updates automatically | Duplicate is independent |
| Use case | Build on existing query | Independent variation |

```m
// A Reference query in M — it just calls another query by name
let
    Source = #"Sales Data"  // references the "Sales Data" query
in
    Source
```

### 3.11 Data Loading Options

**Configuring Load Behaviour:**
- Right-click query in Queries panel → **"Enable Load"** (toggle off to prevent loading to model)
- Disabled queries are still available as referenced by other queries but don't create tables

**Load vs Transform vs Close & Apply:**
- **Close & Apply** — applies all changes and loads data into the model
- **Apply** — applies without closing the editor
- **Close** — closes without applying (changes lost)

**Incremental Refresh (Premium/Premium Per User feature):**
- Requires defining **RangeStart** and **RangeEnd** parameters (type: Date/Time)
- Power BI partitions data into historical (archive) and recent (refresh) partitions
- Only recent partitions are refreshed on each scheduled refresh
- Reduces refresh time for large datasets significantly

```m
// Incremental Refresh filter — added automatically by Power BI
= Table.SelectRows(Source, each
    [OrderDate] >= RangeStart and [OrderDate] < RangeEnd
)
```

### 3.12 Advanced M Code Patterns

**Combining Data from a Folder:**
```m
let
    Source = Folder.Files("C:\SalesData"),
    FilteredFiles = Table.SelectRows(Source, each [Extension] = ".csv"),
    ImportedCSVs = Table.AddColumn(FilteredFiles, "Data",
        each Csv.Document([Content], [Delimiter=",", Encoding=65001])),
    CombinedData = Table.Combine(ImportedCSVs[Data])
in
    CombinedData
```

**Dynamic Date Table (in Power Query):**
```m
let
    StartDate = #date(2020, 1, 1),
    EndDate = Date.From(DateTime.LocalNow()),
    DayCount = Duration.Days(EndDate - StartDate) + 1,
    DateList = List.Dates(StartDate, DayCount, #duration(1,0,0,0)),
    DateTable = Table.FromList(DateList, Splitter.SplitByNothing(), {"Date"}),
    TypedTable = Table.TransformColumnTypes(DateTable, {{"Date", type date}}),
    AddYear = Table.AddColumn(TypedTable, "Year", each Date.Year([Date]), Int64.Type),
    AddMonth = Table.AddColumn(AddYear, "MonthNumber", each Date.Month([Date]), Int64.Type),
    AddMonthName = Table.AddColumn(AddMonth, "MonthName", each Date.ToText([Date], "MMMM"), type text),
    AddQuarter = Table.AddColumn(AddMonthName, "Quarter", each "Q" & Text.From(Date.QuarterOfYear([Date])), type text)
in
    AddQuarter
```

**Error Handling Pattern:**
```m
let
    SafeConvert = (val) =>
        let result = try Number.From(val) otherwise null
        in result,
    AddSafeColumn = Table.AddColumn(Source, "SafeAmount",
        each SafeConvert([RawAmount]), type number)
in
    AddSafeColumn
```

---

## 4. Common Exam Pitfalls

| Pitfall | Correct Understanding |
|---|---|
| Thinking DirectQuery supports all DAX | DirectQuery has limitations; time intelligence requires special setup |
| Confusing Merge and Append | Merge = join (adds columns); Append = union (adds rows) |
| Forgetting to change profiling to "entire dataset" | Default is top 1,000 rows only |
| Assuming Privacy Levels don't matter | Wrong levels can block query execution or expose sensitive data |
| Using Duplicate instead of Reference | Reference propagates source changes; Duplicate is independent |
| Ignoring query folding | Non-folding transforms on large datasets cause severe performance issues |
| Setting all columns as Text | Numeric columns stored as Text cannot be aggregated or used in relationships |
| Leaving "Enable Load" on for staging queries | Staging queries should have "Enable Load" turned off |

---

## 5. Key Terms Glossary

| Term | Definition |
|---|---|
| **Applied Steps** | The recorded sequence of Power Query transformations |
| **M Language** | The functional language used by Power Query |
| **Query Folding** | Translating Power Query steps into native source SQL |
| **Composite Model** | A model combining Import and DirectQuery tables |
| **Incremental Refresh** | Refreshing only recent data partitions |
| **Privacy Level** | Controls how data sources share data during query mashup |
| **Parameter** | A named, reusable value used in Power Query queries |
| **Surrogate Key** | A system-generated integer primary key |
| **Star Schema** | A denormalised dimensional model with one fact and multiple dimension tables |
| **Grain** | The level of detail in a fact table row |

---

## Quick Revision Summary

- **Import** = fastest, data in memory, scheduled refresh needed
- **DirectQuery** = real-time, no data stored, limited DAX/transforms
- **Live Connection** = connects to existing PBI dataset or AS model
- **Composite** = mix Import + DirectQuery in one model
- **Privacy Levels**: Private → Organizational → Public (in order of restriction)
- **Column Quality** shows Valid/Error/Empty percentages
- **Column Distribution** shows distinct vs unique counts
- **Merge** = JOIN (adds columns) | **Append** = UNION (adds rows)
- **Pivot** = rows to columns | **Unpivot** = columns to rows
- **Query Folding** = Power BI pushes transforms to source = better performance
- **Enable Load = OFF** for staging/helper queries that shouldn't create model tables
