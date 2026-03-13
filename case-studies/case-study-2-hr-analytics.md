# Case Study 2: Northwind Enterprises — HR Analytics

> 📋 **Exam Domains Covered:** Prepare the Data | Model the Data | Visualize & Analyze | Deploy & Maintain
> 🕐 **Estimated Study Time:** 90 minutes | **Questions:** 12 scenario-based

---

## Table of Contents

1. [Company Background](#1-company-background)
2. [Data Sources and Description](#2-data-sources-and-description)
3. [Data Model Overview](#3-data-model-overview)
4. [Scenario Questions](#4-scenario-questions)
   - [Q1 – Importing Data from Multiple Sources](#question-1--importing-data-from-multiple-sources)
   - [Q2 – Cleaning Employee Records](#question-2--cleaning-employee-records)
   - [Q3 – Appending and Transforming Attendance Data](#question-3--appending-and-transforming-attendance-data)
   - [Q4 – Building the HR Data Model](#question-4--building-the-hr-data-model)
   - [Q5 – Employee Tenure Calculated Column](#question-5--employee-tenure-calculated-column)
   - [Q6 – Performance Score Measures](#question-6--performance-score-measures)
   - [Q7 – Turnover and Retention Metrics](#question-7--turnover-and-retention-metrics)
   - [Q8 – Creating an HR KPI Dashboard](#question-8--creating-an-hr-kpi-dashboard)
   - [Q9 – Decomposition Tree for Attrition](#question-9--decomposition-tree-for-attrition)
   - [Q10 – Dynamic RLS by Department Manager](#question-10--dynamic-rls-by-department-manager)
   - [Q11 – Sensitivity Labels and Data Protection](#question-11--sensitivity-labels-and-data-protection)
   - [Q12 – Scheduled Refresh and Alerts](#question-12--scheduled-refresh-and-alerts)
5. [Key Exam Concepts Summary](#5-key-exam-concepts-summary)

---

## 1. Company Background

**Northwind Enterprises** is a mid-size professional services company with **520 employees** across **6 departments**: Engineering, Sales, Marketing, Finance, Human Resources, and Operations. The company has offices in **3 cities**: Seattle (headquarters), Chicago, and Austin.

**Key business stakeholders:**

- **Chief Human Resources Officer (CHRO)** — Wants an executive overview of workforce health, diversity, and attrition
- **Department Managers (6)** — Each needs to see performance data for their own department only
- **HR Business Partners (3)** — Each supports 2 departments and needs cross-department visibility
- **Talent Acquisition Team** — Monitors hiring pipeline and time-to-fill metrics
- **Learning & Development (L&D)** — Tracks training completion and skill development

**Business goals for Power BI:**

- Replace quarterly PDF reports with interactive dashboards
- Identify attrition risk factors proactively
- Track training ROI and compliance
- Ensure sensitive employee data is protected with proper access controls

---

## 2. Data Sources and Description

| Source | System | Format | Refresh Frequency | Approximate Size |
|--------|--------|--------|--------------------|------------------|
| **Employee Records** | SQL Server (on-premises HR system) | Relational tables | Weekly | ~520 active + ~200 former |
| **Performance Reviews** | SharePoint Online list | List with metadata | Quarterly | ~1,500 reviews (3 years) |
| **Attendance Records** | Excel workbooks | `.xlsx` per month on network share | Monthly | ~12 files/year, ~15K rows each |
| **Training Records** | Azure SQL Database | Relational tables | Weekly | ~3,000 enrollments |
| **Salary Bands** | CSV file | `.csv` on SharePoint | Annual | ~50 rows |
| **Department Hierarchy** | Excel workbook | `.xlsx` on SharePoint | As needed | ~30 rows |

### Employee Records (DimEmployee)

| Column | Data Type | Description |
|--------|-----------|-------------|
| EmployeeID | Integer | Primary key |
| FullName | Text | Employee full name |
| Email | Text | Corporate email address |
| Department | Text | Department name |
| JobTitle | Text | Current job title |
| JobLevel | Integer | Level 1-8 (1 = entry, 8 = executive) |
| HireDate | Date | Date employee started |
| TerminationDate | Date | Date employee left (null if active) |
| ManagerID | Integer | Self-referencing FK to EmployeeID |
| Office | Text | Seattle, Chicago, or Austin |
| EmploymentStatus | Text | "Active", "Terminated", "On Leave" |
| Gender | Text | For diversity reporting |
| AgeGroup | Text | "Under 25", "25-34", "35-44", "45-54", "55+" |

### Performance Reviews (FactPerformance)

| Column | Data Type | Description |
|--------|-----------|-------------|
| ReviewID | Integer | Primary key |
| EmployeeID | Integer | Foreign key to DimEmployee |
| ReviewDate | Date | Date of review |
| ReviewPeriod | Text | "2023-Q1", "2023-Q2", etc. |
| OverallScore | Decimal | 1.0 to 5.0 scale |
| GoalsMetPct | Decimal | Percentage of goals achieved (0-100) |
| ManagerRating | Integer | 1-5 manager assessment |
| SelfRating | Integer | 1-5 self-assessment |
| ReviewerEmail | Text | Email of the reviewing manager |

### Attendance Records (FactAttendance)

| Column | Data Type | Description |
|--------|-----------|-------------|
| AttendanceDate | Date | Date |
| EmployeeID | Integer | Foreign key to DimEmployee |
| Status | Text | "Present", "Absent", "WFH", "PTO", "Sick" |
| HoursWorked | Decimal | Hours logged |

### Training Records (FactTraining)

| Column | Data Type | Description |
|--------|-----------|-------------|
| EnrollmentID | Integer | Primary key |
| EmployeeID | Integer | Foreign key to DimEmployee |
| CourseName | Text | Name of training course |
| CourseCategory | Text | "Technical", "Leadership", "Compliance", "Soft Skills" |
| EnrollmentDate | Date | Date enrolled |
| CompletionDate | Date | Date completed (null if in progress) |
| Status | Text | "Completed", "In Progress", "Not Started", "Dropped" |
| Score | Decimal | Final assessment score (0-100) |
| IsMandatory | Boolean | Compliance-required courses |

---

## 3. Data Model Overview

```
                        ┌──────────────────┐
                        │     DimDate      │
                        │──────────────────│
                        │ Date (PK)        │
                        │ Year             │
                        │ Quarter          │
                        │ Month            │
                        │ MonthName        │
                        │ FiscalYear       │
                        └────────┬─────────┘
                                 │ 1
              ┌──────────────────┼──────────────────┐
              │                  │                   │
        ┌─────┴──────────┐ ┌────┴──────────┐  ┌────┴──────────────┐
        │FactPerformance │ │FactAttendance │  │  FactTraining     │
        │────────────────│ │───────────────│  │───────────────────│
        │ReviewID (PK)   │ │AttendanceDate │  │EnrollmentID (PK)  │
        │EmployeeID (FK) │ │EmployeeID(FK) │  │EmployeeID (FK)    │
        │ReviewDate      │ │Status         │  │CourseName         │
        │OverallScore    │ │HoursWorked    │  │EnrollmentDate     │
        │GoalsMetPct     │ │               │  │CompletionDate     │
        └─────┬──────────┘ └────┬──────────┘  │Status             │
              │                  │              │Score              │
              │                  │              └────┬──────────────┘
              │                  │                   │
              └──────────────────┼───────────────────┘
                                 │ *
                           ┌─────┴──────────────┐
                           │   DimEmployee      │
                           │────────────────────│
                           │EmployeeID (PK)     │
                           │FullName            │
                           │Email               │
                           │Department          │
                           │JobTitle            │
                           │HireDate            │
                           │TerminationDate     │
                           │ManagerID (FK→self) │
                           │Office              │
                           │EmploymentStatus    │
                           └────────────────────┘
```

**Relationship summary:**

| From (Many) | To (One) | Key Column | Cardinality | Cross-Filter |
|-------------|----------|------------|-------------|--------------|
| FactPerformance | DimEmployee | EmployeeID | Many-to-One | Single |
| FactPerformance | DimDate | ReviewDate → Date | Many-to-One | Single |
| FactAttendance | DimEmployee | EmployeeID | Many-to-One | Single |
| FactAttendance | DimDate | AttendanceDate → Date | Many-to-One | Single |
| FactTraining | DimEmployee | EmployeeID | Many-to-One | Single |
| FactTraining | DimDate | EnrollmentDate → Date | Many-to-One | Single |

> **Exam Tip:** When multiple fact tables share the same dimension (DimEmployee and DimDate), each fact table should have its own relationship to the dimension. Avoid creating fact-to-fact relationships.

---

## 4. Scenario Questions

---

### Question 1 — Importing Data from Multiple Sources

**The HR team needs to combine data from the following sources:**
- **SQL Server (on-premises)** for employee records
- **SharePoint Online list** for performance reviews
- **12 monthly Excel files** on a network share for attendance
- **Azure SQL Database** for training records

**Describe how to configure each data connection and identify any gateway requirements.**

---

#### ✅ Answer

| Source | Connector | Authentication | Gateway |
|--------|-----------|---------------|---------|
| SQL Server (on-premises) | SQL Server connector | Windows authentication | **On-premises data gateway required** |
| SharePoint Online list | SharePoint Online List connector | Organizational account (OAuth) | Not required (cloud-to-cloud) |
| Excel files (network share) | Folder connector → combine files | Windows (network credentials) | **On-premises data gateway required** |
| Azure SQL Database | Azure SQL Database connector | Organizational account or SQL auth | Not required (cloud-to-cloud) |

**For the 12 monthly Excel files, use the Folder connector:**

```m
let
    Source = Folder.Files("\\fileserver\HR\Attendance\2024"),
    FilteredFiles = Table.SelectRows(Source, each Text.EndsWith([Name], ".xlsx")),
    CombinedData = Table.Combine(
        Table.AddColumn(FilteredFiles, "Data", each
            Excel.Workbook([Content]){[Item="Attendance", Kind="Sheet"]}[Data]
        )[Data]
    ),
    PromotedHeaders = Table.PromoteHeaders(CombinedData, [PromoteAllScalars=true])
in
    PromotedHeaders
```

**Alternatively, use the UI approach:**

1. **Get Data** → **Folder** → enter the network path
2. Click **Combine & Transform Data**
3. Select the Excel sheet containing attendance data
4. Power Query auto-generates the combine logic

**Gateway setup:**

- Install the **on-premises data gateway** on a server in the corporate network
- Register both the SQL Server and the file share as data sources in the gateway configuration
- The gateway must remain running for scheduled refresh to work

> **Exam Tip:** The Folder connector is the best approach for combining multiple files with the same structure. It automatically handles new files added to the folder. Both on-premises databases and network file shares require the on-premises data gateway.

---

### Question 2 — Cleaning Employee Records

**While profiling the DimEmployee table, you discover:**
1. **TerminationDate** has null values for active employees (expected) but also has the text `"N/A"` for 12 records
2. **Department** has inconsistent naming: `"Engineering"`, `"Eng."`, `"engineering"`, `"ENGINEERING"`
3. **ManagerID** has `0` for the CEO (no manager) — this should be `null`
4. **Email** column has 3 duplicate values (data entry errors)

**How should you clean this data in Power Query?**

---

#### ✅ Answer

**Step 1: Fix TerminationDate**

```m
// Replace "N/A" text with null before changing column type
= Table.ReplaceValue(PreviousStep, "N/A", null, Replacer.ReplaceValue, {"TerminationDate"})
```

Then change the column type to **Date**.

**Step 2: Standardize Department names**

```m
// Replace all variations with the standard name
= Table.ReplaceValue(
    Table.TransformColumns(PreviousStep, {
        {"Department", each Text.Trim(Text.Proper(_)), type text}
    }),
    "Eng.", "Engineering", Replacer.ReplaceText, {"Department"}
)
```

A more robust approach using a mapping table:

```m
let
    DepartmentMap = #table(
        {"Original", "Standard"},
        {
            {"Eng.", "Engineering"},
            {"engineering", "Engineering"},
            {"ENGINEERING", "Engineering"}
        }
    ),
    Merged = Table.NestedJoin(Employees, {"Department"}, DepartmentMap, {"Original"}, "Map", JoinKind.LeftOuter),
    Expanded = Table.ExpandTableColumn(Merged, "Map", {"Standard"}),
    Replaced = Table.AddColumn(Expanded, "CleanDepartment",
        each if [Standard] <> null then [Standard] else [Department])
in
    Replaced
```

**Step 3: Replace ManagerID 0 with null**

```m
= Table.ReplaceValue(PreviousStep, 0, null, Replacer.ReplaceValue, {"ManagerID"})
```

**Step 4: Handle duplicate emails**

1. Use **Column Profile** to identify the 3 duplicates
2. Investigate the source system to determine correct records
3. Use **Remove Duplicates** only if one copy is clearly the correct one, or merge duplicate rows

```m
// Remove exact duplicate rows (keeping first occurrence)
= Table.Distinct(PreviousStep, {"Email"})
```

> **Exam Tip:** Always fix data type issues after cleaning text values — changing a column type before replacing `"N/A"` with `null` will cause errors. Use **Column Quality** to find error rates and **Column Distribution** to identify duplicates and unique value counts.

---

### Question 3 — Appending and Transforming Attendance Data

**The attendance data arrives as 12 separate Excel files (one per month). Each file has the same structure. After combining them, you need to:**
1. Add a **Month** column derived from the filename (e.g., `"Attendance_2024_01.xlsx"` → January)
2. Standardize the **Status** column to handle `"Work From Home"` and `"WFH"` as the same value
3. Remove rows where **HoursWorked** is null or zero (bad records)

---

#### ✅ Answer

**Step 1: Extract month from filename**

After using the Folder connector to combine files, the `Source.Name` column contains the filename:

```m
// Extract month number from filename pattern "Attendance_2024_MM.xlsx"
= Table.AddColumn(CombinedData, "MonthNumber", each
    Number.From(Text.BetweenDelimiters([Source.Name], "_", ".", 1)),
    Int64.Type
)
```

```m
// Add month name from month number
= Table.AddColumn(PreviousStep, "MonthName", each
    Date.MonthName(#date(2024, [MonthNumber], 1)),
    type text
)
```

**Step 2: Standardize Status values**

```m
= Table.ReplaceValue(PreviousStep, "Work From Home", "WFH", Replacer.ReplaceText, {"Status"})
```

For a more comprehensive mapping:

```m
= Table.TransformColumns(PreviousStep, {
    {"Status", each
        if Text.Contains(Text.Lower(_), "wfh") or Text.Contains(Text.Lower(_), "work from home")
        then "WFH"
        else Text.Proper(Text.Trim(_)),
    type text}
})
```

**Step 3: Remove invalid rows**

```m
= Table.SelectRows(PreviousStep, each
    [HoursWorked] <> null and [HoursWorked] > 0
)
```

**Complete Power Query function for the combined transformation:**

```m
let
    Source = Folder.Files("\\fileserver\HR\Attendance\2024"),
    FilteredXlsx = Table.SelectRows(Source, each Text.EndsWith([Name], ".xlsx")),
    AddData = Table.AddColumn(FilteredXlsx, "SheetData", each
        Excel.Workbook([Content]){[Item="Attendance", Kind="Sheet"]}[Data]),
    ExpandedData = Table.ExpandTableColumn(AddData, "SheetData",
        {"AttendanceDate", "EmployeeID", "Status", "HoursWorked"}),
    PromotedTypes = Table.TransformColumnTypes(ExpandedData, {
        {"AttendanceDate", type date},
        {"EmployeeID", Int64.Type},
        {"HoursWorked", type number}
    }),
    StandardizedStatus = Table.ReplaceValue(PromotedTypes,
        "Work From Home", "WFH", Replacer.ReplaceText, {"Status"}),
    RemovedInvalid = Table.SelectRows(StandardizedStatus,
        each [HoursWorked] <> null and [HoursWorked] > 0)
in
    RemovedInvalid
```

> **Exam Tip:** When combining files with the Folder connector, the `Source.Name` column is preserved and can be used to extract metadata like dates or categories from filenames. Always apply type transformations and filters as early as possible in the query for performance.

---

### Question 4 — Building the HR Data Model

**After loading all tables, you need to configure the data model. The DimEmployee table has a self-referencing relationship (ManagerID → EmployeeID) for the org hierarchy. Additionally, FactTraining has two date columns: EnrollmentDate and CompletionDate.**

**How should you handle these modeling challenges?**

---

#### ✅ Answer

**Challenge 1: Self-referencing relationship (Parent-Child hierarchy)**

Power BI does not natively display parent-child hierarchies from a self-join. Use the `PATH` DAX functions:

```dax
OrgPath = PATH(DimEmployee[EmployeeID], DimEmployee[ManagerID])
```

```dax
OrgLevel = PATHLENGTH(DimEmployee[OrgPath])
```

```dax
Level1Manager = LOOKUPVALUE(
    DimEmployee[FullName],
    DimEmployee[EmployeeID],
    VALUE(PATHITEM(DimEmployee[OrgPath], 1))
)
```

```dax
Level2Manager = LOOKUPVALUE(
    DimEmployee[FullName],
    DimEmployee[EmployeeID],
    VALUE(PATHITEM(DimEmployee[OrgPath], 2))
)
```

These flattened columns can then be used to create a **visual hierarchy** in the Fields pane: `Level1Manager > Level2Manager > FullName`.

**Challenge 2: Two date columns in FactTraining**

A table can only have one **active** relationship to DimDate. Handle this with:

1. Create an **active** relationship: `FactTraining[EnrollmentDate]` → `DimDate[Date]`
2. Create an **inactive** relationship: `FactTraining[CompletionDate]` → `DimDate[Date]`
3. Use `USERELATIONSHIP` in DAX to activate the inactive relationship when needed:

```dax
Training Completions =
CALCULATE(
    COUNTROWS(FactTraining),
    FactTraining[Status] = "Completed",
    USERELATIONSHIP(FactTraining[CompletionDate], DimDate[Date])
)
```

**Alternative approach — Role-playing dimensions:**

Create two references to DimDate in Power Query:

```m
DimDate_Enrollment = DimDate    // Reference (not duplicate)
DimDate_Completion = DimDate    // Reference (not duplicate)
```

Then create separate active relationships from each fact date column to each date reference.

> **Exam Tip:** `PATH()`, `PATHITEM()`, and `PATHLENGTH()` are key functions for handling parent-child hierarchies. When a fact table has multiple date columns, use one active relationship and `USERELATIONSHIP()` in measures for the others. Only one active relationship is allowed between two tables.

---

### Question 5 — Employee Tenure Calculated Column

**HR wants to display each employee's tenure in years. For active employees, calculate tenure from HireDate to today. For terminated employees, calculate from HireDate to TerminationDate.**

**Should this be a measure or a calculated column? Write the DAX.**

---

#### ✅ Answer

This should be a **calculated column** because tenure is an attribute of each employee row, not an aggregation. It needs to be available for filtering and slicing.

```dax
TenureYears =
VAR EndDate =
    IF(
        ISBLANK(DimEmployee[TerminationDate]),
        TODAY(),
        DimEmployee[TerminationDate]
    )
RETURN
    DATEDIFF(DimEmployee[HireDate], EndDate, YEAR)
```

**For a more precise decimal value:**

```dax
TenurePrecise =
VAR EndDate =
    IF(
        ISBLANK(DimEmployee[TerminationDate]),
        TODAY(),
        DimEmployee[TerminationDate]
    )
VAR DaysBetween = DATEDIFF(DimEmployee[HireDate], EndDate, DAY)
RETURN
    ROUND(DaysBetween / 365.25, 1)
```

**Tenure band for grouping:**

```dax
TenureBand =
SWITCH(
    TRUE(),
    DimEmployee[TenureYears] < 1, "< 1 Year",
    DimEmployee[TenureYears] < 3, "1-3 Years",
    DimEmployee[TenureYears] < 5, "3-5 Years",
    DimEmployee[TenureYears] < 10, "5-10 Years",
    "10+ Years"
)
```

**When to use calculated columns vs. measures:**

| Use Case | Choice | Reason |
|----------|--------|--------|
| Row-level attribute (tenure, age band) | **Calculated column** | Stored in the model; available for slicing |
| Aggregation (total, average, count) | **Measure** | Computed at query time; respects filter context |
| Needed in a slicer or filter | **Calculated column** | Measures cannot be used in slicers |
| Needs to react to filters dynamically | **Measure** | Recalculates based on current filter context |

> **Exam Tip:** Use calculated columns for row-level computations that you want to filter or slice by. Use measures for aggregations. Calculated columns increase model size; measures are computed on demand. The exam frequently tests whether you can identify the correct choice.

---

### Question 6 — Performance Score Measures

**The CHRO wants to see the following performance metrics on the dashboard:**
1. **Average Performance Score** across all reviewed employees
2. **High Performer Count** — employees with OverallScore ≥ 4.0
3. **Performance Score Distribution** — count of employees in each score bracket
4. **Goals Achievement Rate** — average GoalsMetPct for a selected time period

**Write DAX measures for each.**

---

#### ✅ Answer

```dax
Avg Performance Score =
AVERAGE(FactPerformance[OverallScore])
```

```dax
High Performer Count =
CALCULATE(
    DISTINCTCOUNT(FactPerformance[EmployeeID]),
    FactPerformance[OverallScore] >= 4.0
)
```

```dax
Total Reviewed Employees =
DISTINCTCOUNT(FactPerformance[EmployeeID])
```

```dax
High Performer % =
DIVIDE(
    [High Performer Count],
    [Total Reviewed Employees],
    0
)
```

**Performance bracket measure using `COUNTROWS` + `FILTER`:**

```dax
Needs Improvement Count =
CALCULATE(
    DISTINCTCOUNT(FactPerformance[EmployeeID]),
    FactPerformance[OverallScore] < 2.0
)
```

```dax
Meets Expectations Count =
CALCULATE(
    DISTINCTCOUNT(FactPerformance[EmployeeID]),
    FactPerformance[OverallScore] >= 2.0,
    FactPerformance[OverallScore] < 4.0
)
```

```dax
Exceeds Expectations Count =
CALCULATE(
    DISTINCTCOUNT(FactPerformance[EmployeeID]),
    FactPerformance[OverallScore] >= 4.0
)
```

```dax
Goals Achievement Rate =
AVERAGE(FactPerformance[GoalsMetPct])
```

**For weighted average when reviews have different periods:**

```dax
Weighted Avg Performance =
DIVIDE(
    SUMX(FactPerformance, FactPerformance[OverallScore] * 1),
    COUNTROWS(FactPerformance),
    BLANK()
)
```

> **Exam Tip:** `CALCULATE` changes filter context — `CALCULATE(expression, filter1, filter2)` applies filters before evaluating the expression. Multiple filter arguments in CALCULATE are combined with AND logic. Use `DISTINCTCOUNT` when counting unique entities (employees) vs. `COUNTROWS` for counting rows (reviews).

---

### Question 7 — Turnover and Retention Metrics

**HR leadership needs to track employee attrition. Define and create measures for:**
1. **Headcount** — number of active employees at any point in time
2. **Attrition Count** — employees who left during a period
3. **Attrition Rate** — percentage of employees who left
4. **New Hire Count** — employees hired during a period

---

#### ✅ Answer

```dax
Headcount =
CALCULATE(
    COUNTROWS(DimEmployee),
    DimEmployee[EmploymentStatus] = "Active"
)
```

**For a point-in-time headcount (as of a selected date):**

```dax
Headcount as of Date =
VAR SelectedDate = MAX(DimDate[Date])
RETURN
CALCULATE(
    COUNTROWS(DimEmployee),
    DimEmployee[HireDate] <= SelectedDate,
    OR(
        ISBLANK(DimEmployee[TerminationDate]),
        DimEmployee[TerminationDate] > SelectedDate
    )
)
```

```dax
Attrition Count =
CALCULATE(
    COUNTROWS(DimEmployee),
    DimEmployee[EmploymentStatus] = "Terminated",
    USERELATIONSHIP(DimEmployee[TerminationDate], DimDate[Date])
)
```

> **Note:** The above requires an inactive relationship between `DimEmployee[TerminationDate]` and `DimDate[Date]`.

**Alternative without `USERELATIONSHIP`:**

```dax
Attrition Count (Alt) =
VAR MinDate = MIN(DimDate[Date])
VAR MaxDate = MAX(DimDate[Date])
RETURN
COUNTROWS(
    FILTER(
        ALL(DimEmployee),
        DimEmployee[TerminationDate] >= MinDate &&
        DimEmployee[TerminationDate] <= MaxDate
    )
)
```

```dax
Attrition Rate =
DIVIDE(
    [Attrition Count],
    [Headcount as of Date],
    0
)
```

```dax
New Hire Count =
CALCULATE(
    COUNTROWS(DimEmployee),
    USERELATIONSHIP(DimEmployee[HireDate], DimDate[Date])
)
```

**Alternative for new hires without `USERELATIONSHIP`:**

```dax
New Hire Count (Alt) =
VAR MinDate = MIN(DimDate[Date])
VAR MaxDate = MAX(DimDate[Date])
RETURN
COUNTROWS(
    FILTER(
        ALL(DimEmployee),
        DimEmployee[HireDate] >= MinDate &&
        DimEmployee[HireDate] <= MaxDate
    )
)
```

> **Exam Tip:** HR metrics often require point-in-time calculations. Use `FILTER` with `ALL` to bypass the current filter context when you need to calculate across all employees regardless of other filters. Understand the difference between `ALL` (removes all filters) and `ALLEXCEPT` (removes all filters except specified columns).

---

### Question 8 — Creating an HR KPI Dashboard

**The CHRO wants a single-page executive dashboard displaying:**
1. Headcount, attrition rate, and average performance score as KPIs
2. Headcount trend by month (last 12 months)
3. Attrition breakdown by department
4. Performance score distribution (histogram)
5. Training completion rate by department
6. Diversity breakdown (gender distribution)

**Recommend the visual layout and visual types.**

---

#### ✅ Answer

| Requirement | Visual Type | Configuration |
|-------------|------------|---------------|
| KPI metrics | **KPI visual** or **Card** | Show headcount, attrition %, avg performance with trend indicators |
| Headcount trend | **Area chart** | X: Month, Y: Headcount as of Date; shows workforce growth/decline |
| Attrition by department | **Clustered bar chart** | Y: Department, X: Attrition Count; sort descending |
| Performance distribution | **Clustered column chart** | X: Score bracket (1-2, 2-3, 3-4, 4-5), Y: Count |
| Training completion | **Stacked bar chart** | Y: Department, X: Count; Legend: Completion Status |
| Diversity breakdown | **Donut chart** | Values: Count, Legend: Gender |

**Dashboard layout:**

```
┌─────────────────────────────────────────────────────────────┐
│  [Card: Headcount]  [Card: Attrition %]  [Card: Avg Perf]  │
│  [Card: New Hires]  [Card: Training %]   [Card: Avg Tenure]│
├────────────────────────────┬────────────────────────────────┤
│                            │                                │
│   Area Chart:              │   Bar Chart:                   │
│   Headcount Trend          │   Attrition by Department      │
│                            │                                │
├────────────────────────────┼────────────────────────────────┤
│                            │                                │
│   Column Chart:            │   Donut: Gender   Stacked Bar: │
│   Performance Distribution │   Distribution    Training     │
│                            │                                │
└────────────────────────────┴────────────────────────────────┘
```

**Interactivity features:**

- Add **slicers** for Year, Department, and Office at the top of the page
- Enable **cross-filtering** between visuals (default behavior)
- Configure specific visuals to **cross-highlight** instead of cross-filter where appropriate
- Add **bookmarks** for preset views (e.g., "Engineering Focus", "2024 Overview")

**Creating the Training Completion Rate measure:**

```dax
Training Completion Rate =
DIVIDE(
    CALCULATE(
        COUNTROWS(FactTraining),
        FactTraining[Status] = "Completed"
    ),
    COUNTROWS(FactTraining),
    0
)
```

> **Exam Tip:** KPI visuals require a measure, a target, and a trend axis. For executive dashboards, place the most important KPIs at the top. Use consistent formatting and color-coding (green = good, red = needs attention). Bookmarks allow you to save filter states and toggle visual visibility.

---

### Question 9 — Decomposition Tree for Attrition

**The CHRO wants to understand what factors contribute to employee attrition. They want an interactive visual that automatically identifies the highest-impact dimensions (department, office, tenure band, job level).**

**Which visual should you use and how do you configure it?**

---

#### ✅ Answer

Use the **Decomposition Tree** visual (built-in AI visual in Power BI).

**Configuration:**

1. Insert a **Decomposition Tree** visual from the Visualizations pane
2. Set **Analyze** to: `[Attrition Count]` measure
3. Add the following fields to **Explain By**:
   - `DimEmployee[Department]`
   - `DimEmployee[Office]`
   - `DimEmployee[TenureBand]` (calculated column)
   - `DimEmployee[JobLevel]`
   - `DimEmployee[AgeGroup]`

**How it works:**

- Users click the **"+"** button at each node to split by a dimension
- The **AI splits** option (light bulb icon) automatically identifies which dimension explains the most variance at each level
- **High value** finds the dimension value with the highest attrition
- **Low value** finds the dimension value with the lowest attrition

**Example drill path:**

```
Attrition Count: 45
  └── Department: Engineering (18)
        └── Tenure Band: < 1 Year (12)
              └── Job Level: 1 (9)
```

This reveals that most attrition comes from entry-level Engineering hires in their first year.

**Alternative: Key Influencers visual**

The **Key Influencers** visual can also analyze attrition:

1. Set **Analyze** to: `DimEmployee[EmploymentStatus]`
2. Set **What influences** to: `"Terminated"`
3. Add dimensions to **Explain by**
4. The visual shows which factors increase the likelihood of termination

> **Exam Tip:** The Decomposition Tree lets users interactively drill into data. The AI feature uses statistical analysis to suggest the most impactful splits. Key Influencers identifies which factors most influence an outcome. Both are important for the "Visualize and Analyze" domain.

---

### Question 10 — Dynamic RLS by Department Manager

**Each department manager should only see HR data for employees in their department. HR Business Partners (HRBPs) each support 2 departments and should see data for both. The CHRO should see all data.**

**Design a dynamic RLS solution.**

---

#### ✅ Answer

**Step 1: Create a security mapping table**

Create a table called **SecurityAccess** that maps user emails to departments:

| UserEmail | Department |
|-----------|-----------|
| eng_mgr@northwind.com | Engineering |
| sales_mgr@northwind.com | Sales |
| mktg_mgr@northwind.com | Marketing |
| fin_mgr@northwind.com | Finance |
| hr_mgr@northwind.com | Human Resources |
| ops_mgr@northwind.com | Operations |
| hrbp_seattle@northwind.com | Engineering |
| hrbp_seattle@northwind.com | Sales |
| hrbp_chicago@northwind.com | Marketing |
| hrbp_chicago@northwind.com | Finance |
| hrbp_austin@northwind.com | Human Resources |
| hrbp_austin@northwind.com | Operations |

> **Note:** HRBPs have two rows — one per supported department.

**Step 2: Create relationships**

- `SecurityAccess[Department]` → `DimEmployee[Department]` (many-to-many)
- Set cross-filter direction to **Both** (bidirectional) for this relationship
- Alternatively, use a DimDepartment bridge table to avoid many-to-many

**Step 3: Define the RLS role**

1. **Modeling** → **Manage Roles** → create role **"Department Access"**
2. Apply a DAX filter on the **SecurityAccess** table:

```dax
[UserEmail] = USERPRINCIPALNAME()
```

**How it works:**

- When `eng_mgr@northwind.com` logs in, the filter matches their row in SecurityAccess
- The Department value `"Engineering"` propagates through the relationship to DimEmployee
- DimEmployee filters all fact tables (Performance, Attendance, Training)
- The HRBP with two rows sees data for both departments

**Step 4: Exclude the CHRO from RLS**

Do NOT add the CHRO to the RLS role. Users not assigned to any role see all data by default (workspace Members, Admins, Contributors).

Alternatively, give the CHRO the **Admin** or **Member** workspace role — these roles bypass RLS.

**Testing:**

1. In Power BI Desktop: **Modeling** → **View as** → select "Department Access" → enter `eng_mgr@northwind.com`
2. Verify only Engineering data is visible
3. Test with HRBP email to confirm two departments appear

> **Exam Tip:** Dynamic RLS uses a security table with user-to-filter mappings and `USERPRINCIPALNAME()`. Many-to-many relationships with bidirectional cross-filtering are sometimes needed for security tables. Workspace Admins and Members bypass RLS — use this for users who need full access.

---

### Question 11 — Sensitivity Labels and Data Protection

**HR data is classified as confidential. The organization requires:**
1. Sensitivity labels on the Power BI dataset and report
2. Prevention of data export for sensitive fields (salary, performance scores)
3. An audit trail of who accesses the report

**How should you configure data protection?**

---

#### ✅ Answer

**Sensitivity labels:**

1. The Power BI admin must enable **sensitivity labels** in the Power BI admin portal
2. Sensitivity labels are defined in the **Microsoft Purview compliance portal** (formerly Microsoft 365 compliance)
3. Apply the **"Confidential"** label to:
   - The Power BI **dataset** in the workspace
   - The Power BI **report** in the workspace
4. Labels can be set manually or inherited automatically from the data source

**Configuring label behavior:**

| Setting | Configuration |
|---------|--------------|
| Label inheritance | Enable "Automatically apply sensitivity labels to downstream content" |
| Export restrictions | Configure the label policy to block export to Excel/CSV/PowerPoint |
| Encryption | Enable encryption on the "Confidential" label to restrict access |
| Mandatory labeling | Require content creators to apply a label before publishing |

**Preventing data export:**

- In the Power BI **admin portal** → **Tenant settings**:
  - Disable **"Export to Excel"** for specific security groups
  - Disable **"Export to CSV"** for specific security groups
  - Disable **"Print dashboards and reports"** if needed
- Alternatively, the sensitivity label policy can enforce export restrictions via Microsoft Information Protection

**Audit trail:**

- **Microsoft 365 unified audit log** captures Power BI access events
- Enable auditing in the Power BI admin portal under **Audit and usage settings**
- Key events tracked: report views, data exports, dataset refreshes, sharing actions
- Access audit logs via the **Microsoft Purview compliance portal** or PowerShell

> **Exam Tip:** Sensitivity labels in Power BI are part of Microsoft Information Protection (MIP). They are configured in Purview, not in Power BI itself. Labels flow downstream — a labeled dataset automatically labels reports built on it. Tenant admin settings control export capabilities at the organizational level.

---

### Question 12 — Scheduled Refresh and Alerts

**The HR dashboard needs to refresh weekly (every Monday at 6:00 AM). The CHRO wants an email alert if the attrition rate exceeds 15% in any department.**

**How do you configure scheduled refresh and alerts?**

---

#### ✅ Answer

**Configuring scheduled refresh:**

1. Publish the report to the Power BI service
2. Navigate to the **dataset settings** in the workspace
3. Under **Scheduled refresh**:
   - Toggle refresh **On**
   - Set frequency to **Weekly**
   - Select **Monday**
   - Set time to **6:00 AM** (in the configured time zone)
   - Add failure notification email addresses
4. Verify gateway credentials are valid (for on-premises sources)

**Gateway refresh configuration:**

| Setting | Value |
|---------|-------|
| Gateway cluster | Northwind On-Premises Gateway |
| SQL Server credentials | Windows authentication (stored) |
| File share credentials | Windows authentication (stored) |
| Privacy level | Organizational |

**Configuring data-driven alerts:**

1. **Pin** the attrition rate card or KPI visual to a **dashboard** (alerts work on dashboards, not reports)
2. Hover over the pinned tile → click **"..."** → **Manage alerts**
3. Configure the alert:

| Alert Setting | Value |
|---------------|-------|
| Alert title | "High Attrition Rate Warning" |
| Condition | Above |
| Threshold | 0.15 (15%) |
| Frequency | At most every 24 hours |
| Send email notification | Yes |

**Limitations of built-in alerts:**

- Alerts only work on **dashboard tiles** (cards, KPIs, gauges) — not on report visuals directly
- Alerts check on **data refresh** — they do not monitor in real-time
- Only the alert creator receives notifications (cannot share alerts)

**Alternative: Power Automate integration**

For more sophisticated alerting (e.g., per-department threshold):

1. Create a Power Automate flow triggered by **"When a data-driven alert is triggered"**
2. Add actions to send emails to specific department managers
3. Include dynamic content from the alert (current value, threshold, timestamp)

> **Exam Tip:** Data-driven alerts only work on dashboard tiles — pin a card, gauge, or KPI from a report to a dashboard first. Scheduled refresh requires valid credentials and gateway configuration. Alerts check thresholds when data refreshes, not continuously. Power Automate extends alerting capabilities beyond built-in options.

---

## 5. Key Exam Concepts Summary

| Concept | Domain | Key Takeaway |
|---------|--------|-------------|
| **Folder connector** | Prepare Data | Best for combining multiple files with the same structure |
| **Data profiling** | Prepare Data | Column Quality, Distribution, and Profile identify issues; set to full dataset |
| **Gateway requirements** | Prepare Data | On-premises SQL and file shares need a gateway; cloud sources do not |
| **Parent-child hierarchies** | Model Data | Use `PATH()`, `PATHITEM()`, `PATHLENGTH()` to flatten org hierarchies |
| **Active vs. inactive relationships** | Model Data | Only one active relationship per table pair; use `USERELATIONSHIP()` |
| **Calculated column vs. measure** | Model Data | Columns for row-level attributes and slicers; measures for aggregations |
| **CALCULATE filter context** | Model Data | Modifies filter context; multiple filters combine with AND logic |
| **DISTINCTCOUNT vs. COUNTROWS** | Model Data | DISTINCTCOUNT for unique entities; COUNTROWS for all rows |
| **Decomposition Tree** | Visualize | AI-powered drill-down that identifies highest-impact splits |
| **Key Influencers** | Visualize | Identifies factors influencing an outcome |
| **Dynamic RLS** | Deploy & Maintain | Security table + `USERPRINCIPALNAME()` for flexible role assignments |
| **Sensitivity labels** | Deploy & Maintain | Configured in Microsoft Purview; flow downstream to reports |
| **Data-driven alerts** | Deploy & Maintain | Dashboard tiles only; triggered on data refresh |
| **Scheduled refresh** | Deploy & Maintain | Requires gateway credentials; supports daily or weekly schedules |
