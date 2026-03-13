# Power Query M Code Reference

> 📋 **Purpose:** Comprehensive reference for Power Query M language functions and patterns for PL-300 exam preparation
> 🕐 **Format:** Organized by category with syntax, examples, and exam tips
> **Exam Weight:** M code knowledge spans Domain 1 (Prepare the Data) — 25–30%

---

## Table of Contents

1. [M Language Basics](#1-m-language-basics)
   - [Values and Types](#11-values-and-types)
   - [Let...In Structure](#12-letin-structure)
   - [Comments](#13-comments)
   - [Case Sensitivity](#14-case-sensitivity)
   - [Operators](#15-operators)
2. [Data Source Functions](#2-data-source-functions)
   - [File Sources](#21-file-sources)
   - [Database Sources](#22-database-sources)
   - [Web Sources](#23-web-sources)
   - [SharePoint Sources](#24-sharepoint-sources)
   - [Folder Sources](#25-folder-sources)
3. [Table Functions](#3-table-functions)
   - [Selection Functions](#31-selection-functions)
   - [Transformation Functions](#32-transformation-functions)
   - [Combine Functions](#33-combine-functions)
   - [Join Types](#34-join-types)
   - [Grouping](#35-grouping)
   - [Pivot and Unpivot](#36-pivot-and-unpivot)
   - [Add Columns](#37-add-columns)
   - [Sort Functions](#38-sort-functions)
   - [Distinct and Remove](#39-distinct-and-remove)
4. [Text Functions](#4-text-functions)
   - [Extraction Functions](#41-extraction-functions)
   - [Information Functions](#42-information-functions)
   - [Case Functions](#43-case-functions)
   - [Modification Functions](#44-modification-functions)
   - [Search Functions](#45-search-functions)
   - [Combine and Convert](#46-combine-and-convert)
5. [Number Functions](#5-number-functions)
   - [Rounding Functions](#51-rounding-functions)
   - [Math Functions](#52-math-functions)
   - [Conversion Functions](#53-conversion-functions)
6. [Date/Time Functions](#6-datetime-functions)
   - [Date Part Extraction](#61-date-part-extraction)
   - [Date Boundaries](#62-date-boundaries)
   - [Date Arithmetic](#63-date-arithmetic)
   - [Current Date/Time](#64-current-datetime)
   - [Duration Functions](#65-duration-functions)
7. [List Functions](#7-list-functions)
   - [Generation Functions](#71-generation-functions)
   - [Transformation and Selection](#72-transformation-and-selection)
   - [Aggregation Functions](#73-aggregation-functions)
   - [Set Operations](#74-set-operations)
8. [Common Patterns](#8-common-patterns)
   - [Date Dimension Table](#81-date-dimension-table)
   - [Parameterized Queries](#82-parameterized-queries)
   - [Error Handling](#83-error-handling)
   - [Conditional Columns](#84-conditional-columns)
   - [Custom Functions](#85-custom-functions)
   - [Combining Files from a Folder](#86-combining-files-from-a-folder)
   - [Dynamic Data Source with Parameters](#87-dynamic-data-source-with-parameters)
   - [Relative Date Filtering](#88-relative-date-filtering)
9. [Best Practices for PL-300 Exam](#9-best-practices-for-pl-300-exam)
   - [Query Folding](#91-query-folding)
   - [Disabling Query Load](#92-disabling-query-load)
   - [Reference vs Duplicate Queries](#93-reference-vs-duplicate-queries)
   - [Performance Tips](#94-performance-tips)

---

## 1. M Language Basics

### 1.1 Values and Types

M is a **functional, case-sensitive** language used by Power Query. Every expression evaluates to a value, and every value has a type.

| Type | Example | Description |
|---|---|---|
| **Logical** | `true`, `false` | Boolean values |
| **Number** | `42`, `3.14`, `1.5e3` | Integers and decimals |
| **Text** | `"Hello World"` | String values in double quotes |
| **Date** | `#date(2024, 1, 15)` | Date intrinsic type |
| **Time** | `#time(14, 30, 0)` | Time intrinsic type |
| **DateTime** | `#datetime(2024, 1, 15, 14, 30, 0)` | Combined date and time |
| **DateTimeZone** | `#datetimezone(2024, 1, 15, 14, 30, 0, -5, 0)` | Date/time with timezone offset |
| **Duration** | `#duration(5, 3, 0, 0)` | 5 days, 3 hours |
| **Null** | `null` | Absence of value |
| **List** | `{1, 2, 3}` | Ordered collection in curly braces |
| **Record** | `[Name = "Alice", Age = 30]` | Set of named fields in brackets |
| **Table** | `#table({"Col1"}, {{"A"}})` | Rows and columns |

> **Exam Tip:** Know the intrinsic type constructors (`#date`, `#time`, `#datetime`, `#datetimezone`, `#duration`). These appear frequently in exam questions about creating calculated date columns or filtering by date ranges.

### 1.2 Let...In Structure

Every Power Query query uses the **let...in** expression. Each step is a named variable, and the final expression after `in` determines the output.

```m
let
    Source = Excel.CurrentWorkbook(){[Name="SalesTable"]}[Content],
    FilteredRows = Table.SelectRows(Source, each [Amount] > 100),
    RenamedColumns = Table.RenameColumns(FilteredRows, {{"Amount", "SalesAmount"}})
in
    RenamedColumns
```

- Each step is separated by a **comma**
- The last step before `in` has **no trailing comma**
- Steps can reference any **previously defined** step
- The `in` clause returns the final result

> **Exam Tip:** The `each` keyword is shorthand for a single-parameter function `(_) => _[FieldName]`. Understanding this is essential for reading and writing row-level filter expressions.

### 1.3 Comments

```m
// This is a single-line comment

/* This is a
   multi-line block comment */

let
    // Step 1: Connect to data
    Source = Sql.Database("server", "db"),
    /* Step 2: Filter to active records only
       This reduces the data volume before further transforms */
    Filtered = Table.SelectRows(Source, each [IsActive] = true)
in
    Filtered
```

### 1.4 Case Sensitivity

M is **case-sensitive** for everything:

- Keywords: `let`, `in`, `if`, `then`, `else`, `true`, `false`, `null` are all **lowercase**
- Column names: `[Sales]` is different from `[sales]`
- Function names: `Table.SelectRows` must match exact casing

```m
// Correct
let result = true in result

// WRONG — will cause an error
let result = True in result
```

> **Exam Tip:** Case sensitivity is a common trap in exam questions. If a query returns an error, check whether column names or keywords have incorrect casing.

### 1.5 Operators

**Arithmetic Operators**

| Operator | Description | Example | Result |
|---|---|---|---|
| `+` | Addition | `3 + 4` | `7` |
| `-` | Subtraction | `10 - 6` | `4` |
| `*` | Multiplication | `5 * 3` | `15` |
| `/` | Division | `20 / 4` | `5` |

**Comparison Operators**

| Operator | Description | Example | Result |
|---|---|---|---|
| `=` | Equal to | `5 = 5` | `true` |
| `<>` | Not equal to | `5 <> 3` | `true` |
| `>` | Greater than | `10 > 5` | `true` |
| `<` | Less than | `3 < 7` | `true` |
| `>=` | Greater than or equal | `5 >= 5` | `true` |
| `<=` | Less than or equal | `3 <= 4` | `true` |

**Logical Operators**

| Operator | Description | Example | Result |
|---|---|---|---|
| `and` | Logical AND | `true and false` | `false` |
| `or` | Logical OR | `true or false` | `true` |
| `not` | Logical NOT | `not true` | `false` |

**Text Concatenation**

```m
// Use the ampersand & to combine text values
"Hello" & " " & "World"   // Result: "Hello World"

// Combine column values
each [FirstName] & " " & [LastName]
```

> **Exam Tip:** M uses `=` for equality comparison (not `==`). The `<>` operator for "not equal" is commonly tested. Also remember that `&` is for text concatenation, not logical AND.

---

## 2. Data Source Functions

### 2.1 File Sources

**File.Contents** — Reads the binary contents of a file from a path.

```m
File.Contents(path as text) as binary
```

```m
let
    Source = File.Contents("C:\Data\report.csv")
in
    Source
```

**Csv.Document** — Parses a CSV document into a table.

```m
Csv.Document(
    source as any,
    optional columns as any,
    optional delimiter as any,
    optional extraValues as nullable number,
    optional encoding as nullable number
) as table
```

```m
let
    Source = File.Contents("C:\Data\sales.csv"),
    Parsed = Csv.Document(Source, [Delimiter=",", Columns=5, Encoding=65001, QuoteStyle=QuoteStyle.None]),
    PromotedHeaders = Table.PromoteHeaders(Parsed, [PromoteAllScalars=true])
in
    PromotedHeaders
```

**Excel.Workbook** — Returns a table of sheets, tables, and named ranges from an Excel workbook.

```m
Excel.Workbook(
    workbook as binary,
    optional useHeaders as any,
    optional delayTypes as nullable logical
) as table
```

```m
let
    Source = File.Contents("C:\Data\report.xlsx"),
    Workbook = Excel.Workbook(Source, null, true),
    SalesSheet = Workbook{[Item="Sales", Kind="Sheet"]}[Data],
    PromotedHeaders = Table.PromoteHeaders(SalesSheet, [PromoteAllScalars=true])
in
    PromotedHeaders
```

**Json.Document** — Parses JSON content.

```m
Json.Document(jsonText as any, optional encoding as nullable number) as any
```

```m
let
    Source = File.Contents("C:\Data\config.json"),
    Parsed = Json.Document(Source),
    ConvertedToTable = Record.ToTable(Parsed)
in
    ConvertedToTable
```

**Xml.Document** — Parses XML content into a table.

```m
Xml.Document(contents as any, optional encoding as nullable number) as table
```

```m
let
    Source = File.Contents("C:\Data\catalog.xml"),
    Parsed = Xml.Document(Source),
    Products = Parsed{0}[Value]
in
    Products
```

> **Exam Tip:** Know which function handles which file type. `File.Contents` only reads raw binary — you always need a second function like `Csv.Document`, `Excel.Workbook`, or `Json.Document` to parse the contents.

### 2.2 Database Sources

**Sql.Database** — Returns a table of SQL tables, views, and stored functions from a SQL Server database.

```m
Sql.Database(
    server as text,
    database as text,
    optional options as nullable record
) as table
```

```m
let
    Source = Sql.Database("myserver.database.windows.net", "AdventureWorks", [
        Query = "SELECT * FROM Sales.SalesOrderHeader WHERE OrderDate >= '2024-01-01'",
        CommandTimeout = #duration(0, 0, 10, 0)
    ])
in
    Source
```

**Sql.Databases** — Lists databases on a SQL Server instance.

```m
let
    Source = Sql.Databases("myserver.database.windows.net"),
    AdventureWorks = Source{[Name="AdventureWorks"]}[Data]
in
    AdventureWorks
```

**Oracle.Database** — Returns a table of Oracle database tables.

```m
Oracle.Database(server as text, optional options as nullable record) as table
```

```m
let
    Source = Oracle.Database("oracle-server:1521/ORCL", [HierarchicalNavigation=true]),
    SchemaHR = Source{[Schema="HR"]}[Data],
    Employees = SchemaHR{[Name="EMPLOYEES"]}[Data]
in
    Employees
```

> **Exam Tip:** When using `Sql.Database` with the `Query` option (native query), **query folding is disabled** for all subsequent steps. Prefer navigating to tables directly when query folding matters.

### 2.3 Web Sources

**Web.Contents** — Downloads content from a URL as binary.

```m
Web.Contents(
    url as text,
    optional options as nullable record
) as binary
```

```m
let
    Source = Web.Contents("https://api.example.com/data", [
        Headers = [#"Content-Type" = "application/json", Authorization = "Bearer token123"],
        Query = [page = "1", pageSize = "100"],
        Timeout = #duration(0, 0, 0, 30),
        RelativePath = "v2/sales"
    ])
in
    Json.Document(Source)
```

**Web.Page** — Extracts tables from an HTML web page.

```m
Web.Page(html as any) as table
```

```m
let
    Source = Web.Page(Web.Contents("https://example.com/data-tables")),
    DataTable = Source{0}[Data]
in
    DataTable
```

> **Exam Tip:** `Web.Contents` returns binary data (use with `Json.Document` or `Csv.Document` to parse). `Web.Page` extracts HTML tables directly. Know the difference for data source questions.

### 2.4 SharePoint Sources

**SharePoint.Files** — Returns a table of documents from a SharePoint site, including subfolders.

```m
SharePoint.Files(url as text, optional options as nullable record) as table
```

```m
let
    Source = SharePoint.Files("https://contoso.sharepoint.com/sites/Finance", [ApiVersion = 15]),
    FilteredFiles = Table.SelectRows(Source, each Text.EndsWith([Name], ".xlsx")),
    FirstFile = FilteredFiles{0}[Content],
    Workbook = Excel.Workbook(FirstFile, true)
in
    Workbook
```

**SharePoint.Contents** — Returns a table of folders and documents from a SharePoint site.

```m
let
    Source = SharePoint.Contents("https://contoso.sharepoint.com/sites/Finance", [ApiVersion = 15]),
    SharedDocuments = Source{[Name="Shared Documents"]}[Content]
in
    SharedDocuments
```

> **Exam Tip:** SharePoint is a very common data source on the PL-300. Know that `SharePoint.Files` returns individual files (useful for combining multiple files), while `SharePoint.Contents` returns the folder navigation structure.

### 2.5 Folder Sources

**Folder.Files** — Returns a table of files in a folder and its subfolders.

```m
Folder.Files(path as text, optional options as nullable record) as table
```

```m
let
    Source = Folder.Files("C:\Data\MonthlySales"),
    FilteredCSV = Table.SelectRows(Source, each [Extension] = ".csv"),
    AddContent = Table.AddColumn(FilteredCSV, "ParsedData", each Csv.Document([Content], [Delimiter=","]))
in
    AddContent
```

**Folder.Contents** — Returns a table of files and folders at the top level of a folder (non-recursive).

```m
let
    Source = Folder.Contents("C:\Data\Reports"),
    OnlyFiles = Table.SelectRows(Source, each [Attributes]?[Kind]? = "File")
in
    OnlyFiles
```

> **Exam Tip:** `Folder.Files` is recursive (includes subfolders), `Folder.Contents` is not. When combining multiple files from a folder, always use `Folder.Files` so new files added later are automatically included on refresh.

---

## 3. Table Functions

Table functions are the **most tested** M code category on the PL-300 exam.

### 3.1 Selection Functions

**Table.SelectRows** — Filters rows based on a condition.

```m
Table.SelectRows(table as table, condition as function) as table
```

```m
let
    Source = SalesTable,
    Filtered = Table.SelectRows(Source, each [Region] = "West" and [Amount] > 500)
in
    Filtered
```

**Table.SelectColumns** — Returns a table with only the specified columns.

```m
Table.SelectColumns(table as table, columns as any, optional missingField as nullable number) as table
```

```m
Table.SelectColumns(Source, {"CustomerName", "OrderDate", "Amount"})
```

**Table.RemoveColumns** — Removes specified columns from a table.

```m
Table.RemoveColumns(table as table, columns as any, optional missingField as nullable number) as table
```

```m
Table.RemoveColumns(Source, {"TempColumn", "InternalID"})
```

**Table.FirstN** — Returns the first N rows.

```m
Table.FirstN(table as table, countOrCondition as any) as table
```

```m
// Get the first 10 rows
Table.FirstN(Source, 10)

// Get rows while condition is true
Table.FirstN(Source, each [Amount] > 0)
```

**Table.LastN** — Returns the last N rows.

```m
Table.LastN(table as table, countOrCondition as any) as table
```

```m
Table.LastN(Source, 5)
```

**Table.Skip** — Skips the first N rows.

```m
Table.Skip(table as table, countOrCondition as any) as table
```

```m
// Skip header rows
Table.Skip(Source, 3)
```

> **Exam Tip:** `Table.SelectRows` is the M code equivalent of filtering rows in the Power Query Editor. The `each` keyword with bracket notation (`[ColumnName]`) is the standard pattern you must recognize.

### 3.2 Transformation Functions

**Table.TransformColumns** — Applies a transformation function to one or more columns.

```m
Table.TransformColumns(
    table as table,
    transformOperations as list,
    optional defaultTransformation as nullable function,
    optional missingField as nullable number
) as table
```

```m
// Round the Amount column to 2 decimal places
Table.TransformColumns(Source, {{"Amount", each Number.Round(_, 2), type number}})

// Trim whitespace from multiple text columns
Table.TransformColumns(Source, {
    {"FirstName", Text.Trim, type text},
    {"LastName", Text.Trim, type text}
})
```

**Table.TransformColumnTypes** — Changes the data type of columns.

```m
Table.TransformColumnTypes(table as table, typeTransformations as list) as table
```

```m
Table.TransformColumnTypes(Source, {
    {"OrderDate", type date},
    {"Amount", type number},
    {"CustomerID", Int64.Type},
    {"IsActive", type logical}
})
```

| Type Keyword | Description |
|---|---|
| `type text` | Text/string |
| `type number` | Decimal number |
| `Int64.Type` | Whole number (integer) |
| `type date` | Date |
| `type datetime` | Date and time |
| `type datetimezone` | Date, time, and timezone |
| `type time` | Time |
| `type logical` | True/False |
| `type duration` | Duration |
| `Currency.Type` | Fixed decimal (currency) |
| `Percentage.Type` | Percentage |

**Table.RenameColumns** — Renames columns in a table.

```m
Table.RenameColumns(table as table, renames as list, optional missingField as nullable number) as table
```

```m
Table.RenameColumns(Source, {
    {"Cust_Name", "Customer Name"},
    {"Ord_Date", "Order Date"},
    {"Amt", "Amount"}
})
```

**Table.ReorderColumns** — Reorders columns in a table.

```m
Table.ReorderColumns(table as table, columnOrder as list, optional missingField as nullable number) as table
```

```m
Table.ReorderColumns(Source, {"CustomerID", "CustomerName", "Region", "OrderDate", "Amount"})
```

> **Exam Tip:** `Table.TransformColumnTypes` is the M code behind the "Change Type" step in the UI. Always set data types as the **last step** in Power Query to ensure query folding is maintained for as many steps as possible.

### 3.3 Combine Functions

**Table.Combine** — Appends multiple tables into one (equivalent to **Append Queries**).

```m
Table.Combine(tables as list, optional columns as any) as table
```

```m
let
    Jan = JanuarySales,
    Feb = FebruarySales,
    Mar = MarchSales,
    Combined = Table.Combine({Jan, Feb, Mar})
in
    Combined
```

**Table.NestedJoin** — Performs a merge and returns the joined table as a nested column (equivalent to **Merge Queries**).

```m
Table.NestedJoin(
    table1 as table,
    key1 as any,
    table2 as any,
    key2 as any,
    newColumnName as text,
    optional joinKind as nullable number
) as table
```

```m
let
    Sales = SalesTable,
    Products = ProductTable,
    Merged = Table.NestedJoin(Sales, {"ProductID"}, Products, {"ProductID"}, "ProductDetails", JoinKind.LeftOuter),
    Expanded = Table.ExpandTableColumn(Merged, "ProductDetails", {"ProductName", "Category"})
in
    Expanded
```

**Table.Join** — Joins two tables directly (flat join, no nesting).

```m
Table.Join(
    table1 as table,
    key1 as any,
    table2 as table,
    key2 as any,
    optional joinKind as nullable number
) as table
```

```m
Table.Join(Sales, "ProductID", Products, "ProductID", JoinKind.Inner)
```

> **Exam Tip:** `Table.NestedJoin` is what the Power Query UI generates when you use Merge Queries. It creates a nested table column that must be expanded with `Table.ExpandTableColumn`. `Table.Join` produces a flat result but is less commonly generated by the UI.

### 3.4 Join Types

| Join Kind | M Code | Description |
|---|---|---|
| **Inner** | `JoinKind.Inner` | Only matching rows from both tables |
| **Left Outer** | `JoinKind.LeftOuter` | All rows from left, matching from right |
| **Right Outer** | `JoinKind.RightOuter` | All rows from right, matching from left |
| **Full Outer** | `JoinKind.FullOuter` | All rows from both tables |
| **Left Anti** | `JoinKind.LeftAnti` | Rows from left with **no match** in right |
| **Right Anti** | `JoinKind.RightAnti` | Rows from right with **no match** in left |

```m
// Find customers who have never placed an order (Left Anti Join)
let
    Customers = CustomerTable,
    Orders = OrderTable,
    NoOrders = Table.NestedJoin(Customers, {"CustomerID"}, Orders, {"CustomerID"}, "Orders", JoinKind.LeftAnti)
in
    NoOrders
```

> **Exam Tip:** Anti joins are heavily tested. **Left Anti** returns rows from the left table that do NOT exist in the right table. This is commonly used to find unmatched records (e.g., customers without orders, products never sold).

### 3.5 Grouping

**Table.Group** — Groups rows and applies aggregate functions.

```m
Table.Group(
    table as table,
    key as any,
    aggregatedColumns as list,
    optional groupKind as nullable number,
    optional comparer as nullable function
) as table
```

```m
// Group by Region, calculate total and average sales
let
    Grouped = Table.Group(Source, {"Region"}, {
        {"TotalSales", each List.Sum([Amount]), type number},
        {"AvgSales", each List.Average([Amount]), type number},
        {"OrderCount", each Table.RowCount(_), Int64.Type},
        {"MinDate", each List.Min([OrderDate]), type date},
        {"AllRows", each _, type table}
    })
in
    Grouped
```

- Use `each List.Sum([ColumnName])` for aggregations
- Use `each Table.RowCount(_)` for counting rows per group
- Use `each _` to keep all rows in a nested table

> **Exam Tip:** `Table.Group` is the M code behind "Group By" in the UI. Know the syntax for common aggregations: `List.Sum`, `List.Average`, `List.Count`, `List.Min`, `List.Max`, and `Table.RowCount`.

### 3.6 Pivot and Unpivot

**Table.Pivot** — Pivots rows into columns (converts long to wide format).

```m
Table.Pivot(
    table as table,
    pivotValues as list,
    attributeColumn as text,
    valueColumn as text,
    optional aggregationFunction as nullable function
) as table
```

```m
// Pivot Month column values into separate columns
let
    Pivoted = Table.Pivot(Source, List.Distinct(Source[Month]), "Month", "Amount", List.Sum)
in
    Pivoted
```

**Table.Unpivot** — Unpivots selected columns into attribute-value pairs (converts wide to long format).

```m
Table.Unpivot(table as table, pivotColumns as list, attributeColumn as text, valueColumn as text) as table
```

```m
// Unpivot month columns into rows
Table.Unpivot(Source, {"Jan", "Feb", "Mar", "Apr"}, "Month", "Amount")
```

**Table.UnpivotOtherColumns** — Unpivots all columns except the specified ones.

```m
Table.UnpivotOtherColumns(table as table, pivotColumns as list, attributeColumn as text, valueColumn as text) as table
```

```m
// Keep Product and Region fixed, unpivot everything else
Table.UnpivotOtherColumns(Source, {"Product", "Region"}, "Attribute", "Value")
```

> **Exam Tip:** `Table.UnpivotOtherColumns` is preferred over `Table.Unpivot` because it automatically handles new columns added in the future. If a new month column appears, `UnpivotOtherColumns` will include it automatically — this is a best practice the exam tests.

### 3.7 Add Columns

**Table.AddColumn** — Adds a new calculated column to a table.

```m
Table.AddColumn(table as table, newColumnName as text, columnGenerator as function, optional columnType as nullable type) as table
```

```m
// Add a profit margin column
Table.AddColumn(Source, "ProfitMargin", each [Revenue] - [Cost], type number)

// Add a full name column
Table.AddColumn(Source, "FullName", each [FirstName] & " " & [LastName], type text)

// Add a conditional column
Table.AddColumn(Source, "SizeCategory", each
    if [Amount] > 10000 then "Large"
    else if [Amount] > 1000 then "Medium"
    else "Small",
    type text
)
```

**Table.DuplicateColumn** — Duplicates an existing column.

```m
Table.DuplicateColumn(table as table, columnName as text, newColumnName as text, optional columnType as nullable type) as table
```

```m
Table.DuplicateColumn(Source, "OrderDate", "OrderDate_Backup")
```

### 3.8 Sort Functions

**Table.Sort** — Sorts rows by one or more columns.

```m
Table.Sort(table as table, comparisonCriteria as any) as table
```

```m
// Sort by single column ascending
Table.Sort(Source, {{"Amount", Order.Ascending}})

// Sort by multiple columns
Table.Sort(Source, {
    {"Region", Order.Ascending},
    {"Amount", Order.Descending}
})
```

| Sort Order | Constant |
|---|---|
| Ascending (A→Z, 0→9) | `Order.Ascending` |
| Descending (Z→A, 9→0) | `Order.Descending` |

> **Exam Tip:** Sorting in Power Query uses `Order.Ascending` and `Order.Descending` constants. Be careful — sorting may **break query folding** depending on where it appears in the step sequence.

### 3.9 Distinct and Remove

**Table.Distinct** — Removes duplicate rows from a table.

```m
Table.Distinct(table as table, optional equationCriteria as any) as table
```

```m
// Remove fully duplicate rows
Table.Distinct(Source)

// Remove duplicates based on specific column(s)
Table.Distinct(Source, {"CustomerID"})
```

**Table.RemoveMatchingRows** — Removes rows that match a specific record pattern.

```m
Table.RemoveMatchingRows(table as table, rows as list, optional equationCriteria as any) as table
```

```m
// Remove rows where Status is "Cancelled"
Table.RemoveMatchingRows(Source, {[Status = "Cancelled"]})
```

---

## 4. Text Functions

### 4.1 Extraction Functions

**Text.Start** — Returns the first N characters.

```m
Text.Start(text as nullable text, count as number) as nullable text
```

```m
Text.Start("Hello World", 5)    // "Hello"
```

**Text.End** — Returns the last N characters.

```m
Text.End(text as nullable text, count as number) as nullable text
```

```m
Text.End("Hello World", 5)     // "World"
```

**Text.Middle** — Returns characters from a given position.

```m
Text.Middle(text as nullable text, start as number, optional count as nullable number) as nullable text
```

```m
Text.Middle("Hello World", 6, 5)    // "World"
Text.Middle("Hello World", 6)       // "World" (to end)
```

**Text.Range** — Returns a substring starting at an offset.

```m
Text.Range(text as nullable text, offset as number, optional count as nullable number) as nullable text
```

```m
Text.Range("ABCDEF", 2, 3)    // "CDE"
```

> **Exam Tip:** `Text.Start` and `Text.End` are the most commonly tested. `Text.Middle` uses a 0-based index. Remember that M text positions start at **0**, not 1.

### 4.2 Information Functions

**Text.Length** — Returns the number of characters.

```m
Text.Length("Power BI")    // 8
```

**Text.Trim / Text.TrimStart / Text.TrimEnd** — Removes whitespace.

```m
Text.Trim("  Hello  ")         // "Hello"
Text.TrimStart("  Hello  ")    // "Hello  "
Text.TrimEnd("  Hello  ")      // "  Hello"
```

### 4.3 Case Functions

```m
Text.Upper("hello world")    // "HELLO WORLD"
Text.Lower("HELLO WORLD")    // "hello world"
Text.Proper("hello world")   // "Hello World"
```

### 4.4 Modification Functions

**Text.Replace** — Replaces occurrences of a substring.

```m
Text.Replace(text as nullable text, old as text, new as text) as nullable text
```

```m
Text.Replace("2024-01-15", "-", "/")    // "2024/01/15"
```

**Text.Remove** — Removes specific characters from text.

```m
Text.Remove(text as nullable text, removeChars as any) as nullable text
```

```m
Text.Remove("A1B2C3", {"1", "2", "3"})    // "ABC"
```

**Text.Split** — Splits text into a list using a delimiter.

```m
Text.Split(text as text, separator as text) as list
```

```m
Text.Split("Red,Green,Blue", ",")    // {"Red", "Green", "Blue"}

// Use with column to extract parts
Table.AddColumn(Source, "Domain", each Text.Split([Email], "@"){1}, type text)
```

### 4.5 Search Functions

**Text.Contains** — Returns true if text contains a substring.

```m
Text.Contains(text as nullable text, substring as text, optional comparer as nullable function) as nullable logical
```

```m
Text.Contains("Power BI Desktop", "BI")    // true

// Case-insensitive search
Text.Contains("Power BI", "power", Comparer.OrdinalIgnoreCase)    // true
```

**Text.StartsWith / Text.EndsWith**

```m
Text.StartsWith("Invoice-001", "Invoice")    // true
Text.EndsWith("report.xlsx", ".xlsx")        // true
```

> **Exam Tip:** By default, `Text.Contains`, `Text.StartsWith`, and `Text.EndsWith` are **case-sensitive**. Use `Comparer.OrdinalIgnoreCase` for case-insensitive comparisons.

### 4.6 Combine and Convert

**Text.Combine** — Joins a list of text values with an optional delimiter.

```m
Text.Combine(texts as list, optional separator as nullable text) as nullable text
```

```m
Text.Combine({"Power", "BI", "Desktop"}, " ")    // "Power BI Desktop"
Text.Combine({"A", "B", "C"}, ", ")               // "A, B, C"
```

**Text.From** — Converts a value to its text representation.

```m
Text.From(value as any, optional culture as nullable text) as nullable text
```

```m
Text.From(42)                    // "42"
Text.From(#date(2024, 1, 15))   // "1/15/2024"
Text.From(3.14)                  // "3.14"
```

---

## 5. Number Functions

### 5.1 Rounding Functions

```m
Number.Round(number as nullable number, optional digits as nullable number, optional roundingMode as nullable number) as nullable number
```

```m
Number.Round(3.456, 2)        // 3.46
Number.RoundUp(3.421, 2)     // 3.43
Number.RoundDown(3.429, 2)   // 3.42

// Rounding modes
Number.Round(2.5, 0, RoundingMode.AwayFromZero)    // 3
Number.Round(2.5, 0, RoundingMode.ToEven)           // 2 (banker's rounding)
```

> **Exam Tip:** By default, `Number.Round` uses **banker's rounding** (round half to even). If a question specifies standard rounding, use `RoundingMode.AwayFromZero`.

### 5.2 Math Functions

```m
Number.Abs(-42)          // 42
Number.Sign(-7)          // -1
Number.Sign(5)           // 1
Number.Sign(0)           // 0
Number.Power(2, 10)      // 1024
Number.Sqrt(144)         // 12
Number.Mod(17, 5)        // 2
Number.IntegerDivide(17, 5)    // 3
```

### 5.3 Conversion Functions

**Number.From** — Converts a value to a number.

```m
Number.From("123.45")     // 123.45
Number.From(true)         // 1
Number.From(false)        // 0
```

**Number.FromText** — Converts text to a number with locale support.

```m
Number.FromText("1,234.56")    // 1234.56 (US locale)
Number.FromText("1.234,56", "de-DE")    // 1234.56 (German locale)
```

> **Exam Tip:** `Number.From` is flexible and converts from many types. For user-facing text conversions where locale matters (e.g., comma vs period as decimal separator), use `Number.FromText` with a culture parameter.

---

## 6. Date/Time Functions

### 6.1 Date Part Extraction

```m
let
    SampleDate = #date(2024, 6, 15)  // June 15, 2024
in
[
    Year = Date.Year(SampleDate),                // 2024
    Month = Date.Month(SampleDate),              // 6
    Day = Date.Day(SampleDate),                  // 15
    DayOfWeek = Date.DayOfWeek(SampleDate),      // 6 (Saturday, 0=Sunday)
    DayOfYear = Date.DayOfYear(SampleDate),      // 167
    WeekOfYear = Date.WeekOfYear(SampleDate),    // 24
    WeekOfMonth = Date.WeekOfMonth(SampleDate),  // 3
    QuarterOfYear = Date.QuarterOfYear(SampleDate)  // 2
]
```

> **Exam Tip:** `Date.DayOfWeek` returns 0 for Sunday by default. You can pass a second parameter to change the first day of the week: `Date.DayOfWeek(date, Day.Monday)` makes Monday = 0.

### 6.2 Date Boundaries

```m
let
    SampleDate = #date(2024, 6, 15)
in
[
    StartOfMonth = Date.StartOfMonth(SampleDate),        // June 1, 2024
    EndOfMonth = Date.EndOfMonth(SampleDate),            // June 30, 2024
    StartOfWeek = Date.StartOfWeek(SampleDate),          // June 9, 2024
    EndOfWeek = Date.EndOfWeek(SampleDate),              // June 15, 2024
    StartOfYear = Date.StartOfYear(SampleDate),          // Jan 1, 2024
    EndOfYear = Date.EndOfYear(SampleDate),              // Dec 31, 2024
    StartOfQuarter = Date.StartOfQuarter(SampleDate),    // Apr 1, 2024
    EndOfQuarter = Date.EndOfQuarter(SampleDate)         // June 30, 2024
]
```

### 6.3 Date Arithmetic

```m
let
    Today = #date(2024, 6, 15)
in
[
    AddDays = Date.AddDays(Today, 30),         // July 15, 2024
    SubtractDays = Date.AddDays(Today, -7),    // June 8, 2024
    AddMonths = Date.AddMonths(Today, 3),      // Sept 15, 2024
    AddYears = Date.AddYears(Today, 1),        // June 15, 2025
    AddQuarters = Date.AddQuarters(Today, 2),  // Dec 15, 2024
    AddWeeks = Date.AddWeeks(Today, 4)         // July 13, 2024
]
```

> **Exam Tip:** Use negative numbers to subtract dates. `Date.AddDays(date, -30)` gives the date 30 days ago. This pattern is used frequently in relative date filtering.

### 6.4 Current Date/Time

```m
// Current local date and time
DateTime.LocalNow()          // e.g., 6/15/2024 2:30:00 PM

// Current date only
DateTime.Date(DateTime.LocalNow())    // e.g., 6/15/2024

// Current UTC date and time with timezone
DateTimeZone.UtcNow()       // e.g., 6/15/2024 6:30:00 PM +00:00

// Convert between types
DateTime.From(#date(2024, 6, 15))    // 6/15/2024 12:00:00 AM
Date.From(DateTime.LocalNow())       // 6/15/2024
```

### 6.5 Duration Functions

```m
let
    SomeDuration = #duration(5, 12, 30, 0)  // 5 days, 12 hours, 30 minutes
in
[
    Days = Duration.Days(SomeDuration),              // 5
    Hours = Duration.Hours(SomeDuration),            // 12
    Minutes = Duration.Minutes(SomeDuration),        // 30
    TotalDays = Duration.TotalDays(SomeDuration),    // 5.520833...
    TotalHours = Duration.TotalHours(SomeDuration),  // 132.5
    TotalMinutes = Duration.TotalMinutes(SomeDuration),  // 7950
    TotalSeconds = Duration.TotalSeconds(SomeDuration)   // 477000
]
```

- `Duration.Days` returns only the **days component** (integer)
- `Duration.TotalDays` returns the **entire duration** expressed in days (decimal)

> **Exam Tip:** The difference between `Duration.Days` and `Duration.TotalDays` is a common exam trap. `Duration.Days(#duration(2, 12, 0, 0))` returns `2`, while `Duration.TotalDays(#duration(2, 12, 0, 0))` returns `2.5`.

---

## 7. List Functions

### 7.1 Generation Functions

**List.Generate** — Generates a list using initial value, condition, and next functions.

```m
List.Generate(
    initial as function,
    condition as function,
    next as function,
    optional selector as nullable function
) as list
```

```m
// Generate numbers 1 to 10
List.Generate(() => 1, each _ <= 10, each _ + 1)
// {1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
```

**List.Dates** — Generates a list of date values.

```m
List.Dates(start as date, count as number, step as duration) as list
```

```m
// Generate all dates in January 2024
List.Dates(#date(2024, 1, 1), 31, #duration(1, 0, 0, 0))
```

**List.Numbers** — Generates a list of numbers.

```m
List.Numbers(from as number, count as number, optional increment as nullable number) as list
```

```m
List.Numbers(0, 10, 5)    // {0, 5, 10, 15, 20, 25, 30, 35, 40, 45}
```

### 7.2 Transformation and Selection

**List.Transform** — Applies a function to each item in a list.

```m
List.Transform(list as list, transform as function) as list
```

```m
List.Transform({1, 2, 3, 4}, each _ * 2)    // {2, 4, 6, 8}
List.Transform({"hello", "world"}, Text.Upper)    // {"HELLO", "WORLD"}
```

**List.Select** — Filters items in a list based on a condition.

```m
List.Select(list as list, selection as function) as list
```

```m
List.Select({1, 2, 3, 4, 5, 6}, each _ > 3)    // {4, 5, 6}
List.Select({"Apple", "Banana", "Avocado"}, each Text.StartsWith(_, "A"))    // {"Apple", "Avocado"}
```

**List.Contains** — Checks if a list contains a specific value.

```m
List.Contains(list as list, value as any, optional equationCriteria as any) as logical
```

```m
List.Contains({1, 2, 3, 4}, 3)    // true
List.Contains({"Red", "Green"}, "Blue")    // false
```

### 7.3 Aggregation Functions

```m
let
    Numbers = {10, 20, 30, 40, 50}
in
[
    Sum = List.Sum(Numbers),           // 150
    Average = List.Average(Numbers),   // 30
    Count = List.Count(Numbers),       // 5
    Min = List.Min(Numbers),           // 10
    Max = List.Max(Numbers),           // 50
    Median = List.Median(Numbers),     // 30
    StandardDeviation = List.StandardDeviation(Numbers)
]
```

> **Exam Tip:** List aggregation functions are used inside `Table.Group` to compute summary statistics. For example, `each List.Sum([Amount])` sums the Amount column within each group.

### 7.4 Set Operations

```m
List.Distinct({1, 1, 2, 2, 3})          // {1, 2, 3}
List.Sort({3, 1, 4, 1, 5})              // {1, 1, 3, 4, 5}
List.Sort({3, 1, 5}, Order.Descending)  // {5, 3, 1}
List.Combine({{1, 2}, {3, 4}})          // {1, 2, 3, 4}
List.Reverse({1, 2, 3})                 // {3, 2, 1}
List.Union({{1, 2, 3}, {2, 3, 4}})      // {1, 2, 3, 4}
List.Intersect({{1, 2, 3}, {2, 3, 4}})  // {2, 3}
List.Difference({1, 2, 3}, {2, 3, 4})   // {1}
```

---

## 8. Common Patterns

### 8.1 Date Dimension Table

Creating a date dimension table is a **very common exam scenario**.

```m
let
    StartDate = #date(2020, 1, 1),
    EndDate = #date(2025, 12, 31),
    DayCount = Duration.Days(EndDate - StartDate) + 1,
    DateList = List.Dates(StartDate, DayCount, #duration(1, 0, 0, 0)),
    ToTable = Table.FromList(DateList, Splitter.SplitByNothing(), {"Date"}, null, ExtraValues.Error),
    ChangedType = Table.TransformColumnTypes(ToTable, {{"Date", type date}}),
    AddYear = Table.AddColumn(ChangedType, "Year", each Date.Year([Date]), Int64.Type),
    AddMonth = Table.AddColumn(AddYear, "Month Number", each Date.Month([Date]), Int64.Type),
    AddMonthName = Table.AddColumn(AddMonth, "Month Name", each Date.ToText([Date], "MMMM"), type text),
    AddQuarter = Table.AddColumn(AddMonthName, "Quarter", each "Q" & Text.From(Date.QuarterOfYear([Date])), type text),
    AddDayOfWeek = Table.AddColumn(AddQuarter, "Day of Week", each Date.DayOfWeekName([Date]), type text),
    AddDayNum = Table.AddColumn(AddDayOfWeek, "Day of Week Number", each Date.DayOfWeek([Date], Day.Monday) + 1, Int64.Type),
    AddWeekNum = Table.AddColumn(AddDayNum, "Week Number", each Date.WeekOfYear([Date]), Int64.Type),
    AddYearMonth = Table.AddColumn(AddWeekNum, "Year-Month", each Text.From(Date.Year([Date])) & "-" & Text.PadStart(Text.From(Date.Month([Date])), 2, "0"), type text),
    AddIsWeekend = Table.AddColumn(AddYearMonth, "Is Weekend", each Date.DayOfWeek([Date], Day.Monday) >= 5, type logical),
    AddFiscalYear = Table.AddColumn(AddIsWeekend, "Fiscal Year", each if Date.Month([Date]) >= 7 then Date.Year([Date]) + 1 else Date.Year([Date]), Int64.Type),
    AddFiscalQuarter = Table.AddColumn(AddFiscalYear, "Fiscal Quarter", each
        let m = Date.Month([Date])
        in if m >= 7 and m <= 9 then "FQ1"
        else if m >= 10 and m <= 12 then "FQ2"
        else if m >= 1 and m <= 3 then "FQ3"
        else "FQ4",
        type text
    )
in
    AddFiscalQuarter
```

> **Exam Tip:** The PL-300 frequently tests your ability to create a date dimension table in Power Query. Know the `List.Dates` + `#date` + `#duration` pattern. Also know that a date table must contain a **contiguous range** of dates (no gaps) and should be **marked as a date table** in the model.

### 8.2 Parameterized Queries

Parameters allow you to make queries dynamic and reusable.

```m
// Define a parameter in Power Query (created via Manage Parameters)
// Parameters are referenced by name in M code

let
    ServerParam = "myserver.database.windows.net",   // Or reference a Query parameter
    DatabaseParam = "AdventureWorks",
    Source = Sql.Database(ServerParam, DatabaseParam),
    Sales = Source{[Schema="Sales", Item="SalesOrderHeader"]}[Data],
    Filtered = Table.SelectRows(Sales, each [OrderDate] >= StartDateParam)
in
    Filtered
```

```m
// Using a parameter for dynamic file path
let
    FolderPath = FolderPathParameter,  // Parameter query
    Source = Folder.Files(FolderPath),
    Filtered = Table.SelectRows(Source, each [Extension] = ".csv")
in
    Filtered
```

> **Exam Tip:** Parameters are essential for making reports portable across environments (dev, test, prod). They can control data sources, filter values, and file paths. Parameters are created via **Home > Manage Parameters** in Power Query Editor.

### 8.3 Error Handling

**try...otherwise** — Catches errors and provides a fallback value.

```m
// Basic error handling
try Number.FromText("abc") otherwise 0    // Returns 0

// Detailed error record
let
    Result = try Number.FromText("abc")
in
    if Result[HasError] then Result[Error][Message] else Result[Value]
```

```m
// Handle errors in a column transformation
Table.AddColumn(Source, "ParsedAmount", each
    try Number.From([RawAmount]) otherwise null,
    type nullable number
)
```

```m
// Replace errors in an entire column
Table.ReplaceErrorValues(Source, {{"Amount", 0}, {"Date", null}})
```

> **Exam Tip:** `try...otherwise` is the M code pattern for error handling. The exam tests this for data cleansing scenarios where some values cannot be converted. Also know `Table.ReplaceErrorValues` for bulk error replacement.

### 8.4 Conditional Columns

**if...then...else** — Creates conditional logic.

```m
// Simple conditional
Table.AddColumn(Source, "Status", each
    if [Amount] > 10000 then "High"
    else if [Amount] > 5000 then "Medium"
    else "Low",
    type text
)
```

```m
// Nested conditions with null handling
Table.AddColumn(Source, "Region Group", each
    if [Region] = null then "Unknown"
    else if List.Contains({"East", "West"}, [Region]) then "Domestic"
    else "International",
    type text
)
```

```m
// Conditional with date logic
Table.AddColumn(Source, "Age Group", each
    let
        age = Duration.TotalDays(DateTime.Date(DateTime.LocalNow()) - [BirthDate]) / 365.25
    in
        if age < 18 then "Minor"
        else if age < 35 then "Young Adult"
        else if age < 55 then "Middle Age"
        else "Senior",
    type text
)
```

### 8.5 Custom Functions

Custom functions allow you to encapsulate reusable logic.

```m
// Define a custom function to clean text columns
let
    CleanText = (inputText as text) as text =>
        let
            Trimmed = Text.Trim(inputText),
            Lowered = Text.Lower(Trimmed),
            Proper = Text.Proper(Lowered)
        in
            Proper
in
    CleanText
```

```m
// Define a function with multiple parameters
let
    CalculateTax = (amount as number, rate as number) as number =>
        Number.Round(amount * rate, 2)
in
    CalculateTax
```

```m
// Invoke a custom function in a table
let
    Source = SalesTable,
    AddTax = Table.AddColumn(Source, "Tax", each CalculateTax([Amount], 0.08), type number)
in
    AddTax
```

> **Exam Tip:** Custom functions are created as separate queries. They appear in the Queries pane and can be invoked by name. The exam may ask you to identify correct function syntax or how to apply a function across table rows.

### 8.6 Combining Files from a Folder

This pattern is essential for loading multiple files with the same structure.

```m
let
    // Step 1: Get all files from a folder
    Source = Folder.Files("C:\Data\MonthlySales"),

    // Step 2: Filter to only CSV files
    FilteredFiles = Table.SelectRows(Source, each [Extension] = ".csv"),

    // Step 3: Parse each file's content
    AddParsed = Table.AddColumn(FilteredFiles, "Tables", each
        let
            FileContent = Csv.Document([Content], [Delimiter=",", Encoding=65001]),
            Promoted = Table.PromoteHeaders(FileContent, [PromoteAllScalars=true])
        in
            Promoted
    ),

    // Step 4: Select only the parsed tables and the file name
    SelectColumns = Table.SelectColumns(AddParsed, {"Name", "Tables"}),

    // Step 5: Expand the nested tables
    ExpandedTables = Table.ExpandTableColumn(SelectColumns, "Tables",
        Table.ColumnNames(SelectColumns{0}[Tables])
    ),

    // Step 6: Set data types
    TypedTable = Table.TransformColumnTypes(ExpandedTables, {
        {"Name", type text},
        {"Date", type date},
        {"Amount", type number}
    })
in
    TypedTable
```

> **Exam Tip:** Combining files from a folder is a heavily tested scenario. The Power Query UI generates a helper function and sample file query automatically when you use "Combine Files." Understand that new files added to the folder are included on the next data refresh.

### 8.7 Dynamic Data Source with Parameters

Use parameters to switch between environments or make data sources flexible.

```m
let
    // Parameters defined as separate queries
    Environment = "Production",  // Could be a parameter query

    ServerName = if Environment = "Production" then "prod-server.database.windows.net"
                 else if Environment = "Staging" then "staging-server.database.windows.net"
                 else "dev-server.database.windows.net",

    Source = Sql.Database(ServerName, "SalesDB"),
    SalesTable = Source{[Schema="dbo", Item="Sales"]}[Data]
in
    SalesTable
```

```m
// Web API with dynamic URL and query parameters
let
    BaseUrl = "https://api.example.com/",
    ApiVersion = "v2",
    Source = Json.Document(Web.Contents(
        BaseUrl,
        [
            RelativePath = ApiVersion & "/sales",
            Query = [
                startDate = Date.ToText(StartDateParam, "yyyy-MM-dd"),
                endDate = Date.ToText(EndDateParam, "yyyy-MM-dd"),
                format = "json"
            ],
            Headers = [Accept = "application/json"]
        ]
    ))
in
    Source
```

> **Exam Tip:** When using `Web.Contents` with dynamic URLs, always use the `RelativePath` and `Query` options rather than concatenating strings into the URL. This enables Power BI to correctly identify the data source for **data source privacy settings** and gateway configuration.

### 8.8 Relative Date Filtering

Filter data relative to the current date for rolling reports.

```m
// Filter to last 30 days
let
    Source = SalesTable,
    Today = Date.From(DateTime.LocalNow()),
    Last30Days = Table.SelectRows(Source, each [OrderDate] >= Date.AddDays(Today, -30))
in
    Last30Days
```

```m
// Filter to current month
let
    Source = SalesTable,
    Today = Date.From(DateTime.LocalNow()),
    StartOfCurrentMonth = Date.StartOfMonth(Today),
    CurrentMonthData = Table.SelectRows(Source, each
        [OrderDate] >= StartOfCurrentMonth and [OrderDate] <= Today
    )
in
    CurrentMonthData
```

```m
// Filter to last N complete months
let
    Source = SalesTable,
    Today = Date.From(DateTime.LocalNow()),
    StartOfCurrentMonth = Date.StartOfMonth(Today),
    NMonthsAgo = Date.AddMonths(StartOfCurrentMonth, -6),
    FilteredData = Table.SelectRows(Source, each
        [OrderDate] >= NMonthsAgo and [OrderDate] < StartOfCurrentMonth
    )
in
    FilteredData
```

> **Exam Tip:** Relative date filtering in Power Query uses `DateTime.LocalNow()` combined with date arithmetic. Be aware that this approach means the filter evaluates at **refresh time** — it does not dynamically change throughout the day in import mode.

---

## 9. Best Practices for PL-300 Exam

### 9.1 Query Folding

**Query folding** is the process where Power Query translates M code steps into native source queries (e.g., SQL). This is the **most important performance concept** for the PL-300.

**Steps that typically fold:**

- `Table.SelectRows` (row filtering)
- `Table.SelectColumns` / `Table.RemoveColumns`
- `Table.Sort`
- `Table.Group`
- `Table.TransformColumnTypes`
- `Table.NestedJoin` (when both tables are from the same source)
- `Table.Distinct`
- `Table.FirstN` / `Table.Skip`

**Steps that typically break folding:**

- `Table.AddColumn` with custom M expressions
- `Table.TransformColumns` with M functions
- `Table.Pivot` / `Table.Unpivot`
- Merges across different data sources
- Using `Sql.Database` with the `Query` option (native SQL)
- Any step using custom M functions

```m
// GOOD: This query folds — filtering happens at the database
let
    Source = Sql.Database("server", "db"),
    Sales = Source{[Schema="Sales", Item="Orders"]}[Data],
    Filtered = Table.SelectRows(Sales, each [Year] = 2024),
    Selected = Table.SelectColumns(Filtered, {"OrderID", "CustomerID", "Amount"})
in
    Selected

// LESS OPTIMAL: Native SQL query disables folding for subsequent steps
let
    Source = Sql.Database("server", "db", [Query="SELECT * FROM Sales.Orders"]),
    Filtered = Table.SelectRows(Source, each [Year] = 2024)  // This filter runs locally
in
    Filtered
```

**How to check query folding:**

- Right-click a step in Power Query Editor
- If "View Native Query" is available and not grayed out, the step is folding
- If it is grayed out, folding has been broken at or before that step

> **Exam Tip:** Query folding is one of the most tested concepts on the PL-300. Know which steps fold and which break folding. Place foldable steps (filters, column selection) **before** non-foldable steps (custom columns, pivots) to maximize performance.

### 9.2 Disabling Query Load

Not every query needs to be loaded into the data model. **Staging queries** (intermediate steps) should have their load disabled.

- Right-click a query → **Enable Load** (uncheck)
- Staging queries are used as building blocks for other queries
- Reduces model size and refresh time

```m
// This staging query should have load disabled
// It's only used as input for other queries
let
    Source = Sql.Database("server", "AdventureWorks"),
    Sales = Source{[Schema="Sales", Item="SalesOrderHeader"]}[Data]
in
    Sales
```

**When to disable load:**

- Helper/staging queries referenced by other queries
- Parameter queries (already not loaded by default)
- Queries used only for intermediate transformation steps

> **Exam Tip:** The exam asks about optimizing data models. Disabling load on staging queries is a best practice that reduces model size. Look for questions about "reducing the number of tables in the model" or "optimizing refresh performance."

### 9.3 Reference vs Duplicate Queries

| Feature | Reference | Duplicate |
|---|---|---|
| **How it works** | Points to the output of another query | Creates an independent copy of the query |
| **Dependency** | Depends on source query (linked) | No dependency (standalone) |
| **Refresh behavior** | Source query must refresh first | Refreshes independently |
| **Changes to source** | Automatically reflected | Not reflected |
| **Use case** | Branch from a common staging query | Create a completely separate copy |

```m
// Original query: SalesRaw (staging, load disabled)
let
    Source = Sql.Database("server", "db"),
    Sales = Source{[Schema="dbo", Item="Sales"]}[Data]
in
    Sales

// Reference query 1: SalesSummary
// Right-click SalesRaw → Reference
let
    Source = SalesRaw,  // References the output of SalesRaw
    Grouped = Table.Group(Source, {"Region"}, {{"Total", each List.Sum([Amount]), type number}})
in
    Grouped

// Reference query 2: SalesDetail
// Right-click SalesRaw → Reference
let
    Source = SalesRaw,  // Also references SalesRaw
    Filtered = Table.SelectRows(Source, each [Amount] > 100)
in
    Filtered
```

> **Exam Tip:** Use **Reference** to create multiple output queries from a single staging query. This avoids duplicating data source connections and ensures query folding chains are maintained. **Duplicate** creates an entirely independent query with no link to the original.

### 9.4 Performance Tips

**1. Filter early, transform late**

```m
// GOOD: Filter first, then transform
let
    Source = Sql.Database("server", "db"),
    Sales = Source{[Schema="dbo", Item="Sales"]}[Data],
    Filtered = Table.SelectRows(Sales, each [Year] = 2024),          // Folds to SQL
    Selected = Table.SelectColumns(Filtered, {"ID", "Amount", "Date"}), // Folds to SQL
    Added = Table.AddColumn(Selected, "Tax", each [Amount] * 0.1)    // Runs locally
in
    Added
```

**2. Remove unnecessary columns early**

- Reduces memory usage and speeds up subsequent steps
- `Table.SelectColumns` (keep only what you need) is better than `Table.RemoveColumns` when you need only a few columns

**3. Use appropriate data types**

| Instead of | Use | Why |
|---|---|---|
| `type text` for dates | `type date` | Enables date intelligence |
| `type number` for IDs | `Int64.Type` | Smaller memory footprint |
| `type number` for money | `Currency.Type` | Precise fixed decimal |
| Loading all columns | Select only needed | Reduces model size |

**4. Use Table.Buffer strategically**

```m
// Buffer a table in memory to avoid re-evaluation
let
    Source = Sql.Database("server", "db"),
    Products = Source{[Schema="dbo", Item="Products"]}[Data],
    Buffered = Table.Buffer(Products)  // Loads fully into memory once
in
    Buffered
```

- Use `Table.Buffer` when a table is referenced multiple times
- Prevents repeated source queries
- Be cautious with large tables — uses memory

**5. Avoid row-by-row operations when possible**

```m
// SLOWER: Row-by-row lookup
Table.AddColumn(Source, "ProductName", each
    Table.SelectRows(Products, (p) => p[ID] = [ProductID]){0}[Name]
)

// FASTER: Use Table.NestedJoin instead
Table.NestedJoin(Source, "ProductID", Products, "ID", "Product", JoinKind.LeftOuter)
```

**6. Incremental refresh considerations**

- Define `RangeStart` and `RangeEnd` parameters (type `datetime`)
- These parameters must filter a date column using `Table.SelectRows`
- The filter step must **fold** to the data source for incremental refresh to work
- Power BI automatically manages these parameters during scheduled refresh

```m
let
    Source = Sql.Database("server", "db"),
    Sales = Source{[Schema="dbo", Item="Sales"]}[Data],
    // These filters enable incremental refresh
    Filtered = Table.SelectRows(Sales, each
        [OrderDate] >= RangeStart and [OrderDate] < RangeEnd
    )
in
    Filtered
```

> **Exam Tip:** Incremental refresh requires `RangeStart` and `RangeEnd` parameters of type `datetime`. The filtering step **must fold** to the data source. The exam frequently tests whether a given query configuration supports incremental refresh.

---

## Quick Reference Card

### Most Common Functions for PL-300

| Category | Function | Purpose |
|---|---|---|
| **Filter Rows** | `Table.SelectRows` | Remove rows based on condition |
| **Select Columns** | `Table.SelectColumns` | Keep only specified columns |
| **Remove Columns** | `Table.RemoveColumns` | Drop specified columns |
| **Change Types** | `Table.TransformColumnTypes` | Set column data types |
| **Rename** | `Table.RenameColumns` | Rename columns |
| **Add Column** | `Table.AddColumn` | Create calculated column |
| **Merge** | `Table.NestedJoin` | Join two tables (Merge Queries) |
| **Append** | `Table.Combine` | Stack tables vertically |
| **Group** | `Table.Group` | Aggregate with grouping |
| **Unpivot** | `Table.UnpivotOtherColumns` | Wide to long format |
| **Replace Errors** | `Table.ReplaceErrorValues` | Handle column errors |
| **Sort** | `Table.Sort` | Order rows |
| **Distinct** | `Table.Distinct` | Remove duplicates |
| **Promote Headers** | `Table.PromoteHeaders` | First row as column names |
| **Expand** | `Table.ExpandTableColumn` | Expand nested table column |

---

> **Exam Tip:** Focus your M code study on reading and understanding existing code rather than writing from scratch. The PL-300 primarily tests your ability to **identify correct M code patterns**, **troubleshoot query errors**, and **select the right function** for a given data transformation scenario.
