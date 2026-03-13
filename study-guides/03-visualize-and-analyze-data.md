# Domain 3: Visualize and Analyze the Data (25–30%)

> **Exam Weight:** 25–30% | **Estimated Study Time:** 2 weeks

This domain covers creating and enhancing reports, and using analytical features to identify patterns and trends. It includes new Copilot features added in the January 2026 update.

---

## Table of Contents

1. [Create Reports](#1-create-reports)
2. [Enhance Reports for Usability and Storytelling](#2-enhance-reports-for-usability-and-storytelling)
3. [Identify Patterns and Trends](#3-identify-patterns-and-trends)
4. [Common Exam Pitfalls](#4-common-exam-pitfalls)
5. [Key Terms Glossary](#5-key-terms-glossary)

---

## 1. Create Reports

### 1.1 Selecting Appropriate Visuals

Choosing the right visual is a core exam skill. Know which visual suits each scenario:

| Scenario | Best Visual |
|---|---|
| Compare values across categories | **Clustered Bar / Clustered Column Chart** |
| Show part-to-whole relationship | **Pie Chart** or **Donut Chart** (≤ 7 categories) |
| Show trend over time | **Line Chart** or **Area Chart** |
| Compare trend + magnitude | **Combo Chart** (Line + Column) |
| Show distribution of values | **Histogram** (via Bar Chart with bins) |
| Show correlation between two measures | **Scatter Chart** |
| Display exact numbers prominently | **Card** or **Multi-row Card** |
| Show progress to goal | **Gauge** |
| Display tabular data | **Table** or **Matrix** |
| Show geographic data | **Map**, **Filled Map**, or **Azure Maps** |
| Show hierarchical data | **Treemap** or **Decomposition Tree** |
| Show KPI status | **KPI visual** |
| Ask natural language questions | **Q&A visual** |
| Show influence on an outcome | **Key Influencers** |
| Display a waterfall (cumulative) | **Waterfall Chart** |
| Show funnel stages | **Funnel Chart** |
| Display rich formatted content | **Paginated Report** (via Power BI Report Builder) |
| Generate narrative text automatically | **Narrative (Smart Narrative) Visual** |
| Summarise with AI-generated text | **Copilot Narrative Visual** |

> **Exam Tip:** For geographic data: **Map** uses bubble overlays; **Filled Map** shades entire regions; **Azure Maps** is the most capable. Shape Map requires custom GeoJSON.

### 1.2 Visual Configuration and Formatting

Each visual has three panes in the **Visualizations panel**:
- **Build visual** (Fields) — assign columns to visual fields (Axis, Values, Legend, Tooltips, etc.)
- **Format visual** — configure appearance (colours, fonts, borders, titles)
- **Analytics** — add analytical lines (trend line, constant line, min/max lines, forecast)

**Key Formatting Options to Know:**

**Titles:**
- Can be static text or **dynamic** (data-driven) using a measure that returns text
```dax
// Dynamic title measure
Report Title = "Sales Report – " & FORMAT(MAX('Date'[Date]), "MMMM YYYY")
```

**Data Labels:**
- Enable via Format → Data labels
- Configure position, colour, decimal places, display units

**Conditional Formatting:**
Applied via **Format visual → Cell elements (for tables) or specific format options**:
- **Background colour** — colour cells based on value or rules
- **Font colour** — change text colour based on rules
- **Icons** — show traffic lights, arrows, or custom icons
- **Data bars** — in-cell bar charts in table columns
- **Web URL** — make column values clickable links

```dax
// Measure for conditional formatting (returns a colour hex string)
Sales Colour =
SWITCH(
    TRUE(),
    [Total Sales] >= 100000, "#00B050",   -- Green
    [Total Sales] >= 50000, "#FFFF00",    -- Yellow
    "#FF0000"                             -- Red
)
```

### 1.3 Report Themes

Themes apply consistent formatting across an entire report:

**Applying a Theme:**
- **View → Themes** → select built-in theme or browse for JSON file

**Custom Theme JSON Structure:**
```json
{
  "name": "Corporate Theme",
  "dataColors": ["#004B87", "#00A3E0", "#F7941D", "#009844"],
  "background": "#FFFFFF",
  "foreground": "#333333",
  "tableAccent": "#004B87",
  "visualStyles": {
    "barChart": { "*": { "dataPoint": [{ "fill": {"solid": {"color": "#004B87"}} }] } }
  }
}
```

**Theme scope:** Applies to all visuals on all pages. Individual visual formatting overrides the theme.

### 1.4 Slicers

Slicers provide interactive filtering on a report page:

| Slicer Style | Use Case |
|---|---|
| **List** | Categorical columns with few values |
| **Dropdown** | Categorical columns; saves space |
| **Between** | Numeric or date range |
| **Before/After** | Date filtering |
| **Relative Date** | "Last 7 days", "This month", "Last N quarters" |
| **Tile** | Image or text buttons for selection |
| **Slider** | Numeric range selection |

**Slicer Settings:**
- **Single select** vs **Multi-select** (with Ctrl)
- **Select all** option
- **Show "Select All"** toggle
- Slicer header customisation

### 1.5 Narrative Visual with Copilot

The **Narrative (Smart Narrative)** visual automatically generates text summaries of your data.

**Copilot Narrative Visual (January 2026 update):**
- Requires **Copilot-enabled capacity** (Fabric/Premium)
- Generates AI-written narrative summaries based on selected data
- Can be prompted with natural language to focus on specific insights
- Accessible via **Visualizations pane → Copilot visual**

**Usage Steps:**
1. Add the Narrative or Copilot visual to the canvas
2. It automatically summarises visible data
3. For Copilot visual: enter a prompt like "Summarise the key trends in sales by region"
4. The visual updates when data or filters change

### 1.6 Copilot for Creating Report Pages

A January 2026 feature that uses AI to generate entire report pages:

- Available in **Power BI service** with Copilot-enabled workspace
- In the report editor: **Edit → Copilot** side panel
- Prompt Copilot with requests like "Create a page showing sales performance by region and category"
- Copilot creates visuals, chooses layout, and wires up data automatically
- Output can be edited like any manually created report

> **Exam Tip:** Copilot for report creation requires a **Fabric capacity or Premium Per User** licence with Copilot enabled by the tenant admin.

### 1.7 Paginated Reports

Paginated reports are designed for **pixel-perfect, print-ready output** — they are different from standard Power BI reports.

| Feature | Power BI Report | Paginated Report |
|---|---|---|
| Created with | Power BI Desktop | Power BI Report Builder |
| File format | .pbix | .rdl |
| Layout | Responsive | Fixed/pixel-perfect |
| Data volume | Optimised for interactivity | Handles millions of rows |
| Output formats | Interactive, PDF | PDF, Excel, Word, CSV, XML |
| Best for | Exploration and analysis | Invoices, statements, regulated reports |

**Publishing Paginated Reports:**
- Requires **Premium, PPU, or Embedded** capacity
- Published to Power BI service like standard reports
- Can be embedded in Power BI apps

### 1.8 Visual Calculations

Visual calculations (added in 2024, prominent in January 2026 update) are DAX calculations defined **on the visual** rather than in the model.

**Key Characteristics:**
- Written directly in the visual's "Visual calculations" mode
- Access to visual-level context: ROWS, COLUMNS, PREVIOUS, NEXT
- Not stored in the semantic model — only affect that visual
- Ideal for running totals, rankings, and percent-of-total within a visual

**Visual Calculation Examples:**
```dax
// Running total across the current axis
Running Total = RUNNINGSUM([Total Sales])

// Percent of total for each row in the visual
% of Total = DIVIDE([Total Sales], COLLAPSE([Total Sales], ROWS))

// Compare to previous row
vs Previous = [Total Sales] - PREVIOUS([Total Sales])

// Rank within visual
Sales Rank = RANK(ORDERBY([Total Sales], DESC))
```

> **Exam Tip:** Visual calculations use their own set of functions (RUNNINGSUM, COLLAPSE, PREVIOUS, NEXT, RANK) that are distinct from regular DAX.

---

## 2. Enhance Reports for Usability and Storytelling

### 2.1 Bookmarks

Bookmarks capture the current state of a report page and can be used for navigation, storytelling, or alternate views.

**What Bookmarks Capture:**
- Visible/hidden state of visuals
- Slicer selections
- Cross-filter states
- Scroll position
- Spotlight state

**Bookmark Types:**
- **Report bookmark** — affects the entire page
- **Personal bookmark** — per-user bookmarks (created by report consumers in service)

**Creating and Using Bookmarks:**
1. **View → Bookmarks pane** → Add bookmark
2. Configure state of the page
3. Right-click bookmark → Update to save new state
4. Group bookmarks for organisation

**Bookmark Navigator:**
- Insert → Buttons → Navigator → Bookmark Navigator
- Creates an automatic navigation strip from your bookmarks

### 2.2 Buttons and Navigation

Buttons provide interactive navigation within reports:

**Button Types (Insert → Buttons):**
- **Back** — navigates to previous page
- **Blank** — customisable button
- **Q&A** — opens Q&A experience
- **Bookmark** — triggers a specific bookmark
- **Page navigation** — navigates to a specified page
- **Drill through** — triggers a drill-through action

**Button States:**
Each button has three configurable states:
- **Default** — normal appearance
- **On hover** — appearance when mouse hovers
- **On press** — appearance when clicked

**Configuring Button Action:**
- Format → Action → Type: Bookmark / Page navigation / Drill through / Web URL / Q&A
- For Page navigation: specify destination page
- For Bookmark: specify which bookmark to trigger

### 2.3 Tooltips

Tooltips appear when hovering over a data point:

**Default Tooltip:**
- Shows the field value and measure value for the hovered data point

**Custom Tooltip (Report Page Tooltip):**
1. Create a new report page
2. Page settings → Set as Tooltip page
3. Set the page size to Tooltip (typically 320×240 px)
4. Design the page with visuals
5. In the source visual: Format → Tooltips → Type: Report page → Page: [your tooltip page]

> **Exam Tip:** Tooltip pages must be explicitly set as tooltip in **Page information → Allow use as tooltip**.

### 2.4 Cross-Visual Interactions

When you click a data point in one visual, other visuals on the page respond. Control this behaviour:

**Configure Interactions:**
1. Click the source visual
2. **Format → Edit interactions** (or Format ribbon → Edit interactions)
3. For each target visual, choose: Filter | Highlight | None

| Interaction Type | Behaviour |
|---|---|
| **Filter** | Other visual shows only matching data |
| **Highlight** | Other visual dims non-matching data (default for most visuals) |
| **None** | Other visual is unaffected |

### 2.5 Drill Through

Drill through allows navigating from a summary to a detail page filtered by a specific value.

**Setting Up Drill Through:**
1. Create a detail report page
2. In the detail page's **Drill through** field well, add the field to filter by (e.g., ProductCategory)
3. Power BI automatically adds a **Back button** on the page
4. On any other page, right-click a data point on that field → Drill through → [detail page name]

**Cross-Report Drill Through:**
- Drill through to a page in a **different report**
- Enable via: File → Options → Report settings → Enable cross-report drill through
- The destination report must have the same field in its drill-through filter

### 2.6 Sync Slicers

Sync slicers allow a slicer on one page to filter visuals on other pages:

1. Select a slicer
2. **View → Sync slicers**
3. In the Sync slicers pane, tick which pages the slicer should **sync** to and which pages it should be **visible** on
4. Visible = shown on page; Sync = filters data on page (even if not visible)

> **Exam Tip:** A slicer can be synced to pages where it is invisible — it still applies the filter silently. This is useful for applying global filters without cluttering every page.

### 2.7 Drill Down

Drill down navigates within a hierarchy within the same visual:

**Enabling Drill Down:**
- Add multiple fields to the Axis (hierarchy)
- Use the visual header drill icons (down arrow, fork arrow)

| Icon | Action |
|---|---|
| **↓ (single arrow)** | Drill down to next level for selected item only |
| **⑂ (fork arrow)** | Drill down to next level for all items |
| **↑ (up arrow)** | Drill up to previous level |
| **➡ (right arrow)** | Expand one level down (shows all levels simultaneously) |

### 2.8 Sorting

**Sorting Options:**
- Click a column header in a table/matrix to sort ascending/descending
- In charts: **More options (…) → Sort axis → [field]** and **Sort ascending/descending**
- To sort by a non-displayed field: create a calculated column and use **Sort by Column** in the model

### 2.9 Mobile Layout

Design dedicated mobile views for phone users:

1. **View → Mobile layout**
2. The canvas switches to portrait phone dimensions
3. Drag visuals from the panel onto the mobile canvas
4. Resize and arrange for phone screens
5. Not all visuals need to be included

### 2.10 Accessibility

Power BI reports should be accessible to screen readers and keyboard users:

- Add **Alt text** to each visual (Format → General → Alt text)
- Use **Tab order** (View → Tab order) to control keyboard navigation sequence
- Use **high-contrast themes** for visually impaired users
- Provide **data table alternatives** for complex visuals
- Use **descriptive titles** on every visual
- Avoid colour-only encoding; use patterns or labels as well

### 2.11 Automatic Page Refresh

Automatic page refresh updates DirectQuery/Streaming visuals at a set interval:

- **Page settings → Page refresh** → set interval (e.g., every 30 seconds)
- **Fixed interval** — refresh on a set schedule
- **Change detection** — refresh only when data has changed (requires Premium capacity)
- Works only with DirectQuery or Streaming datasets (not Import mode)

> **Exam Tip:** Change detection requires a **Premium** capacity and a measure that returns a scalar value used to detect data changes.

---

## 3. Identify Patterns and Trends

### 3.1 Analyze Feature (Explain the Increase/Decrease)

The **Analyze** feature provides automatic explanations for data changes:

- Right-click a data point in a line or bar chart
- Select **Analyze → Explain the increase** (or decrease, or difference)
- Power BI generates a list of possible explanations using machine learning
- Results show contributing factors with magnitude

> **Exam Tip:** The Analyze feature is available in standard Power BI service and works without any additional licence or configuration.

### 3.2 Grouping, Binning, and Clustering

**Grouping:**
- Group categorical values manually
- Right-click a field in a visual → Group → create named groups
- Example: Group "January, February, March" → "Q1"

**Binning:**
- Create buckets for continuous numeric data
- Right-click a numeric or date column in Fields pane → New group → Bin type
- Bin types: **Bin size** (fixed width) or **Number of bins** (fixed count)
- Creates a new grouped column usable as an axis

**Clustering:**
- Available on Scatter charts
- **Analytics → Find clusters** (or visual Format → Clusters)
- Uses k-means algorithm to automatically identify clusters in scatter plot data
- Adds a cluster assignment column to the data

### 3.3 AI Visuals

#### Q&A Visual
- Allows users to ask natural language questions
- Returns a visual automatically chosen by the AI
- Available as a **visual on the canvas** or via the Q&A button in the toolbar
- **Teach Q&A:** Define synonyms and terms to improve accuracy (Modeling → Q&A setup)
- Q&A visual can be converted to a standard visual after asking a question

#### Key Influencers Visual
- Identifies which factors most influence a metric's value
- **Analyse**: the measure you want to explain
- **Explain by**: the fields you want to test as influencers
- Two tabs:
  - **Key Influencers** — shows top factors that drive the metric up or down
  - **Top segments** — shows combinations of factors (segments) with highest/lowest values

> **Exam Tip:** Key Influencers only works with **Import mode** datasets. It requires the data in the visual to be aggregated appropriately.

#### Decomposition Tree Visual
- Interactive exploration of a metric broken down by multiple dimensions
- Start with the overall value; click `+` to add a dimension breakdown
- **AI splits** (lightning bolt icon) — automatically chooses the field with the highest or lowest next value
- **Explain the next level** functionality
- Supports drill across multiple fields in sequence

### 3.4 Analytics Pane — Reference Lines

Add analytical reference lines to line, bar, and column charts:

| Line Type | Description |
|---|---|
| **Constant line** | Fixed value (e.g., target line at 100,000) |
| **Min line** | Line at minimum value in current filter |
| **Max line** | Line at maximum value in current filter |
| **Average line** | Line at average of values |
| **Median line** | Line at median value |
| **Percentile line** | Line at a specified percentile |
| **Trend line** | Statistical trend line (linear regression) |
| **Forecast** | Extrapolated forecast with confidence interval |
| **Error bars** | Show variability/confidence intervals around data points |

### 3.5 Forecasting

Forecasting is available for line charts with a time-based axis:

**Configuring Forecast:**
1. Select a line chart with a Date axis
2. **Analytics pane → Forecast**
3. Configure:
   - **Forecast length** — how many periods to predict
   - **Confidence interval** — e.g., 95% (shows shaded band)
   - **Seasonality** — auto-detect or specify (in data points, e.g., 12 for monthly)
   - **Ignore last N points** — exclude recent outlier data from the model

**How it Works:** Uses exponential smoothing (ETS) algorithm internally.

> **Exam Tip:** Forecasting works only with **aggregated** time-series data. It requires the x-axis to be a date/time field, not a text field. It is not available for DirectQuery datasets.

### 3.6 Anomaly Detection

Anomaly detection automatically identifies unexpected data points in line charts:

**Configuration:**
1. Select a line chart
2. **Analytics pane → Find anomalies**
3. Set **Sensitivity** (lower = fewer anomalies detected; higher = more)
4. Set expected value range

**Anomaly Explanation:**
- Click an anomaly marker on the chart
- Power BI shows potential explanations (similar to the Analyze feature)

### 3.7 Copilot for Semantic Model Summary (January 2026 Update)

A new Copilot feature that generates a natural language summary of the semantic model:

- Available in Power BI service Fabric workspaces with Copilot enabled
- Summarises what the semantic model contains, key measures, and relationships
- Accessible via the dataset/semantic model page in the service
- Helps new users understand what data is available

> **Exam Tip:** This is distinct from the Copilot narrative visual — it summarises the **model itself** (tables, measures, relationships), not the data in a report.

### 3.8 Error Bars

Error bars communicate the uncertainty or variability of data points:

- Available for bar, column, and line charts in Analytics pane
- Can be configured as:
  - **Fixed value** — same absolute error for all points
  - **Relative value** — percentage of each data point value
  - **Measure** — use a DAX measure to compute per-point error

### 3.9 Identifying Outliers

Techniques for surfacing outliers:

| Method | How |
|---|---|
| **Scatter Chart** | Plot two measures; outliers are visually isolated from the cluster |
| **Anomaly Detection** | Automatic line chart outlier marking |
| **Conditional Formatting** | Colour-code table/matrix cells that exceed thresholds |
| **Key Influencers** | Identifies what makes a value unusually high or low |
| **Reference Lines** | Constant line shows values that exceed targets |
| **DAX STDEV + Z-Score** | Calculate Z-score measure to identify statistically significant outliers |

```dax
// Z-Score calculated column (identifies statistical outliers)
Z-Score =
VAR Mean = CALCULATE(AVERAGE(Sales[SalesAmount]), ALL(Sales))
VAR StdDev = CALCULATE(STDEV.P(Sales[SalesAmount]), ALL(Sales))
RETURN DIVIDE(Sales[SalesAmount] - Mean, StdDev, 0)
```

---

## 4. Common Exam Pitfalls

| Pitfall | Correct Understanding |
|---|---|
| Using Pie charts with many categories | Pie/Donut visuals become unreadable above 7 categories; use Bar instead |
| Forgetting to set Tooltip page type | Must enable "Allow use as tooltip" in page settings |
| Thinking Drill Through works for any field | Must be the field placed in the Drill through field well |
| Confusing Drill Down with Drill Through | Drill Down = within same visual; Drill Through = navigates to different page |
| Using Fixed interval page refresh on Import data | Page refresh only works with DirectQuery or Streaming |
| Thinking Copilot is available on all licences | Copilot requires Fabric or Premium capacity with Copilot enabled by admin |
| Forgetting Alt text is per visual | Must set on every individual visual, not at page level |
| Using a calculated column for report title | Dynamic titles must be **measures** (filter-context aware) |
| Thinking forecasting works for any axis type | Forecast requires a Date axis; text axes are not supported |
| Assuming Sync slicers auto-applies to new pages | Must manually configure each page in the Sync slicers pane |

---

## 5. Key Terms Glossary

| Term | Definition |
|---|---|
| **Bookmark** | Saved state of a report page (filters, visible visuals, etc.) |
| **Drill Through** | Navigate from summary page to detail page filtered by a value |
| **Drill Down** | Expand a hierarchy within the same visual |
| **Slicer** | Interactive filter control on a report page |
| **Sync Slicers** | Link a slicer to filter multiple pages simultaneously |
| **Tooltip page** | A dedicated report page displayed when hovering over a data point |
| **Q&A Visual** | Natural language query visual that returns auto-selected visuals |
| **Key Influencers** | AI visual identifying factors that drive a metric up or down |
| **Decomposition Tree** | Interactive visual for breaking a metric down across multiple dimensions |
| **Visual Calculation** | DAX formula applied at the visual level, not in the model |
| **Paginated Report** | Pixel-perfect, print-ready report format (.rdl) |
| **Conditional Formatting** | Dynamic colour/icon/bar formatting based on data values or rules |
| **Anomaly Detection** | Automatic identification of unexpected data points in line charts |
| **Copilot** | AI assistant for generating visuals, narratives, and report pages |
| **Narrative Visual** | Automatically generated text summary of report data |

---

## Quick Revision Summary

- **Choose the right visual:** trend = Line; part-to-whole = Pie/Donut (≤7 cats); compare = Bar/Column; geography = Map/Filled Map; exact numbers = Card; KPI = Gauge or KPI visual
- **Bookmarks** capture page state; used for navigation and storytelling
- **Drill Through** = navigate to a detail page (needs field in drill-through well)
- **Drill Down** = expand hierarchy within the same visual
- **Sync Slicers** = apply one slicer across multiple pages
- **Tooltip pages** need "Allow use as tooltip" enabled in page settings
- **Q&A** = natural language; **Key Influencers** = factor analysis; **Decomposition Tree** = ad-hoc breakdown
- **Forecasting** = line chart + date axis + Analytics pane; exponential smoothing
- **Anomaly detection** = Analytics pane → Find anomalies; sensitivity control
- **Copilot narrative** = AI text summary of data (needs Copilot-enabled capacity)
- **Copilot for report pages** = AI creates entire report pages from prompts
- **Copilot semantic model summary** = describes the model's content in natural language
- **Visual calculations** = DAX on the visual (RUNNINGSUM, COLLAPSE, PREVIOUS, RANK)
- **Conditional formatting** can use a measure that returns a hex colour string
- **Mobile layout** = View → Mobile layout; configure separately from desktop view
- **Accessibility** = Alt text, Tab order, high-contrast themes, descriptive titles
