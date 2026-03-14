# Case Study 2: Northwind Enterprises — HR Analytics Visualization & Dashboard Guide

> 📊 **Focus Area:** Visualize and Analyze the Data | Dashboard Design | Step-by-Step Instructions
> 🕐 **Estimated Study Time:** 120 minutes | **Questions:** 15 visualization-focused
> 📋 **Companion to:** [Case Study 2 – HR Analytics](case-study-2-hr-analytics.md)

---

## Table of Contents

1. [Dashboard Overview](#1-dashboard-overview)
2. [Visualization Questions](#2-visualization-questions)
   - [Q1 – Workforce KPI Cards](#question-1--workforce-kpi-cards)
   - [Q2 – Headcount Trend Area Chart](#question-2--headcount-trend-area-chart)
   - [Q3 – Attrition by Department Bar Chart](#question-3--attrition-by-department-bar-chart)
   - [Q4 – Performance Distribution Histogram](#question-4--performance-distribution-histogram)
   - [Q5 – Diversity Donut Charts](#question-5--diversity-donut-charts)
   - [Q6 – Employee Tenure Heatmap Matrix](#question-6--employee-tenure-heatmap-matrix)
   - [Q7 – Training Completion Stacked Bar](#question-7--training-completion-stacked-bar)
   - [Q8 – Decomposition Tree for Attrition Drivers](#question-8--decomposition-tree-for-attrition-drivers)
   - [Q9 – Key Influencers for Performance Ratings](#question-9--key-influencers-for-performance-ratings)
   - [Q10 – Org Chart with Parent-Child Hierarchy](#question-10--org-chart-with-parent-child-hierarchy)
   - [Q11 – Drill-Through Employee Detail Page](#question-11--drill-through-employee-detail-page)
   - [Q12 – Bookmarks for Department Views](#question-12--bookmarks-for-department-views)
   - [Q13 – Smart Narrative Visual](#question-13--smart-narrative-visual)
   - [Q14 – Conditional Formatting on HR Metrics Table](#question-14--conditional-formatting-on-hr-metrics-table)
   - [Q15 – Dynamic Title and Subtitle Measures](#question-15--dynamic-title-and-subtitle-measures)
3. [Complete Business Dashboard Design](#3-complete-business-dashboard-design)
4. [DAX Measures Reference](#4-dax-measures-reference)
5. [Key Exam Concepts Summary](#5-key-exam-concepts-summary)

---

## 1. Dashboard Overview

The Northwind Enterprises HR Analytics dashboard enables the CHRO, Department Managers, HR Business Partners, and the L&D team to monitor workforce health, track attrition, analyze performance, and measure training effectiveness across 520 employees in 6 departments.

**Dashboard pages:**

```
┌─────────────────────────────────────────────────────────────┐
│  📄 Page 1: Executive HR Overview                           │
│  KPI cards, headcount trend, attrition, diversity           │
├─────────────────────────────────────────────────────────────┤
│  📄 Page 2: Performance & Training                          │
│  Performance distribution, training completion, trends      │
├─────────────────────────────────────────────────────────────┤
│  📄 Page 3: Attrition Deep Dive                             │
│  Decomposition tree, key influencers, tenure analysis       │
└─────────────────────────────────────────────────────────────┘
```

**Target audience and access:**

| Stakeholder | Pages Used | Access Level |
|-------------|-----------|--------------|
| CHRO | All 3 pages | Full access (bypasses RLS as Admin) |
| Department Managers (6) | All pages (filtered to own dept via dynamic RLS) | Department-only data |
| HR Business Partners (3) | All pages (filtered to 2 supported depts) | Multi-department data |
| L&D Team | Page 2 (training focus) | Full access to training data |

---

## 2. Visualization Questions

### Question 1 – Workforce KPI Cards

**Scenario:** The CHRO wants six headline KPI cards at the top of the Executive HR Overview: Active Headcount, Attrition Rate, Average Tenure (years), Average Performance Score, New Hires (current year), and Training Completion Rate.

**Task:** Create six KPI cards with dynamic subtitles showing the trend vs. prior year.

#### ✅ Step-by-Step Solution

**Step 1: Create the DAX measures**

```dax
Active Headcount =
CALCULATE(
    COUNTROWS(DimEmployee),
    DimEmployee[EmploymentStatus] = "Active"
)
```

```dax
Attrition Count =
CALCULATE(
    COUNTROWS(DimEmployee),
    DimEmployee[EmploymentStatus] = "Terminated"
)
```

```dax
Attrition Rate =
DIVIDE(
    [Attrition Count],
    [Attrition Count] + [Active Headcount],
    0
)
```

```dax
Avg Tenure Years =
AVERAGEX(
    FILTER(DimEmployee, DimEmployee[EmploymentStatus] = "Active"),
    DATEDIFF(DimEmployee[HireDate], TODAY(), YEAR)
)
```

```dax
Avg Performance Score =
AVERAGE(FactPerformance[OverallScore])
```

```dax
New Hires Current Year =
CALCULATE(
    COUNTROWS(DimEmployee),
    YEAR(DimEmployee[HireDate]) = YEAR(TODAY())
)
```

```dax
Training Completion Rate =
DIVIDE(
    CALCULATE(COUNTROWS(FactTraining), FactTraining[Status] = "Completed"),
    COUNTROWS(FactTraining),
    0
)
```

**Step 2: Insert KPI Card visuals**

1. Insert six **Card** visuals across the top of the page
2. Assign one measure to each card

| Card | Measure | Format | Target Color |
|------|---------|--------|-------------|
| Headcount | `[Active Headcount]` | Whole number | Blue (#4472C4) |
| Attrition Rate | `[Attrition Rate]` | Percentage, 1 decimal | Red if > 15%, Green if ≤ 15% |
| Avg Tenure | `[Avg Tenure Years]` | 1 decimal + " years" | Neutral blue |
| Avg Performance | `[Avg Performance Score]` | 1 decimal + " / 5.0" | Green if ≥ 3.5 |
| New Hires | `[New Hires Current Year]` | Whole number | Blue |
| Training Rate | `[Training Completion Rate]` | Percentage, 1 decimal | Green if ≥ 90% |

**Step 3: Apply conditional formatting**

1. Select the Attrition Rate card → **Format** → **Callout value** → **Color** → **fx**
2. **Format style**: Rules
3. Rule 1: Value > 0.15 → Red (#FF0000)
4. Rule 2: Value ≤ 0.15 → Green (#00B050)
5. Repeat similar conditional formatting for other KPIs

**Step 4: Add category labels**

1. **Format** pane → **Category label** → toggle **On**
2. The measure name appears below the value as a subtitle

> **Exam Tip:** Card visuals show a single aggregate value prominently. Use conditional formatting (rules-based or field-value) to make KPIs change color based on thresholds. The category label acts as a subtitle. For more control over layout, use a Multi-row Card.

---

### Question 2 – Headcount Trend Area Chart

**Scenario:** The CHRO wants to track how the workforce has grown or declined over the past 24 months, distinguishing between Active, Terminated, and On Leave employees.

**Task:** Create a stacked area chart showing headcount by employment status over time.

#### ✅ Step-by-Step Solution

**Step 1: Create a snapshot headcount measure**

```dax
Headcount as of Date =
CALCULATE(
    COUNTROWS(DimEmployee),
    FILTER(
        ALL(DimEmployee),
        DimEmployee[HireDate] <= MAX(DimDate[Date])
            && (
                ISBLANK(DimEmployee[TerminationDate])
                || DimEmployee[TerminationDate] > MAX(DimDate[Date])
            )
    )
)
```

**Step 2: Insert a Stacked Area Chart**

1. Select **Stacked Area Chart** from the Visualizations pane
2. Configure:

| Field Well | Field |
|-----------|-------|
| **X-axis** | `DimDate[Date]` (set to Month granularity) |
| **Y-axis** | `[Headcount as of Date]` |
| **Legend** | (leave empty for total headcount, or split by status) |

**Step 3: Alternative — split by department**

1. Add `DimEmployee[Department]` to the **Legend** well
2. Each department gets a separate color band in the area chart

**Step 4: Format the chart**

1. **Format** pane → **X-axis** → set to display Month-Year format
2. **Data labels** → toggle **On** for the topmost series (total line)
3. **Legend** → position: **Top center**
4. **Area transparency** → set to **30%** for better readability of overlapping areas

**Step 5: Add an analytics reference line**

1. **Analytics** pane → **Average line** → toggle **On**
2. This shows the average headcount across the time period

> **Exam Tip:** Area charts are best for showing cumulative totals over time. The stacked variant shows how components contribute to the total. Use Month granularity on the date axis for workforce trends. The Analytics pane provides reference lines (average, median, min, max, constant, percentile).

---

### Question 3 – Attrition by Department Bar Chart

**Scenario:** The CHRO wants to see which departments have the highest attrition, both in absolute count and as a percentage of department headcount.

**Task:** Create a clustered bar chart with attrition count and a reference line for the company average.

#### ✅ Step-by-Step Solution

**Step 1: Create department-level attrition measures**

```dax
Dept Attrition Rate =
DIVIDE(
    [Attrition Count],
    [Attrition Count] + [Active Headcount],
    0
)
```

**Step 2: Insert a Clustered Bar Chart**

1. Select **Clustered Bar Chart**
2. Configure:
   - **Y-axis**: `DimEmployee[Department]`
   - **X-axis**: `[Attrition Count]`
3. Sort descending by `[Attrition Count]`

**Step 3: Add a company-average reference line**

1. **Analytics** pane → **Average line** → toggle **On**
2. Set color to **Red dashed** line
3. Add a **Data label** to the average line showing the value

**Step 4: Apply conditional formatting**

1. **Format** pane → **Columns** → **Color** → click **fx**
2. **Format style**: Rules
3. Rule 1: If `[Dept Attrition Rate]` > company average → Red (#FF0000)
4. Rule 2: If `[Dept Attrition Rate]` ≤ company average → Blue (#4472C4)

**Step 5: Add data labels and tooltips**

1. **Data labels** → toggle **On**, position: **Outside end**
2. Tooltips: add `[Dept Attrition Rate]` and `[Active Headcount]` to the **Tooltips** well

> **Exam Tip:** Bar charts are best for comparing a single metric across categories. The Analytics pane adds reference lines (average, median, constant) to highlight benchmarks. Conditional bar color using rules-based formatting helps users instantly identify above/below-average departments.

---

### Question 4 – Performance Distribution Histogram

**Scenario:** HR Business Partners need to see the distribution of performance scores across the company to identify if ratings follow a normal distribution or are skewed.

**Task:** Create a histogram showing the count of employees in each performance score bracket.

#### ✅ Step-by-Step Solution

**Step 1: Create a performance bracket calculated column**

```dax
Performance Bracket =
SWITCH(
    TRUE(),
    FactPerformance[OverallScore] >= 4.5, "4.5 - 5.0 (Outstanding)",
    FactPerformance[OverallScore] >= 3.5, "3.5 - 4.4 (Exceeds)",
    FactPerformance[OverallScore] >= 2.5, "2.5 - 3.4 (Meets)",
    FactPerformance[OverallScore] >= 1.5, "1.5 - 2.4 (Below)",
    "1.0 - 1.4 (Needs Improvement)"
)
```

**Step 2: Create a sort-order column**

```dax
Performance Bracket Sort =
SWITCH(
    TRUE(),
    FactPerformance[OverallScore] >= 4.5, 5,
    FactPerformance[OverallScore] >= 3.5, 4,
    FactPerformance[OverallScore] >= 2.5, 3,
    FactPerformance[OverallScore] >= 1.5, 2,
    1
)
```

**Step 3: Insert a Clustered Column Chart**

1. Select **Clustered Column Chart**
2. Configure:
   - **X-axis**: `FactPerformance[Performance Bracket]` (sorted by `Performance Bracket Sort`)
   - **Y-axis**: Count of `FactPerformance[OverallScore]`

**Step 4: Sort the brackets**

1. Select `Performance Bracket` in the Data pane
2. **Column tools** → **Sort by column** → select `Performance Bracket Sort`

**Step 5: Format as a histogram**

1. **Format** pane → **Columns** → **Spacing** → set **Inner padding** to **0%** (bars touch)
2. Apply a color gradient: **Color** → **fx** → **Gradient**
   - Minimum (low scores): Red
   - Middle: Yellow
   - Maximum (high scores): Green

> **Exam Tip:** Power BI does not have a native histogram visual. Create one using a Clustered Column Chart with binned categories. Use calculated columns to create bins and a separate sort column to control display order. The **Sort by column** feature is essential for custom sort orders.

---

### Question 5 – Diversity Donut Charts

**Scenario:** The CHRO needs to see gender distribution and age group breakdown for diversity reporting, with the ability to filter by department.

**Task:** Create two donut charts for gender and age group distribution.

#### ✅ Step-by-Step Solution

**Step 1: Insert a Donut Chart for gender**

1. Select **Donut Chart** from the Visualizations pane
2. Configure:
   - **Legend**: `DimEmployee[Gender]`
   - **Values**: `[Active Headcount]`

**Step 2: Insert a Donut Chart for age group**

1. Select another **Donut Chart**
2. Configure:
   - **Legend**: `DimEmployee[AgeGroup]`
   - **Values**: `[Active Headcount]`

**Step 3: Set custom colors**

1. **Format** pane → **Slices** → click each legend item and assign:
   - Gender: Use two contrasting colors (e.g., Teal #2E8B8B, Orange #E87722)
   - Age groups: Use a sequential palette from light to dark

**Step 4: Add detail labels**

1. **Format** pane → **Detail labels**:
   - **Label contents**: Category, Value, Percent of total
   - **Position**: Outside
   - **Font size**: 9

**Step 5: Center text using a card overlay (optional)**

1. Place a **Card** visual in the center of each donut
2. For gender: display `[Active Headcount]` (total)
3. For age: display `[Avg Tenure Years]`
4. Set the card background to transparent

> **Exam Tip:** Donut charts show proportional distribution of a category. Limit to 5–7 slices for readability (combine small categories into "Other"). Donut and pie charts are best for part-to-whole comparisons. The exam may ask you to choose between a donut chart and a stacked bar chart.

---

### Question 6 – Employee Tenure Heatmap Matrix

**Scenario:** HR Business Partners want to see employee counts by department and tenure band, with a color heatmap highlighting concentrations.

**Task:** Create a matrix visual with background color conditional formatting to create a heatmap effect.

#### ✅ Step-by-Step Solution

**Step 1: Create a tenure band calculated column**

```dax
Tenure Band =
VAR TenureYears = DATEDIFF(DimEmployee[HireDate], TODAY(), YEAR)
RETURN
SWITCH(
    TRUE(),
    TenureYears >= 10, "10+ Years",
    TenureYears >= 5, "5-9 Years",
    TenureYears >= 2, "2-4 Years",
    TenureYears >= 1, "1-2 Years",
    "< 1 Year"
)
```

**Step 2: Insert a Matrix visual**

1. Select **Matrix** from the Visualizations pane
2. Configure:
   - **Rows**: `DimEmployee[Department]`
   - **Columns**: `DimEmployee[Tenure Band]`
   - **Values**: `[Active Headcount]`

**Step 3: Apply heatmap conditional formatting**

1. Select the Matrix → **Format** pane → **Cell elements** → select `[Active Headcount]`
2. Toggle **Background color** → click **fx**
3. **Format style**: Gradient
   - **Minimum**: White (#FFFFFF) — fewest employees
   - **Maximum**: Dark Blue (#08306B) — most employees
4. Click **OK**

**Step 4: Sort tenure bands correctly**

1. Create a `Tenure Band Sort` column:

```dax
Tenure Band Sort =
VAR TenureYears = DATEDIFF(DimEmployee[HireDate], TODAY(), YEAR)
RETURN
SWITCH(
    TRUE(),
    TenureYears >= 10, 5,
    TenureYears >= 5, 4,
    TenureYears >= 2, 3,
    TenureYears >= 1, 2,
    1
)
```

2. **Column tools** → **Sort by column**: Sort `Tenure Band` by `Tenure Band Sort`

**Step 5: Add subtotals**

1. **Format** pane → **Row subtotals** → toggle **On**
2. **Column subtotals** → toggle **On**
3. This shows department totals and tenure band totals

> **Exam Tip:** Matrix visuals with gradient background colors create an effective heatmap. Configure via **Cell elements > Background color > Gradient**. Matrices support row and column subtotals, stepped layout for hierarchies, and conditional formatting per column. Heatmaps are useful for identifying concentrations in two-dimensional data.

---

### Question 7 – Training Completion Stacked Bar

**Scenario:** The L&D team wants to see training completion status by department, showing the proportion of completed, in-progress, and not-started enrollments.

**Task:** Create a 100% stacked bar chart for training status by department.

#### ✅ Step-by-Step Solution

**Step 1: Insert a 100% Stacked Bar Chart**

1. Select **100% Stacked Bar Chart** from the Visualizations pane
2. Configure:
   - **Y-axis**: `DimEmployee[Department]`
   - **X-axis**: Count of `FactTraining[TrainingID]` (or `COUNTROWS(FactTraining)`)
   - **Legend**: `FactTraining[Status]`

**Step 2: Set custom colors for status**

1. **Format** pane → **Columns** → click each legend series:
   - **Completed**: Green (#00B050)
   - **In Progress**: Yellow (#FFC000)
   - **Not Started**: Red (#FF0000)

**Step 3: Add data labels**

1. **Format** pane → **Data labels** → toggle **On**
2. **Label contents**: Percent of total
3. **Font size**: 9

**Step 4: Sort by completion rate**

1. Click the ellipsis (…) on the visual → **Sort by** → `[Training Completion Rate]` → Descending
2. Departments with highest completion appear at the top

**Step 5: Add a visual-level filter**

1. Optionally filter to only current-year training enrollments
2. Drag `DimDate[Year]` to **Filters on this visual** → select current year

> **Exam Tip:** 100% Stacked Bar Charts show proportional distribution within each category, making it easy to compare completion rates across departments. Use custom colors for status categories (green = good, red = needs attention). The exam may ask when to use 100% stacked vs. regular stacked bar charts.

---

### Question 8 – Decomposition Tree for Attrition Drivers

**Scenario:** The CHRO wants to interactively explore what combination of factors (department, office, tenure, job level, age group) contributes most to employee attrition.

**Task:** Configure a Decomposition Tree AI visual to analyze attrition drivers.

#### ✅ Step-by-Step Solution

**Step 1: Insert the Decomposition Tree visual**

1. From the Visualizations pane, select **Decomposition Tree**
2. Configure:
   - **Analyze**: `[Attrition Count]`
   - **Explain by**: `DimEmployee[Department]`, `DimEmployee[Office]`, `DimEmployee[Tenure Band]`, `DimEmployee[JobLevel]`, `DimEmployee[AgeGroup]`

**Step 2: Use the visual interactively**

1. The visual initially shows the total attrition count
2. Click the **"+"** button at the first node
3. Three options appear:
   - **High value** (light bulb + up arrow) — AI finds the dimension with the highest attrition
   - **Low value** (light bulb + down arrow) — AI finds the dimension with the lowest attrition
   - **Manual selection** — choose which dimension to split by

**Step 3: Perform an AI-guided drill-down**

1. Click **High value** at the first level → AI selects (e.g.) **Department: Engineering** (highest attrition)
2. Click **High value** at the second level → AI selects (e.g.) **Tenure Band: < 1 Year**
3. Click **High value** at the third level → AI selects (e.g.) **Job Level: 1**

**Example result path:**

```
Attrition Count: 45
  └── Department: Engineering (18)
        └── Tenure Band: < 1 Year (12)
              └── Job Level: 1 (9)
                    └── Office: Seattle (7)
```

**Insight:** Most attrition comes from entry-level Engineering employees in Seattle within their first year.

**Step 4: Lock a level**

1. Right-click a node → **Lock** to prevent it from changing when filters are applied
2. This is useful for maintaining a consistent drill path while changing slicers

> **Exam Tip:** The Decomposition Tree uses AI (statistical analysis) to suggest the most impactful splits at each level. **High value** finds the category contributing most to the measure; **Low value** finds the least. Users can also manually choose dimensions. This visual is key for the "Identify patterns and trends" exam objective.

---

### Question 9 – Key Influencers for Performance Ratings

**Scenario:** HR Business Partners want to understand what factors influence employees receiving high performance ratings (score ≥ 4.0). They want to know if department, tenure, training completion, or office location has an impact.

**Task:** Configure a Key Influencers visual to analyze high-performance drivers.

#### ✅ Step-by-Step Solution

**Step 1: Create a performance category column**

```dax
Performance Category =
IF(
    FactPerformance[OverallScore] >= 4.0,
    "High Performer",
    "Standard"
)
```

> **Note:** Create this as a **calculated column** (not a measure) because Key Influencers needs row-level data.

**Step 2: Insert the Key Influencers visual**

1. Select **Key Influencers** from the Visualizations pane
2. Configure:

| Field Well | Field |
|-----------|-------|
| **Analyze** | `FactPerformance[Performance Category]` |
| **Explain by** | `DimEmployee[Department]`, `DimEmployee[Office]`, `DimEmployee[Tenure Band]`, `DimEmployee[JobLevel]`, `DimEmployee[AgeGroup]`, `FactTraining[Training Count]` |

**Step 3: Set the target**

1. In the visual's dropdown: **"What influences Performance Category to be ___"**
2. Select **"High Performer"**

**Step 4: Interpret results**

The visual shows ranked factors:
- "When Department is **Engineering**, the likelihood of High Performer is **2.1x** more likely"
- "When Tenure Band is **5-9 Years**, the likelihood is **1.8x** more likely"
- "When Training Count is **high**, the likelihood is **1.5x** more likely"

**Step 5: View Top Segments**

1. Click the **Top segments** tab
2. Review automatically detected clusters of employees that are more likely to be high performers
3. Example: "Engineering employees with 5+ years tenure and 3+ training completions = 78% high performers"

> **Exam Tip:** Key Influencers uses logistic regression (for categorical targets) or linear regression (for continuous targets). **Explain by** fields should be potential causal factors. The **Top segments** tab uses clustering to find groups. Calculated columns (not measures) must be used for the Analyze field.

---

### Question 10 – Org Chart with Parent-Child Hierarchy

**Scenario:** The CHRO wants to visualize the organizational structure showing reporting relationships from executives down to individual contributors.

**Task:** Flatten the parent-child employee hierarchy and create a visual representing the org structure.

#### ✅ Step-by-Step Solution

**Step 1: Create PATH columns on DimEmployee**

```dax
OrgPath =
PATH(DimEmployee[EmployeeID], DimEmployee[ManagerID])
```

```dax
OrgLevel =
PATHLENGTH(DimEmployee[OrgPath])
```

```dax
Level1Manager =
LOOKUPVALUE(
    DimEmployee[FullName],
    DimEmployee[EmployeeID],
    VALUE(PATHITEM(DimEmployee[OrgPath], 1))
)
```

```dax
Level2Manager =
IF(
    PATHLENGTH(DimEmployee[OrgPath]) >= 2,
    LOOKUPVALUE(
        DimEmployee[FullName],
        DimEmployee[EmployeeID],
        VALUE(PATHITEM(DimEmployee[OrgPath], 2))
    )
)
```

```dax
Level3Manager =
IF(
    PATHLENGTH(DimEmployee[OrgPath]) >= 3,
    LOOKUPVALUE(
        DimEmployee[FullName],
        DimEmployee[EmployeeID],
        VALUE(PATHITEM(DimEmployee[OrgPath], 3))
    )
)
```

**Step 2: Build a hierarchy**

1. Create a hierarchy: `Level1Manager` → `Level2Manager` → `Level3Manager` → `FullName`
2. This represents: Executive → Director → Manager → Employee

**Step 3: Display in a Matrix visual**

1. Insert a **Matrix** visual
2. Configure:
   - **Rows**: The org hierarchy (Level1 → Level2 → Level3 → FullName)
   - **Values**: `[Active Headcount]`, `[Avg Performance Score]`
3. Enable **Stepped layout**: **Format** → **Row headers** → **Stepped layout** → **On**
4. This indents each level, creating a tree-like view

**Step 4: Add expand/collapse controls**

1. The Matrix automatically provides **+/-** icons for expanding and collapsing hierarchy levels
2. Users click **+** to drill down into a manager's direct reports

> **Exam Tip:** Parent-child hierarchies use `PATH()`, `PATHITEM()`, and `PATHLENGTH()` to flatten recursive relationships into columnar levels. Each level is created with `LOOKUPVALUE` + `PATHITEM`. The flattened hierarchy can then be used in Matrix visuals with stepped layout. This is a frequently tested pattern in PL-300.

---

### Question 11 – Drill-Through Employee Detail Page

**Scenario:** Department managers want to right-click on an employee name in any visual and navigate to a detailed Employee Profile page showing that person's performance history, training records, and attendance summary.

**Task:** Create a drill-through page for individual employee analysis.

#### ✅ Step-by-Step Solution

**Step 1: Create the target page**

1. Add a new report page → rename to **"Employee Profile"**

**Step 2: Configure drill-through**

1. On the Employee Profile page, drag `DimEmployee[FullName]` into the **Drill-through filters** well
2. Power BI adds a **Back** button automatically

**Step 3: Add visuals to the Employee Profile page**

| Visual | Type | Configuration |
|--------|------|---------------|
| Employee Name | Card | `DimEmployee[FullName]` |
| Department & Title | Multi-row Card | `DimEmployee[Department]`, `DimEmployee[JobTitle]`, `DimEmployee[Office]` |
| Performance Trend | Line Chart | X: `FactPerformance[ReviewDate]`, Y: `FactPerformance[OverallScore]` |
| Training Summary | Table | `FactTraining[CourseName]`, `FactTraining[Status]`, `FactTraining[CompletionDate]` |
| Attendance Summary | Card | `[Attendance Rate]`, `[Absent Days]` |
| Tenure | Card | `[Employee Tenure]` measure |

**Step 4: Create the employee tenure measure**

```dax
Employee Tenure =
VAR HireDate = MIN(DimEmployee[HireDate])
RETURN
DATEDIFF(HireDate, TODAY(), YEAR) & " years, " &
MOD(DATEDIFF(HireDate, TODAY(), MONTH), 12) & " months"
```

**Step 5: Test the drill-through**

1. Go to Page 1 → right-click on any employee-related data point
2. **Drill through** → **Employee Profile**
3. Verify the page filters to the selected employee
4. Click **Back** to return

> **Exam Tip:** Drill-through fields are configured on the **target** page. Multiple drill-through fields can be added (e.g., employee name + department). Cross-report drill-through works across reports in the same workspace — enable under File > Options > Report settings.

---

### Question 12 – Bookmarks for Department Views

**Scenario:** The CHRO frequently switches between department-specific views during leadership meetings. They want quick-access buttons for each department view plus an "All Departments" reset button.

**Task:** Create bookmarks and buttons for preset department filter views.

#### ✅ Step-by-Step Solution

**Step 1: Create bookmarks for each department**

1. Set the Department slicer to **"Engineering"** only
2. Go to **View** tab → **Bookmarks** pane → click **Add**
3. Rename to **"Engineering View"**
4. Right-click → ensure **Data** is checked (saves the filter state)
5. Repeat for each department:

| Bookmark Name | Department Filter |
|--------------|-------------------|
| Engineering View | Engineering |
| Sales View | Sales |
| Marketing View | Marketing |
| Finance View | Finance |
| HR View | Human Resources |
| Operations View | Operations |
| All Departments | (clear all slicer selections) |

**Step 2: Create navigation buttons**

1. **Insert** → **Buttons** → **Blank** → add 7 buttons
2. Set button text for each: "All", "Eng", "Sales", "Mktg", "Fin", "HR", "Ops"

**Step 3: Link buttons to bookmarks**

1. Select the "Eng" button → **Format** → **Action** → toggle **On**
2. **Type**: Bookmark
3. **Bookmark**: Engineering View
4. Repeat for all 7 buttons

**Step 4: Style the buttons**

1. Arrange buttons in a horizontal row below the navigation bar
2. **Format** → **Button style**:
   - Default: Light grey background (#E8E8E8), dark text
   - On hover: Teal background (#2E8B8B), white text
3. Set consistent size (80 × 30 px) for all buttons

> **Exam Tip:** Bookmarks save three components: **Data** (filter state), **Display** (visual visibility), and **Current page**. You can choose which components to save. Combine bookmarks with buttons for guided analytics. The "All Departments" bookmark with all filters cleared acts as a reset button.

---

### Question 13 – Smart Narrative Visual

**Scenario:** The CHRO wants an AI-generated text summary on the Executive HR Overview that automatically describes key trends in plain language (e.g., "Headcount increased 5% this quarter, driven primarily by Engineering new hires").

**Task:** Add a Smart Narrative visual that summarizes the page data.

#### ✅ Step-by-Step Solution

**Step 1: Insert the Smart Narrative visual**

1. From the Visualizations pane, select **Smart Narrative** (also called Narrative visual)
2. Place it on the page (recommended: bottom of the page or in a dedicated insights section)
3. The visual automatically generates a text summary based on the visuals on the page

**Step 2: Review the auto-generated text**

The narrative visual analyzes all visuals on the page and generates insights like:
- "Active headcount is **485**, which is **5.2% higher** than last year."
- "**Engineering** has the highest attrition at **18 employees**, followed by Sales at **12**."
- "Training completion rate is **87%**, with **Marketing** having the lowest at **72%**."

**Step 3: Customize the narrative**

1. Click on the Smart Narrative visual to edit it
2. You can:
   - Delete auto-generated sentences you do not want
   - Add custom text
   - Insert **dynamic values** by typing **+** and selecting a measure
   - Example: "The current headcount is **+ [Active Headcount]** employees."

**Step 4: Add dynamic measure references**

1. In the narrative editor, type: "Year-over-year attrition changed by "
2. Click **+ Value** → select `[Attrition YoY Change]` measure
3. The narrative dynamically updates when filters change

**Step 5: Resize and format**

1. Set the visual to span the full width of a section
2. **Format** pane → **Font**: Segoe UI, size 11
3. Background: Light grey (#F5F5F5) to visually separate it from charts

> **Exam Tip:** The Smart Narrative visual uses AI to generate text summaries. It updates dynamically when filters change. You can customize text and insert dynamic measure values with the **+ Value** button. This visual is part of the AI visuals category tested in PL-300 — know where to find it and how to customize it.

---

### Question 14 – Conditional Formatting on HR Metrics Table

**Scenario:** HR Business Partners want a department scorecard table showing headcount, attrition rate, average performance, and training completion, with color-coded indicators for metrics that are above or below target.

**Task:** Create a table with multiple conditional formatting types applied.

#### ✅ Step-by-Step Solution

**Step 1: Create the table visual**

1. Insert a **Table** visual
2. Add columns:
   - `DimEmployee[Department]`
   - `[Active Headcount]`
   - `[Attrition Rate]`
   - `[Avg Performance Score]`
   - `[Training Completion Rate]`

**Step 2: Apply background color to Attrition Rate**

1. **Format** → **Cell elements** → select **Attrition Rate**
2. Toggle **Background color** → click **fx**
3. **Format style**: Rules

| Rule | Condition | Color |
|------|-----------|-------|
| 1 | Value > 0.20 | 🔴 Red (#FF0000) |
| 2 | Value > 0.10 AND ≤ 0.20 | 🟡 Yellow (#FFC000) |
| 3 | Value ≤ 0.10 | 🟢 Green (#00B050) |

**Step 3: Add icons to Performance Score**

1. **Cell elements** → select **Avg Performance Score**
2. Toggle **Icons** → click **fx**
3. **Icon style**: Stars (or custom)

| Rule | Condition | Icon |
|------|-----------|------|
| 1 | Value ≥ 4.0 | ⭐⭐⭐ (or green checkmark) |
| 2 | Value ≥ 3.0 AND < 4.0 | ⭐⭐ (or yellow dash) |
| 3 | Value < 3.0 | ⭐ (or red X) |

**Step 4: Add data bars to Training Completion Rate**

1. **Cell elements** → select **Training Completion Rate**
2. Toggle **Data bars** → click **fx**
3. Positive bar color: Teal (#2E8B8B)
4. Show bar and value together

**Step 5: Format headers**

1. **Format** → **Column headers**:
   - Background: Dark Teal (#1A5C5C)
   - Font color: White
   - Bold, size 10
2. **Values** → Alternate row color: Light grey (#F8F8F8)

> **Exam Tip:** Tables and matrices support four conditional formatting types per column: background color, font color, data bars, and icons. Each is independent — you can apply multiple types to the same column. Rules-based formatting uses static thresholds; field-value formatting uses a DAX measure for complete control.

---

### Question 15 – Dynamic Title and Subtitle Measures

**Scenario:** The CHRO wants each page title to dynamically reflect the current filter context — for example, "HR Overview — Engineering Department — 2024" when filtered, or "HR Overview — All Departments — All Years" when no filters are applied.

**Task:** Create a dynamic title that updates based on slicer selections.

#### ✅ Step-by-Step Solution

**Step 1: Create a dynamic title measure**

```dax
Page Title =
VAR SelectedDept =
    IF(
        HASONEVALUE(DimEmployee[Department]),
        SELECTEDVALUE(DimEmployee[Department]),
        "All Departments"
    )
VAR SelectedYear =
    IF(
        HASONEVALUE(DimDate[Year]),
        FORMAT(SELECTEDVALUE(DimDate[Year]), "0"),
        "All Years"
    )
RETURN
"HR Overview — " & SelectedDept & " — " & SelectedYear
```

**Step 2: Apply the dynamic title to a visual**

1. Select the visual (e.g., the headcount trend chart)
2. **Format** pane → **General** → **Title**
3. Click **fx** next to the title text
4. **Format style**: Field value
5. Select the `[Page Title]` measure
6. Click **OK**

**Step 3: Create a page-level dynamic title**

1. Insert a **Card** visual at the very top of the page
2. Set the field to `[Page Title]`
3. Format as a title:
   - **Callout value** → Font size: **20**, Bold, Color: **Dark Teal (#1A5C5C)**
   - Remove **Category label**
   - Set **Background** to transparent
   - Remove **Border**
4. This card now acts as a dynamic page header

**Step 4: Create a dynamic subtitle**

```dax
Page Subtitle =
VAR EmpCount = [Active Headcount]
VAR AttrRate = FORMAT([Attrition Rate], "0.0%")
RETURN
"Showing " & EmpCount & " active employees | Attrition rate: " & AttrRate
```

1. Insert a smaller **Card** below the title
2. Set field to `[Page Subtitle]`
3. Format with smaller font (size 11, grey color)

> **Exam Tip:** Dynamic titles use the **fx** button in the visual's Title format setting. They reference a DAX measure that responds to filter context. `SELECTEDVALUE()` returns the selected filter value; `HASONEVALUE()` checks if exactly one value is selected. This technique is commonly tested — know where to find the **fx** option.

---

## 3. Complete Business Dashboard Design

### Page 1: Executive HR Overview

```
┌──────────────────────────────────────────────────────────────────┐
│ [🏠 HR Overview] [📊 Performance & Training] [📉 Attrition]      │
│ [All] [Eng] [Sales] [Mktg] [Fin] [HR] [Ops]  ← dept bookmarks  │
├──────────────────────────────────────────────────────────────────┤
│  Dynamic Title: "HR Overview — All Departments — 2025"           │
│  Subtitle: "Showing 485 active employees | Attrition rate: 8.7%" │
├──────────────────────────────────────────────────────────────────┤
│ [Slicer: Year ▼]  [Slicer: Department ▼]  [Slicer: Office ▼]    │
├──────────┬──────────┬──────────┬──────────┬──────────┬───────────┤
│ Card:    │ Card:    │ Card:    │ Card:    │ Card:    │ Card:     │
│Headcount │Attrition │Avg Tenure│Avg Perf  │New Hires │Training % │
│  485     │  8.7%    │ 4.2 yrs  │ 3.6/5.0  │  42      │  87%      │
├──────────┴──────────┴──────────┴──────────┴──────────┴───────────┤
│                               │                                   │
│  Area Chart:                  │  Clustered Bar Chart:             │
│  Headcount Trend (24 months)  │  Attrition by Department          │
│  ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓   │  ████████████ Engineering         │
│                               │  █████████ Sales                  │
│                               │  ███████ Marketing                │
│                               │  --- avg reference line ---       │
├───────────────────────────────┼───────────────────────────────────┤
│                               │                                   │
│  Donut Chart:                 │  Donut Chart:                     │
│  Gender Distribution          │  Age Group Distribution           │
│  🔵 Male 58% 🟠 Female 42%   │  Sequential color palette         │
│                               │                                   │
├───────────────────────────────┴───────────────────────────────────┤
│  Smart Narrative: "Active headcount is 485, up 5.2% from last    │
│  year. Engineering has the highest attrition at 18 employees..."  │
└──────────────────────────────────────────────────────────────────┘
```

**Visual field configuration:**

| Visual | Type | Fields | Format Notes |
|--------|------|--------|-------------|
| KPI Cards (×6) | Card | Individual measures | Conditional color per KPI |
| Headcount Trend | Stacked Area | X: `DimDate[Date]` (Month), Y: `[Headcount as of Date]` | Analytics: average line |
| Attrition by Dept | Clustered Bar | Y: `DimEmployee[Department]`, X: `[Attrition Count]` | Analytics: average line, conditional bar color |
| Gender Donut | Donut | Legend: `DimEmployee[Gender]`, Values: `[Active Headcount]` | Custom Teal/Orange colors |
| Age Group Donut | Donut | Legend: `DimEmployee[AgeGroup]`, Values: `[Active Headcount]` | Sequential palette |
| Smart Narrative | Smart Narrative | Auto-generated + custom dynamic values | Light grey background |

---

### Page 2: Performance & Training

```
┌──────────────────────────────────────────────────────────────────┐
│ [🏠 HR Overview] [📊 Performance & Training] [📉 Attrition]      │
├──────────────────────────────────────────────────────────────────┤
│ [Slicer: Year ▼] [Slicer: Department ▼] [Slicer: Office ▼]      │
├──────────────────────────────┬───────────────────────────────────┤
│                              │                                   │
│  Column Chart:               │  100% Stacked Bar:               │
│  Performance Score           │  Training Completion by Dept     │
│  Distribution (Histogram)    │  ██████████████████ Engineering   │
│  ▂ ▄ ████ ██ ▆              │  ████████████████ Sales           │
│  1.0  2.0  3.0  4.0  5.0    │  ██████████████ Marketing         │
│                              │  🟢 Complete 🟡 In Prog 🔴 Not   │
├──────────────────────────────┼───────────────────────────────────┤
│                              │                                   │
│  Matrix: Tenure Heatmap      │  Key Influencers:                │
│  Dept / Tenure Band          │  What influences High Performer   │
│  [gradient background color] │  to be "High Performer"?          │
│                              │  • Dept = Engineering: 2.1x       │
│                              │  • Tenure 5-9 yrs: 1.8x          │
│                              │  • Training Count high: 1.5x      │
├──────────────────────────────┴───────────────────────────────────┤
│  Table: Department Scorecard                                     │
│  Department | Headcount | Attrition | Perf Score | Training %    │
│  ───────────┼───────────┼───────────┼────────────┼────────────── │
│  Engineering│    125    │  🔴 14%   │  ⭐⭐⭐ 4.1 │ ████████ 92%  │
│  Sales      │     95    │  🟡 11%   │  ⭐⭐ 3.5   │ ██████ 78%    │
└──────────────────────────────────────────────────────────────────┘
```

**Visual field configuration:**

| Visual | Type | Fields |
|--------|------|--------|
| Performance Histogram | Clustered Column | X: `[Performance Bracket]`, Y: Count of scores |
| Training Stacked Bar | 100% Stacked Bar | Y: Department, X: Training count, Legend: Status |
| Tenure Heatmap | Matrix | Rows: Department, Columns: Tenure Band, Values: Headcount (gradient bg) |
| Key Influencers | Key Influencers | Analyze: Performance Category, Explain by: Dept, Tenure, Training, Office |
| Department Scorecard | Table | Department, Headcount, Attrition Rate, Perf Score, Training % (conditional formatting) |

---

### Page 3: Attrition Deep Dive

```
┌──────────────────────────────────────────────────────────────────┐
│ [🏠 HR Overview] [📊 Performance & Training] [📉 Attrition]      │
├──────────────────────────────────────────────────────────────────┤
│ [Slicer: Year ▼] [Slicer: Department ▼] [Slicer: Office ▼]      │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Decomposition Tree:                                             │
│  Analyzing: Attrition Count                                      │
│  Explain by: Department, Office, Tenure Band, Job Level, Age     │
│                                                                  │
│  Attrition Count: 45                                             │
│    └── Department: Engineering (18)                               │
│          └── Tenure Band: < 1 Year (12)                          │
│                └── Job Level: 1 (9)                               │
│                      └── Office: Seattle (7)                      │
│                                                                  │
├──────────────────────────────┬───────────────────────────────────┤
│                              │                                   │
│  Line Chart:                 │  Clustered Bar Chart:             │
│  Monthly Attrition Trend     │  Attrition by Tenure Band         │
│  ── with forecast (6 months) │  ████████████ < 1 Year            │
│                              │  ████████ 1-2 Years               │
│                              │  ████ 2-4 Years                   │
│                              │  ██ 5-9 Years                     │
│                              │  █ 10+ Years                      │
│                              │                                   │
├──────────────────────────────┴───────────────────────────────────┤
│  Matrix: Org Hierarchy (stepped layout)                          │
│  Level1 → Level2 → Level3 → Employee | Headcount | Performance   │
│  + CEO                                                           │
│    + VP Engineering                                              │
│      + Sr Manager                                                │
│        Employee A, Employee B, Employee C                        │
└──────────────────────────────────────────────────────────────────┘
```

---

## 4. DAX Measures Reference

### Workforce Metrics

```dax
Active Headcount =
CALCULATE(
    COUNTROWS(DimEmployee),
    DimEmployee[EmploymentStatus] = "Active"
)
```

```dax
Attrition Count =
CALCULATE(
    COUNTROWS(DimEmployee),
    DimEmployee[EmploymentStatus] = "Terminated"
)
```

```dax
Attrition Rate =
DIVIDE(
    [Attrition Count],
    [Attrition Count] + [Active Headcount],
    0
)
```

```dax
Avg Tenure Years =
AVERAGEX(
    FILTER(DimEmployee, DimEmployee[EmploymentStatus] = "Active"),
    DATEDIFF(DimEmployee[HireDate], TODAY(), YEAR)
)
```

```dax
New Hires Current Year =
CALCULATE(
    COUNTROWS(DimEmployee),
    YEAR(DimEmployee[HireDate]) = YEAR(TODAY())
)
```

### Performance Metrics

```dax
Avg Performance Score =
AVERAGE(FactPerformance[OverallScore])
```

```dax
High Performer Count =
CALCULATE(
    COUNTROWS(FactPerformance),
    FactPerformance[OverallScore] >= 4.0
)
```

```dax
High Performer % =
DIVIDE([High Performer Count], COUNTROWS(FactPerformance), 0)
```

### Training Metrics

```dax
Training Completion Rate =
DIVIDE(
    CALCULATE(COUNTROWS(FactTraining), FactTraining[Status] = "Completed"),
    COUNTROWS(FactTraining),
    0
)
```

```dax
Training Count per Employee =
DIVIDE(
    COUNTROWS(FactTraining),
    [Active Headcount],
    0
)
```

### Time Intelligence

```dax
Headcount as of Date =
CALCULATE(
    COUNTROWS(DimEmployee),
    FILTER(
        ALL(DimEmployee),
        DimEmployee[HireDate] <= MAX(DimDate[Date])
            && (
                ISBLANK(DimEmployee[TerminationDate])
                || DimEmployee[TerminationDate] > MAX(DimDate[Date])
            )
    )
)
```

```dax
Attrition PY =
CALCULATE([Attrition Count], SAMEPERIODLASTYEAR(DimDate[Date]))
```

```dax
Attrition YoY Change =
[Attrition Count] - [Attrition PY]
```

### Dynamic Display Measures

```dax
Page Title =
VAR SelectedDept =
    IF(
        HASONEVALUE(DimEmployee[Department]),
        SELECTEDVALUE(DimEmployee[Department]),
        "All Departments"
    )
VAR SelectedYear =
    IF(
        HASONEVALUE(DimDate[Year]),
        FORMAT(SELECTEDVALUE(DimDate[Year]), "0"),
        "All Years"
    )
RETURN
"HR Overview — " & SelectedDept & " — " & SelectedYear
```

```dax
Page Subtitle =
VAR EmpCount = [Active Headcount]
VAR AttrRate = FORMAT([Attrition Rate], "0.0%")
RETURN
"Showing " & EmpCount & " active employees | Attrition rate: " & AttrRate
```

### Hierarchy Columns

```dax
OrgPath = PATH(DimEmployee[EmployeeID], DimEmployee[ManagerID])
```

```dax
OrgLevel = PATHLENGTH(DimEmployee[OrgPath])
```

---

## 5. Key Exam Concepts Summary

| Concept | Domain | Key Takeaway |
|---------|--------|-------------|
| **KPI Card visuals** | Visualize | Display single metrics; conditional formatting for threshold-based coloring |
| **Area charts** | Visualize | Best for cumulative trends over time; stacked variant shows component contributions |
| **Histograms** | Visualize | Created with column charts + binned calculated columns; no native histogram visual |
| **Donut charts** | Visualize | Part-to-whole comparison; limit to 5–7 slices for readability |
| **Matrix heatmaps** | Visualize | Gradient background color on matrix cells creates a heatmap; configure via Cell elements |
| **100% Stacked Bar** | Visualize | Shows proportional distribution within categories; ideal for completion/status breakdown |
| **Decomposition Tree** | Visualize | AI-powered drill-down; High/Low value suggests most impactful splits automatically |
| **Key Influencers** | Visualize | Logistic regression for categorical targets; Explain by fields provide context factors |
| **Parent-child hierarchy** | Model Data | PATH(), PATHITEM(), PATHLENGTH() flatten recursive relationships into levels |
| **Smart Narrative** | Visualize | AI-generated text summaries; customize with + Value for dynamic measure references |
| **Drill-through** | Visualize | Configured on target page; right-click triggers navigation; carries filter context |
| **Bookmarks** | Visualize | Save Data + Display state; link to buttons for preset filtered views |
| **Conditional formatting** | Visualize | Background color, font color, data bars, icons — each per column in tables/matrices |
| **Dynamic titles** | Visualize | Use fx button + DAX measure with SELECTEDVALUE/HASONEVALUE for context-aware titles |
| **Sort by column** | Model Data | Essential for custom sort orders (month names, tenure bands, performance brackets) |

---

*This dashboard guide is a companion to [Case Study 2 – HR Analytics](case-study-2-hr-analytics.md). Complete both for full PL-300 exam preparation.*
