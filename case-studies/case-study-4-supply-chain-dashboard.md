# Case Study 4: Fabrikam Manufacturing — Supply Chain Visualization & Dashboard Guide

> 📊 **Focus Area:** Visualize and Analyze the Data | Dashboard Design | Step-by-Step Instructions
> 🕐 **Estimated Study Time:** 120 minutes | **Questions:** 15 visualization-focused
> 📋 **Companion to:** [Case Study 4 – Supply Chain Analytics](case-study-4-supply-chain.md)

---

## Table of Contents

1. [Dashboard Overview](#1-dashboard-overview)
2. [Visualization Questions](#2-visualization-questions)
   - [Q1 – Supply Chain KPI Cards](#question-1--supply-chain-kpi-cards)
   - [Q2 – Inventory Value Trend Line Chart](#question-2--inventory-value-trend-line-chart)
   - [Q3 – Warehouse Map Visual with Azure Maps](#question-3--warehouse-map-visual-with-azure-maps)
   - [Q4 – Supplier Performance Scorecard Matrix](#question-4--supplier-performance-scorecard-matrix)
   - [Q5 – Order Status Waterfall Chart](#question-5--order-status-waterfall-chart)
   - [Q6 – Lead Time Distribution Column Chart](#question-6--lead-time-distribution-column-chart)
   - [Q7 – Fill Rate Gauge Visuals by Warehouse](#question-7--fill-rate-gauge-visuals-by-warehouse)
   - [Q8 – Stockout Risk Heatmap Matrix](#question-8--stockout-risk-heatmap-matrix)
   - [Q9 – Decomposition Tree for Inventory Shortfall Drivers](#question-9--decomposition-tree-for-inventory-shortfall-drivers)
   - [Q10 – Shipment Volume with Anomaly Detection and Forecasting](#question-10--shipment-volume-with-anomaly-detection-and-forecasting)
   - [Q11 – Drill-Through for Warehouse Detail Page](#question-11--drill-through-for-warehouse-detail-page)
   - [Q12 – Inventory by Category Treemap](#question-12--inventory-by-category-treemap)
   - [Q13 – Carrier Performance Horizontal Bar Chart](#question-13--carrier-performance-horizontal-bar-chart)
   - [Q14 – Bookmarks and Navigation for Regional Views](#question-14--bookmarks-and-navigation-for-regional-views)
   - [Q15 – Dynamic Report Title and Smart Narrative](#question-15--dynamic-report-title-and-smart-narrative)
3. [Complete Business Dashboard Design](#3-complete-business-dashboard-design)
4. [DAX Measures Reference](#4-dax-measures-reference)
5. [Key Exam Concepts Summary](#5-key-exam-concepts-summary)

---

## 1. Dashboard Overview

The Fabrikam Manufacturing Supply Chain dashboard provides the VP of Supply Chain, Warehouse Managers, Procurement Team, Logistics Team, and Executive Team with real-time visibility into inventory levels, supplier performance, order fulfillment, and shipment tracking across 8 warehouses in 3 regions (NA, EU, APAC).

**Dashboard pages:**

```
┌─────────────────────────────────────────────────────────────┐
│  📄 Page 1: Executive Supply Chain Overview                  │
│  KPI cards, inventory trend, warehouse map, top stockouts   │
├─────────────────────────────────────────────────────────────┤
│  📄 Page 2: Inventory & Stockout Analysis                    │
│  Heatmap, treemap, gauges, decomposition tree               │
├─────────────────────────────────────────────────────────────┤
│  📄 Page 3: Supplier & Logistics Performance                 │
│  Scorecard matrix, lead time, carrier chart, shipment trend │
└─────────────────────────────────────────────────────────────┘
```

**Target audience and access:**

| Stakeholder | Pages Used | Access Level |
|-------------|-----------|--------------|
| VP of Supply Chain | All 3 pages | Full access across all warehouses |
| Warehouse Managers (8) | Page 1, Page 2 (filtered by warehouse via RLS) | Own warehouse data only |
| Procurement Team | Page 1, Page 3 (supplier focus) | Full access |
| Logistics Team | Page 1, Page 3 (shipment focus) | Full access |
| Executive Team | Page 1 (KPI overview) | Full access, summary only |

---

## 2. Visualization Questions

### Question 1 – Supply Chain KPI Cards

**Scenario:** The VP of Supply Chain wants five headline KPI cards at the top of the Executive Overview: Total Inventory Value, Fill Rate, On-Time Delivery %, Average Lead Time, and Stockout Product Count. Each card should use conditional coloring — green when the KPI meets its target, red when it does not.

**Task:** Create five KPI card visuals with target-based conditional formatting.

#### ✅ Step-by-Step Solution

**Step 1: Create the DAX measures**

```dax
Current Inventory Value =
SUMX(
    FactInventory,
    FactInventory[QuantityOnHand] * FactInventory[UnitCost]
)
```

```dax
Total Ordered =
SUM(FactOrders[QuantityOrdered])
```

```dax
Total Fulfilled =
SUM(FactOrders[QuantityReceived])
```

```dax
Fill Rate =
DIVIDE(
    [Total Fulfilled],
    [Total Ordered],
    BLANK()
)
```

```dax
On-Time Delivery Count =
CALCULATE(
    COUNTROWS(FactOrders),
    FactOrders[ActualDeliveryDate] <= FactOrders[ExpectedDeliveryDate],
    FactOrders[OrderStatus] = "Delivered"
)
```

```dax
Total Delivered Orders =
CALCULATE(
    COUNTROWS(FactOrders),
    FactOrders[OrderStatus] = "Delivered"
)
```

```dax
On-Time Delivery % =
DIVIDE(
    [On-Time Delivery Count],
    [Total Delivered Orders],
    BLANK()
)
```

```dax
Avg Lead Time =
AVERAGE(FactOrders[Actual Lead Time Days])
```

```dax
Current Stockout Products =
VAR LatestSnapshot = MAX(FactInventory[SnapshotDateTime])
RETURN
CALCULATE(
    COUNTROWS(
        FILTER(
            FactInventory,
            FactInventory[QuantityAvailable] < FactInventory[ReorderPoint]
        )
    ),
    FactInventory[SnapshotDateTime] = LatestSnapshot
)
```

**Step 2: Create conditional color measures**

```dax
Fill Rate Color =
IF([Fill Rate] >= 0.98, "#00B050", "#FF0000")
```

```dax
On-Time Color =
IF([On-Time Delivery %] >= 0.95, "#00B050", "#FF0000")
```

```dax
Lead Time Color =
IF([Avg Lead Time] <= 14, "#00B050", "#FF0000")
```

```dax
Stockout Color =
IF([Current Stockout Products] = 0, "#00B050", "#FF0000")
```

**Step 3: Configure each card visual**

1. Insert a **Card** visual for each KPI
2. Drag the measure to the **Fields** well
3. In **Format** → **Callout value** → click **fx** (conditional formatting)
4. Set **Format style** to **Field value**
5. Select the corresponding color measure (e.g., `Fill Rate Color`)

**KPI targets summary:**

| KPI | Measure | Target | Color Logic |
|-----|---------|--------|-------------|
| Inventory Value | `[Current Inventory Value]` | Informational | Blue (static) |
| Fill Rate | `[Fill Rate]` | ≥ 98% | Green if met, Red if not |
| On-Time Delivery | `[On-Time Delivery %]` | ≥ 95% | Green if met, Red if not |
| Avg Lead Time | `[Avg Lead Time]` | ≤ 14 days | Green if met, Red if not |
| Stockout Count | `[Current Stockout Products]` | 0 | Green if 0, Red if > 0 |

> **Exam Tip:** Card visuals display single aggregate values. Use DAX measures that return color hex codes for conditional formatting via the **fx** button. This pattern separates business logic (thresholds) from visual configuration.

---

### Question 2 – Inventory Value Trend Line Chart

**Scenario:** The VP of Supply Chain wants a line chart showing monthly inventory value over the last 12 months, with a comparison line for the prior year period. The chart should highlight months where inventory value exceeded budget.

**Task:** Create a dual-line trend chart with prior year comparison and conditional reference line.

#### ✅ Step-by-Step Solution

**Step 1: Create time intelligence measures**

```dax
Inventory Value PY =
CALCULATE(
    [Current Inventory Value],
    SAMEPERIODLASTYEAR(DimDate[Date])
)
```

```dax
Inventory Value YoY Change =
[Current Inventory Value] - [Inventory Value PY]
```

```dax
Inventory Value YoY % =
DIVIDE(
    [Inventory Value YoY Change],
    [Inventory Value PY],
    BLANK()
)
```

**Step 2: Configure the line chart**

| Field Well | Value |
|-----------|-------|
| **X-axis** | DimDate[Month] (or DimDate[Date] for daily granularity) |
| **Y-axis** | `[Current Inventory Value]` |
| **Secondary Y-axis** | `[Inventory Value PY]` (optional, or add as another line on same axis) |

**Step 3: Add a constant line for budget target**

1. Select the line chart → **Analytics** pane (magnifying glass icon)
2. Click **Constant line** → **+ Add line**
3. Set value to the budget target (e.g., $40,000,000)
4. Format: dashed line, color red, label "Budget Target"

**Step 4: Add data labels for YoY change**

1. In **Format** pane → **Data labels** → toggle **On**
2. Use `[Inventory Value YoY %]` as a tooltip field to show percentage change on hover

**Step 5: Configure tooltips**

Add to the **Tooltips** well:
- `[Current Inventory Value]`
- `[Inventory Value PY]`
- `[Inventory Value YoY %]`

> **Exam Tip:** SAMEPERIODLASTYEAR shifts the date filter context back by one year. Use constant lines in the Analytics pane for budget targets or thresholds. Adding measures to the Tooltips well enriches hover information without cluttering the visual.

---

### Question 3 – Warehouse Map Visual with Azure Maps

**Scenario:** The VP of Supply Chain wants a world map showing all 8 warehouse locations. Bubble size should represent current inventory value, and bubble color should indicate inventory health (green = healthy, yellow = low stock, red = critical stockouts). Clicking a warehouse should drill through to a detail page.

**Task:** Configure an Azure Maps visual with size, color, and drill-through.

#### ✅ Step-by-Step Solution

**Step 1: Create the health score measure**

```dax
Warehouse Health Score =
VAR StockoutPct = [Stockout %]
RETURN
SWITCH(
    TRUE(),
    StockoutPct > 0.20, 1,
    StockoutPct > 0.10, 2,
    3
)
```

```dax
Stockout % =
DIVIDE(
    [Current Stockout Products],
    DISTINCTCOUNT(FactInventory[ProductID]),
    0
)
```

**Step 2: Insert and configure the Azure Maps visual**

| Field Well | Column/Measure |
|-----------|---------------|
| **Latitude** | DimWarehouse[Latitude] |
| **Longitude** | DimWarehouse[Longitude] |
| **Size** | `[Current Inventory Value]` |
| **Color** | `[Warehouse Health Score]` |
| **Tooltips** | DimWarehouse[WarehouseName], `[Total Quantity On Hand]`, `[Current Stockout Products]`, `[Stockout %]` |

**Step 3: Configure conditional color rules**

1. **Format** pane → **Bubbles** → **Color** → click **fx**
2. Set **Format style** to **Rules**
3. Define rules:
   - Value = 1 → Red (#FF0000) — Critical: >20% products in stockout
   - Value = 2 → Yellow (#FFC000) — Warning: 10–20% in stockout
   - Value = 3 → Green (#00B050) — Healthy: <10% in stockout

**Step 4: Configure drill-through**

1. Create a **Warehouse Details** page (used in Q11)
2. Add `DimWarehouse[WarehouseName]` to the **Drill-through filters** well on that page
3. Users right-click a bubble → **Drill through** → **Warehouse Details**

**Why use Latitude/Longitude instead of City/Country:**

- **Precise positioning** — City names can be ambiguous (e.g., "Portland" exists in both Oregon and Maine)
- **Faster rendering** — No geocoding API call needed
- **Offline capability** — Does not require Bing Maps geocoding service

> **Exam Tip:** Azure Maps visuals support Latitude/Longitude for precise positioning. Bubble size and color can represent different measures simultaneously. Drill-through on map visuals works the same as other visuals — configured on the target page. Use conditional formatting rules for discrete color categories (Red/Yellow/Green).

---

### Question 4 – Supplier Performance Scorecard Matrix

**Scenario:** The Procurement Team needs a matrix visual showing each supplier with their On-Time Delivery %, Quality Rating, Fill Rate, and composite Supplier Score. Cells should use conditional formatting — background color gradient from red (poor) to green (excellent).

**Task:** Create a supplier scorecard matrix with gradient conditional formatting.

#### ✅ Step-by-Step Solution

**Step 1: Create the Supplier Score measure**

```dax
Supplier Score =
VAR OnTimePct = [On-Time Delivery %]
VAR QualityRating = SELECTEDVALUE(DimSupplier[QualityRating])
VAR NormQuality = DIVIDE(QualityRating, 5, 0)
RETURN
    (OnTimePct * 0.6) + (NormQuality * 0.4)
```

**Step 2: Configure the matrix visual**

| Field Well | Value |
|-----------|-------|
| **Rows** | DimSupplier[SupplierName] |
| **Values** | `[On-Time Delivery %]`, `[Fill Rate]`, DimSupplier[QualityRating], `[Supplier Score]` |

**Step 3: Apply gradient conditional formatting**

For each value column:

1. In the **Format** pane → **Cell elements** → **Background color** → toggle **On**
2. Click **fx** (conditional formatting)
3. Set **Format style** to **Gradient**
4. Configure:
   - **Minimum**: Value = 0, Color = Red (#FF0000)
   - **Center**: Value = 0.5, Color = Yellow (#FFC000)
   - **Maximum**: Value = 1, Color = Green (#00B050)

**Step 4: Add data bars for visual comparison**

1. For `[On-Time Delivery %]` column → **Cell elements** → **Data bars** → toggle **On**
2. Set positive bar color to blue
3. This adds inline bar charts within each cell for quick visual comparison

**Step 5: Add sorting**

1. Click the `[Supplier Score]` column header to sort descending
2. Best suppliers appear at the top

**Step 6: Configure row-level detail on click**

Add to **Tooltips** well:
- `[Avg Lead Time]`
- `[Total Delivered Orders]`
- DimSupplier[Country]

> **Exam Tip:** Matrix visuals support gradient conditional formatting on individual value columns using the **fx** button. Data bars add inline charts within cells. Combining background color with data bars gives users two visual cues. The Supplier Score pattern (weighted composite) is a common business calculation tested on the exam.

---

### Question 5 – Order Status Waterfall Chart

**Scenario:** The Procurement Team wants to visualize the order lifecycle — how many orders are in each status stage (Pending → In Transit → Delivered → Cancelled) and the net flow between stages. A waterfall chart should show the breakdown of total orders by status.

**Task:** Create a waterfall chart showing order counts by status with sentiment coloring.

#### ✅ Step-by-Step Solution

**Step 1: Create order count measures by status**

```dax
Pending Orders =
CALCULATE(
    COUNTROWS(FactOrders),
    FactOrders[OrderStatus] = "Pending"
)
```

```dax
In Transit Orders =
CALCULATE(
    COUNTROWS(FactOrders),
    FactOrders[OrderStatus] = "In Transit"
)
```

```dax
Delivered Orders =
CALCULATE(
    COUNTROWS(FactOrders),
    FactOrders[OrderStatus] = "Delivered"
)
```

```dax
Cancelled Orders =
CALCULATE(
    COUNTROWS(FactOrders),
    FactOrders[OrderStatus] = "Cancelled"
)
```

```dax
Total Orders =
COUNTROWS(FactOrders)
```

**Step 2: Create a summary table for the waterfall**

Option A — Use a **Stacked Bar Chart** with OrderStatus as the axis:

| Field Well | Value |
|-----------|-------|
| **Y-axis** | FactOrders[OrderStatus] |
| **X-axis** | Count of OrderID (or `[Total Orders]` filtered per status) |

Option B — Use a **Waterfall Chart** (for net flow visualization):

| Field Well | Value |
|-----------|-------|
| **Category** | FactOrders[OrderStatus] |
| **Y-axis** | Count of FactOrders[OrderID] |

**Step 3: Configure the waterfall chart**

1. Insert a **Waterfall chart** visual
2. Drag `FactOrders[OrderStatus]` to **Category**
3. Drag `[Total Orders]` to **Y-axis**
4. In **Format** pane → **Sentiment colors**:
   - **Increase**: Green (#00B050) — Delivered orders
   - **Decrease**: Red (#FF0000) — Cancelled orders
   - **Total**: Blue (#4472C4)

**Step 4: Mark the total column**

1. Right-click the final bar → **Total** → toggle **On**
2. This shows the aggregate bar as a distinct total

**Step 5: Add data labels**

1. **Format** pane → **Data labels** → toggle **On**
2. Set position to **Outside end**
3. Display values show the exact count per status

> **Exam Tip:** Waterfall charts show how individual components contribute to a total. Sentiment colors distinguish positive (increase) and negative (decrease) contributions. Mark columns as "Total" to show aggregate bars. Waterfall charts are ideal for variance analysis and stage-based breakdowns.

---

### Question 6 – Lead Time Distribution Column Chart

**Scenario:** The Procurement Team wants to see the distribution of actual lead times across supplier regions (NA, EU, APAC) to identify which regions have the longest and most variable delivery times. The chart should show average lead time by region with error bars representing variability.

**Task:** Create a grouped column chart showing lead time distribution by supplier region.

#### ✅ Step-by-Step Solution

**Step 1: Create lead time measures**

```dax
Avg Lead Time =
AVERAGE(FactOrders[Actual Lead Time Days])
```

```dax
Min Lead Time =
MIN(FactOrders[Actual Lead Time Days])
```

```dax
Max Lead Time =
MAX(FactOrders[Actual Lead Time Days])
```

```dax
Lead Time Std Dev =
STDEVX.P(
    FactOrders,
    FactOrders[Actual Lead Time Days]
)
```

**Step 2: Configure the clustered column chart**

| Field Well | Value |
|-----------|-------|
| **X-axis** | DimSupplier[Region] |
| **Y-axis** | `[Avg Lead Time]` |
| **Legend** | DimSupplier[Category] (Raw Materials, Sub-Components, Packaging) |
| **Tooltips** | `[Min Lead Time]`, `[Max Lead Time]`, `[Lead Time Std Dev]`, `[Total Delivered Orders]` |

**Step 3: Add a reference line for target lead time**

1. Select the chart → **Analytics** pane
2. **Constant line** → **+ Add line**
3. Set value to **14** (target lead time in days)
4. Format: dashed red line, label "Target: 14 days"

**Step 4: Configure conditional formatting on columns**

1. **Format** pane → **Columns** → **Color** → click **fx**
2. Set **Format style** to **Rules**:
   - If value ≤ 14 → Green (#00B050)
   - If value > 14 and ≤ 21 → Yellow (#FFC000)
   - If value > 21 → Red (#FF0000)

**Step 5: Add data labels**

1. **Format** pane → **Data labels** → toggle **On**
2. Display format: whole number with "d" suffix (e.g., "12d")

> **Exam Tip:** Clustered column charts compare values across categories. Use the Analytics pane for reference lines (constant, average, median). Conditional formatting on columns uses rules-based coloring. Adding measures to the Tooltips well shows additional context on hover without cluttering the visual.

---

### Question 7 – Fill Rate Gauge Visuals by Warehouse

**Scenario:** The VP of Supply Chain wants to see a gauge visual for each warehouse showing its current Fill Rate against the 98% target. Each gauge should show a green zone (≥98%), yellow zone (95–98%), and red zone (<95%).

**Task:** Create gauge visuals with color-coded zones for fill rate by warehouse.

#### ✅ Step-by-Step Solution

**Step 1: Create warehouse-level fill rate measure**

```dax
Fill Rate =
DIVIDE(
    [Total Fulfilled],
    [Total Ordered],
    BLANK()
)
```

**Step 2: Create target and maximum measures**

```dax
Fill Rate Target =
0.98
```

```dax
Fill Rate Maximum =
1.0
```

**Step 3: Configure the gauge visual**

| Field Well | Value |
|-----------|-------|
| **Value** | `[Fill Rate]` |
| **Minimum value** | 0.90 (set manually in Format pane) |
| **Maximum value** | `[Fill Rate Maximum]` |
| **Target value** | `[Fill Rate Target]` |

**Step 4: Apply conditional color to the gauge fill**

1. **Format** pane → **Gauge** → **Fill** → click **fx**
2. Set **Format style** to **Rules**:
   - If value ≥ 0.98 → Green (#00B050)
   - If value ≥ 0.95 and < 0.98 → Yellow (#FFC000)
   - If value < 0.95 → Red (#FF0000)

**Step 5: Create a Small Multiples layout**

To show one gauge per warehouse:

1. Add `DimWarehouse[WarehouseName]` to the **Small multiples** field well (available in newer Power BI versions)
2. Or create a slicer with `DimWarehouse[WarehouseName]` and enable **single select** for filtering one warehouse at a time
3. Alternative: Use 8 individual gauge visuals on a grid layout, each filtered to a specific warehouse using visual-level filters

**Step 6: Add data label formatting**

1. **Format** pane → **Callout value** → **Display units** = None
2. Set decimal places to 1
3. Format as percentage

> **Exam Tip:** Gauge visuals show a value against a target and maximum. Color zones can be applied via conditional formatting rules. Small multiples split a single visual into a grid of mini-charts by a category field, allowing comparison across warehouses. Gauge visuals are commonly tested for KPI threshold scenarios.

---

### Question 8 – Stockout Risk Heatmap Matrix

**Scenario:** The Warehouse Managers need a heatmap showing stockout risk across Product Categories (rows) and Warehouses (columns). Each cell should display the count of products below reorder point, with background color intensity reflecting severity.

**Task:** Create a matrix heatmap with gradient conditional formatting.

#### ✅ Step-by-Step Solution

**Step 1: Create the stockout count measure**

```dax
Current Stockout Products =
VAR LatestSnapshot = MAX(FactInventory[SnapshotDateTime])
RETURN
CALCULATE(
    COUNTROWS(
        FILTER(
            FactInventory,
            FactInventory[QuantityAvailable] < FactInventory[ReorderPoint]
        )
    ),
    FactInventory[SnapshotDateTime] = LatestSnapshot
)
```

**Step 2: Configure the matrix visual**

| Field Well | Value |
|-----------|-------|
| **Rows** | DimProduct[ProductCategory] |
| **Columns** | DimWarehouse[WarehouseName] |
| **Values** | `[Current Stockout Products]` |

**Step 3: Apply heatmap conditional formatting**

1. **Format** pane → **Cell elements** → **Background color** → toggle **On**
2. Click **fx** (conditional formatting)
3. Set **Format style** to **Gradient**
4. Configure:
   - **Minimum**: Value = 0, Color = White (#FFFFFF)
   - **Center**: Value = 10, Color = Yellow (#FFC000)
   - **Maximum**: Value = 20+, Color = Red (#FF0000)

**Step 4: Add font color for readability**

1. **Cell elements** → **Font color** → toggle **On** → click **fx**
2. Set **Format style** to **Rules**:
   - If background is dark (value > 15) → White font
   - Otherwise → Black font

**Step 5: Add row and column totals**

1. **Format** pane → **Row subtotals** → toggle **On**
2. **Format** pane → **Column subtotals** → toggle **On**
3. Totals show aggregate stockout counts per category and per warehouse

**Step 6: Add tooltips for context**

Add to **Tooltips** well:
- `[Total Quantity On Hand]`
- `[Days of Supply]`
- `[Stockout %]`

> **Exam Tip:** Matrix visuals with gradient background color create heatmaps. Use the **fx** button for gradient or rules-based conditional formatting. Heatmaps are excellent for identifying patterns across two categorical dimensions. Configure both row and column subtotals for aggregate views. Font color conditional formatting improves readability on dark backgrounds.

---

### Question 9 – Decomposition Tree for Inventory Shortfall Drivers

**Scenario:** The VP of Supply Chain wants to understand what drives inventory shortfalls (products below reorder point). The analysis should automatically identify the most impactful factors — by Region, Warehouse, Product Category, and Supplier.

**Task:** Configure a Decomposition Tree AI visual for root cause analysis.

#### ✅ Step-by-Step Solution

**Step 1: Create the analysis measure**

```dax
Shortfall Quantity =
SUMX(
    FILTER(
        FactInventory,
        FactInventory[QuantityAvailable] < FactInventory[ReorderPoint]
    ),
    FactInventory[ReorderPoint] - FactInventory[QuantityAvailable]
)
```

**Step 2: Insert and configure the Decomposition Tree**

1. Insert a **Decomposition Tree** visual from the Visualizations pane
2. Configure:

| Field Well | Value |
|-----------|-------|
| **Analyze** | `[Shortfall Quantity]` |
| **Explain By** | DimWarehouse[Region], DimWarehouse[WarehouseName], DimProduct[ProductCategory], DimProduct[ProductSubCategory] |

**Step 3: Use AI splits**

1. Click the **+** button on the root node
2. Select **High value** — Power BI automatically splits by the dimension that contributes most to the shortfall
3. Continue drilling down: Region → Warehouse → Product Category → SubCategory
4. Each level shows the factor with the highest shortfall contribution

**Step 4: Manual exploration**

Users can also:
- Click **+** and select a specific field (e.g., DimWarehouse[Region]) for manual drill-down
- **Lock** a level to prevent it from changing during interaction
- Right-click a node → **Exclude** to remove outliers from analysis

**Example drill-down path:**

```
Shortfall Quantity: 4,500 units
  └─ Region: APAC (2,800 units — highest)
      └─ Warehouse: Singapore (1,900 units)
          └─ Category: Raw Materials (1,200 units)
              └─ SubCategory: Steel Components (800 units)
```

> **Exam Tip:** The Decomposition Tree is an AI visual that performs root cause analysis. The **Analyze** field takes a measure; **Explain By** accepts multiple dimensions. **High value** and **Low value** options use AI to find the most impactful splits. This visual requires no DAX for the AI functionality — Power BI handles the analysis automatically. Know the difference between AI splits (automatic) and manual splits (user-selected).

---

### Question 10 – Shipment Volume with Anomaly Detection and Forecasting

**Scenario:** The Logistics Team wants a line chart showing daily shipment volumes over the past 6 months. The chart should automatically flag anomalies (unusual spikes or drops) and forecast the next 30 days of expected shipment volume.

**Task:** Configure anomaly detection and forecasting on a shipment volume line chart.

#### ✅ Step-by-Step Solution

**Step 1: Create the shipment count measure**

```dax
Daily Shipment Count =
COUNTROWS(FactShipments)
```

**Step 2: Configure the line chart**

| Field Well | Value |
|-----------|-------|
| **X-axis** | DimDate[Date] |
| **Y-axis** | `[Daily Shipment Count]` |

Ensure the X-axis is set to **continuous** (not categorical) — right-click the axis → confirm Date type.

**Step 3: Enable anomaly detection**

1. Select the line chart → **Analytics** pane (magnifying glass icon)
2. Toggle **Find anomalies** → **On**
3. Configure **Sensitivity**: 75 (recommended starting point)
   - Higher sensitivity (closer to 100) = more anomalies detected, more false positives
   - Lower sensitivity (closer to 0) = fewer anomalies, only extreme outliers

**How anomaly detection works:**

- Uses **SR-CNN** (Spectral Residual and Convolutional Neural Network) algorithm
- Highlighted dots appear on the line at anomalous data points
- Clicking an anomaly opens an **Explanations pane** showing possible contributing factors

**Step 4: Enable forecasting**

1. In the same **Analytics** pane → toggle **Forecast** → **On**
2. Configure:

| Setting | Value |
|---------|-------|
| Forecast length | 30 points (30 days) |
| Confidence interval | 95% |
| Seasonality | Auto-detect |
| Ignore last | 0 points |

**How forecasting works:**

- Uses **ETS** (Exponential Smoothing) algorithm
- Detects trend (increasing/decreasing volume) and seasonality (weekly patterns)
- Displays a shaded **confidence interval** band (wider = more uncertainty)
- Forecast line extends beyond the last data point

**Step 5: Add a tooltip for anomaly context**

Add to the **Tooltips** well:
- `[Daily Shipment Count]`
- DimWarehouse[Region]
- FactShipments[ShipmentStatus]

> **Exam Tip:** Anomaly detection and forecasting are built-in AI features in the Analytics pane on line charts. They require a **continuous date axis** (not categorical). Anomaly detection uses SR-CNN; forecasting uses ETS (Exponential Smoothing). Neither requires custom DAX. The exam tests where to find these features and how to configure sensitivity and forecast length.

---

### Question 11 – Drill-Through for Warehouse Detail Page

**Scenario:** When a user right-clicks a warehouse on the map (Page 1) or the heatmap (Page 2), they should drill through to a Warehouse Detail page showing: inventory breakdown by product category, daily inventory trend, top stockout products, and warehouse metadata.

**Task:** Create and configure a drill-through detail page.

#### ✅ Step-by-Step Solution

**Step 1: Create the Warehouse Detail page**

1. Add a new page → rename to **"Warehouse Details"**
2. In the **Format** pane → **Page information** → **Page type** → leave as **Report page**

**Step 2: Configure the drill-through filter**

1. In the **Visualizations** pane → **Drill through** section
2. Drag `DimWarehouse[WarehouseName]` to the **Drill-through filters** well
3. Optionally keep **Cross-report drill-through** = Off (same report only)

**Step 3: Add visuals to the detail page**

**Layout:**

```
┌──────────────────────────────────────────────────────────────┐
│  ← Back Button    |  Warehouse: [WarehouseName]              │
├──────────┬──────────┬──────────┬──────────────────────────────┤
│ Card:    │ Card:    │ Card:    │ Card:                        │
│ On-Hand  │ Stockout │ Fill Rate│ Days of Supply               │
│ Qty      │ Count    │          │                              │
├──────────┴──────────┴──────────┴──────────────────────────────┤
│                                                               │
│  Line Chart: Daily Inventory Value (last 90 days)             │
│                                                               │
├────────────────────────────┬──────────────────────────────────┤
│  Stacked Bar:              │  Table:                          │
│  Inventory by Product      │  Top 10 Stockout Products        │
│  Category                  │  (Product, Qty, Reorder Pt, Gap) │
│                            │                                  │
└────────────────────────────┴──────────────────────────────────┘
```

**Step 4: Create a Back button**

1. Insert → **Button** → **Back**
2. Power BI automatically assigns the **Back** action
3. Format: arrow icon with "← Back to Overview" text

**Step 5: Add dynamic title**

```dax
Warehouse Detail Title =
"Warehouse Details — " & SELECTEDVALUE(DimWarehouse[WarehouseName], "All Warehouses")
```

1. Add a **Text box** or **Card** visual at the top
2. Or set the page title using: visual title → **fx** → select `[Warehouse Detail Title]`

**Step 6: Create a top stockout products table**

| Field Well | Value |
|-----------|-------|
| **Columns** | DimProduct[ProductName], `[Total Quantity On Hand]`, DimProduct[ProductCategory], ReorderPoint, `[Days of Supply]` |
| **Visual-level filter** | Inventory Health = "Below Reorder" or "Out of Stock" |

Sort by `[Days of Supply]` ascending (most critical first).

> **Exam Tip:** Drill-through is configured on the **target** page by adding a field to the Drill-through filter well. The Back button is automatically added but should be verified. Drill-through carries the filter context from the source visual. The target page updates all visuals based on the drill-through filter. This is a frequently tested concept on the PL-300 exam.

---

### Question 12 – Inventory by Category Treemap

**Scenario:** The Warehouse Managers want a treemap showing inventory value broken down by Product Category and Product SubCategory. The size of each rectangle should represent inventory value, and the color should represent inventory health.

**Task:** Create a hierarchical treemap with size and color encoding.

#### ✅ Step-by-Step Solution

**Step 1: Create the inventory health color measure**

```dax
Inventory Health Index =
VAR AvgDOS = [Days of Supply]
RETURN
SWITCH(
    TRUE(),
    ISBLANK(AvgDOS), 0,
    AvgDOS <= 7, 1,
    AvgDOS <= 14, 2,
    3
)
```

**Step 2: Configure the treemap visual**

| Field Well | Value |
|-----------|-------|
| **Category** | DimProduct[ProductCategory] |
| **Details** | DimProduct[ProductSubCategory] |
| **Values** | `[Current Inventory Value]` |
| **Tooltips** | `[Total Quantity On Hand]`, `[Days of Supply]`, `[Current Stockout Products]` |

**Step 3: Apply conditional color formatting**

1. **Format** pane → **Data colors** → click **fx**
2. Set **Format style** to **Rules**
3. Based on `[Inventory Health Index]`:
   - Value = 0 → Gray (#808080) — No data
   - Value = 1 → Red (#FF0000) — Critical (≤7 days supply)
   - Value = 2 → Yellow (#FFC000) — Low (8–14 days)
   - Value = 3 → Green (#00B050) — Healthy (>14 days)

**Step 4: Enable drill-down**

1. The hierarchy (Category → SubCategory) enables built-in drill-down
2. Click the **drill-down arrow** in the visual header to navigate from Category to SubCategory
3. Click the **expand all** icon to show both levels simultaneously

**Step 5: Add data labels**

1. **Format** pane → **Category labels** → toggle **On**
2. **Data labels** → toggle **On** to show inventory values on each rectangle

> **Exam Tip:** Treemaps display hierarchical data as proportionally sized rectangles. Adding both Category and Details creates a two-level hierarchy with built-in drill-down. Treemaps are ideal for showing part-to-whole relationships. Color encoding adds a second data dimension (e.g., health status). Know when to choose treemaps vs. pie/donut charts for the exam.

---

### Question 13 – Carrier Performance Horizontal Bar Chart

**Scenario:** The Logistics Team wants to compare carrier performance by showing average freight cost per shipment and on-time delivery rate for each carrier. The chart should be sorted by freight cost descending, with a reference line for the average across all carriers.

**Task:** Create a horizontal bar chart for carrier comparison with a dual measure and reference line.

#### ✅ Step-by-Step Solution

**Step 1: Create carrier performance measures**

```dax
Avg Freight Cost =
AVERAGE(FactShipments[FreightCost])
```

```dax
Total Shipments =
COUNTROWS(FactShipments)
```

```dax
On-Time Shipments =
CALCULATE(
    COUNTROWS(FactShipments),
    FactShipments[ShipmentStatus] = "Delivered",
    FactShipments[DeliveryDate] <= FactShipments[DeliveryDate]
)
```

```dax
Carrier On-Time % =
VAR Delivered =
    CALCULATE(
        COUNTROWS(FactShipments),
        FactShipments[ShipmentStatus] = "Delivered"
    )
VAR OnTime =
    CALCULATE(
        COUNTROWS(FactShipments),
        FactShipments[ShipmentStatus] = "Delivered",
        NOT ISBLANK(FactShipments[DeliveryDate])
    )
RETURN
    DIVIDE(OnTime, Delivered, BLANK())
```

**Step 2: Configure the bar chart**

| Field Well | Value |
|-----------|-------|
| **Y-axis** | FactShipments[CarrierName] |
| **X-axis** | `[Avg Freight Cost]` |
| **Tooltips** | `[Total Shipments]`, `[Carrier On-Time %]` |

**Step 3: Add an average reference line**

1. Select the chart → **Analytics** pane
2. **Average line** → **+ Add line**
3. Select measure: `[Avg Freight Cost]`
4. Format: dashed gray line with label "Fleet Average"

**Step 4: Apply conditional formatting on bars**

1. **Format** pane → **Bars** → **Color** → click **fx**
2. Set **Format style** to **Gradient**:
   - **Minimum** (lowest cost) → Green (#00B050)
   - **Maximum** (highest cost) → Red (#FF0000)

**Step 5: Sort by freight cost**

1. Click the **"..."** menu on the visual → **Sort by** → `[Avg Freight Cost]`
2. Set to **Sort descending** (most expensive carriers at top)

> **Exam Tip:** Horizontal bar charts are best for comparing values across categories with long labels (like carrier names). Use the Analytics pane for average or median reference lines. Gradient conditional formatting on bars helps quickly identify outliers. Sorting by the measure (not the category) provides a meaningful ranking.

---

### Question 14 – Bookmarks and Navigation for Regional Views

**Scenario:** The VP of Supply Chain frequently switches between three views: "All Regions", "NA Only", "EU Only", and "APAC Only". Instead of using slicers each time, navigation buttons should instantly switch between pre-configured views.

**Task:** Create bookmarks and navigation buttons for regional views.

#### ✅ Step-by-Step Solution

**Step 1: Create the region slicer**

1. Add a **Slicer** visual with `DimWarehouse[Region]` on Page 1
2. Set slicer type to **Dropdown** (saves space)

**Step 2: Create bookmarks for each view**

1. **View** tab → **Bookmarks** pane → **Show bookmarks pane**
2. Set the slicer to show **All Regions** → click **Add bookmark** → name: "All Regions"
3. Set the slicer to **NA** → click **Add bookmark** → name: "NA Only"
4. Set the slicer to **EU** → click **Add bookmark** → name: "EU Only"
5. Set the slicer to **APAC** → click **Add bookmark** → name: "APAC Only"

**Step 3: Configure bookmark properties**

For each bookmark, right-click → **Update**:

- ☑ **Data** — saves the slicer/filter state
- ☑ **Display** — saves visual visibility (optional)
- ☐ **Current page** — uncheck if you want the bookmark to work across pages

**Step 4: Create navigation buttons**

1. Insert → **Buttons** → **Blank** (for each region)
2. Configure each button:
   - **Format** → **Button text** → e.g., "All Regions"
   - **Action** → **Type** = Bookmark
   - **Action** → **Bookmark** = select the corresponding bookmark
3. Position buttons horizontally at the top of the page as a navigation bar

**Step 5: Format active/inactive button states**

1. For each button → **Format** → **Style**:
   - **Default state**: Light gray background, dark text
   - **On hover**: Blue background, white text
   - **On press**: Dark blue background, white text
2. This provides visual feedback on the currently active view

**Step 6: Apply bookmarks across pages**

If the slicer syncs across pages:
1. **View** → **Sync slicers** pane
2. Select the Region slicer → check sync for Pages 1, 2, and 3
3. The bookmark navigation buttons then affect all pages simultaneously

> **Exam Tip:** Bookmarks save filter state (Data) and visual state (Display). Link bookmarks to buttons via the Action property for guided analytics. Sync slicers ensure consistent filtering across pages. Bookmark properties (Data, Display, Current page) control exactly what is saved — know the difference for the exam. Navigation buttons provide a polished user experience compared to raw slicers.

---

### Question 15 – Dynamic Report Title and Smart Narrative

**Scenario:** The VP of Supply Chain wants the report title to dynamically update based on the active filters (e.g., "Supply Chain Overview — NA Region — Q4 2025"). Additionally, a Smart Narrative visual should provide an AI-generated text summary of the current data view.

**Task:** Create a dynamic title measure and configure a Smart Narrative visual.

#### ✅ Step-by-Step Solution

**Step 1: Create the dynamic title measure**

```dax
Report Title =
VAR SelectedRegion =
    IF(
        HASONEVALUE(DimWarehouse[Region]),
        SELECTEDVALUE(DimWarehouse[Region]),
        "All Regions"
    )
VAR SelectedQuarter =
    IF(
        HASONEVALUE(DimDate[Quarter]),
        "Q" & SELECTEDVALUE(DimDate[Quarter]),
        "All Quarters"
    )
VAR SelectedYear =
    IF(
        HASONEVALUE(DimDate[Year]),
        FORMAT(SELECTEDVALUE(DimDate[Year]), "0"),
        "All Years"
    )
RETURN
"Supply Chain Overview — " & SelectedRegion & " — " & SelectedQuarter & " " & SelectedYear
```

**Step 2: Apply the dynamic title to a visual**

Option A — **Card visual as title:**

1. Insert a **Card** visual at the top of the page
2. Drag `[Report Title]` to the **Fields** well
3. Format: increase font size, remove category label, set bold

Option B — **Visual title with fx:**

1. Select any visual → **Format** → **Title**
2. Click **fx** next to the Title text
3. Select **Field value** → choose `[Report Title]`

**Step 3: Create a dynamic subtitle**

```dax
Report Subtitle =
VAR InvValue = FORMAT([Current Inventory Value], "$#,0,,M")
VAR FillRateVal = FORMAT([Fill Rate], "0.0%")
VAR Stockouts = [Current Stockout Products]
RETURN
"Inventory: " & InvValue & " | Fill Rate: " & FillRateVal & " | Stockout Products: " & Stockouts
```

**Step 4: Insert and configure the Smart Narrative visual**

1. Insert a **Smart Narrative** visual from the Visualizations pane
2. Power BI automatically generates a text summary based on visible data
3. To customize:
   - Double-click the narrative text to enter edit mode
   - Click **+ Value** to insert dynamic measure references
   - Add: `[Current Inventory Value]`, `[Fill Rate]`, `[On-Time Delivery %]`, `[Current Stockout Products]`

**Step 5: Customize the narrative template**

Example customized narrative:

```
As of the latest refresh, total inventory value is +Value[Current Inventory Value].
The overall fill rate is +Value[Fill Rate], with +Value[Current Stockout Products]
products currently below their reorder point. On-time delivery stands at
+Value[On-Time Delivery %], against a target of 95%.
```

**Step 6: Position the narrative**

1. Place the Smart Narrative at the bottom of Page 1 (Executive Overview)
2. Size: full width, approximately 100px height
3. The narrative updates dynamically as slicers and filters change

> **Exam Tip:** Dynamic titles use `HASONEVALUE()` and `SELECTEDVALUE()` to detect active filters. The **fx** button on visual titles allows binding to a DAX measure. Smart Narrative visuals generate AI-powered text summaries that respond to filter context. Customize narratives with **+ Value** to insert specific measure references. Both features enhance report usability and storytelling — a key PL-300 exam topic.

---

## 3. Complete Business Dashboard Design

### Page 1: Executive Supply Chain Overview

```
┌──────────────────────────────────────────────────────────────┐
│  [Dynamic Title: Report Title measure]                       │
│  [Buttons: All Regions | NA | EU | APAC]      [Date Slicer]  │
├──────────┬──────────┬──────────┬──────────┬──────────────────┤
│ Card:    │ Card:    │ Card:    │ Card:    │ Card:            │
│Inventory │Fill Rate │On-Time % │Avg Lead  │Stockout          │
│Value     │98.5%     │94.2%     │Time 12d  │Products: 23      │
│$42.5M    │          │          │          │                  │
├──────────┴──────────┴──────────┴──────────┴──────────────────┤
│                                                               │
│  Line Chart: Inventory Value Trend (12 months + PY)           │
│                                                               │
├────────────────────────────┬──────────────────────────────────┤
│  Azure Map:                │  Horizontal Bar:                 │
│  Warehouse Locations       │  Top 10 Stockout Products        │
│  (bubble = inv value,      │  (sorted by days below reorder)  │
│   color = health)          │                                  │
├────────────────────────────┴──────────────────────────────────┤
│  Smart Narrative: AI-generated summary of current view        │
└───────────────────────────────────────────────────────────────┘
```

### Page 2: Inventory & Stockout Analysis

```
┌──────────────────────────────────────────────────────────────┐
│  [Dynamic Title]     [Category Slicer]     [Warehouse Slicer] │
├──────────────────────────────┬───────────────────────────────┤
│                              │                               │
│  Heatmap Matrix:             │  Treemap:                     │
│  Stockout Risk               │  Inventory Value by Category  │
│  (Category × Warehouse)      │  & SubCategory                │
│                              │  (color = health)             │
├──────────────────────────────┼───────────────────────────────┤
│                              │                               │
│  Gauge Visuals:              │  Decomposition Tree:          │
│  Fill Rate by Warehouse      │  Inventory Shortfall Drivers  │
│  (small multiples or grid)   │  (AI-powered root cause)      │
│                              │                               │
└──────────────────────────────┴───────────────────────────────┘
```

### Page 3: Supplier & Logistics Performance

```
┌──────────────────────────────────────────────────────────────┐
│  [Dynamic Title]     [Region Slicer]     [Supplier Slicer]    │
├──────────────────────────────────────────────────────────────┤
│                                                               │
│  Matrix: Supplier Performance Scorecard                       │
│  (SupplierName, On-Time%, Fill Rate, Quality, Score)          │
│  (gradient conditional formatting)                            │
│                                                               │
├────────────────────────────┬─────────────────────────────────┤
│  Clustered Column:         │  Horizontal Bar:                │
│  Lead Time Distribution    │  Carrier Performance            │
│  by Supplier Region        │  (Avg Freight Cost by Carrier)  │
│  (color = threshold)       │  (gradient: green→red)          │
├────────────────────────────┴─────────────────────────────────┤
│                                                               │
│  Line Chart: Daily Shipment Volume (anomaly + forecast)       │
│  (6 months history + 30-day forecast)                         │
│                                                               │
├──────────────────────────────────────────────────────────────┤
│  Waterfall: Order Status Breakdown (Pending→Transit→Done)     │
└──────────────────────────────────────────────────────────────┘
```

---

## 4. DAX Measures Reference

### KPI Measures

```dax
Current Inventory Value =
SUMX(
    FactInventory,
    FactInventory[QuantityOnHand] * FactInventory[UnitCost]
)
```

```dax
Total Quantity On Hand =
SUM(FactInventory[QuantityOnHand])
```

```dax
Latest Inventory Qty =
CALCULATE(
    SUM(FactInventory[QuantityOnHand]),
    FILTER(
        ALL(FactInventory[SnapshotDateTime]),
        FactInventory[SnapshotDateTime] = MAX(FactInventory[SnapshotDateTime])
    )
)
```

```dax
Fill Rate =
DIVIDE(
    SUM(FactOrders[QuantityReceived]),
    SUM(FactOrders[QuantityOrdered]),
    BLANK()
)
```

```dax
On-Time Delivery % =
DIVIDE(
    [On-Time Delivery Count],
    [Total Delivered Orders],
    BLANK()
)
```

```dax
Avg Lead Time =
AVERAGE(FactOrders[Actual Lead Time Days])
```

### Stockout Measures

```dax
Current Stockout Products =
VAR LatestSnapshot = MAX(FactInventory[SnapshotDateTime])
RETURN
CALCULATE(
    COUNTROWS(
        FILTER(
            FactInventory,
            FactInventory[QuantityAvailable] < FactInventory[ReorderPoint]
        )
    ),
    FactInventory[SnapshotDateTime] = LatestSnapshot
)
```

```dax
Stockout % =
DIVIDE(
    [Current Stockout Products],
    DISTINCTCOUNT(FactInventory[ProductID]),
    0
)
```

```dax
Days of Supply =
DIVIDE(
    [Total Quantity On Hand],
    [Avg Daily Usage],
    BLANK()
)
```

### Supplier and Logistics Measures

```dax
Supplier Score =
VAR OnTimePct = [On-Time Delivery %]
VAR QualityRating = SELECTEDVALUE(DimSupplier[QualityRating])
VAR NormQuality = DIVIDE(QualityRating, 5, 0)
RETURN
    (OnTimePct * 0.6) + (NormQuality * 0.4)
```

```dax
Avg Freight Cost =
AVERAGE(FactShipments[FreightCost])
```

```dax
Daily Shipment Count =
COUNTROWS(FactShipments)
```

### Conditional Formatting Helpers

```dax
Fill Rate Color =
IF([Fill Rate] >= 0.98, "#00B050", "#FF0000")
```

```dax
On-Time Color =
IF([On-Time Delivery %] >= 0.95, "#00B050", "#FF0000")
```

```dax
Lead Time Color =
IF([Avg Lead Time] <= 14, "#00B050", "#FF0000")
```

```dax
Stockout Color =
IF([Current Stockout Products] = 0, "#00B050", "#FF0000")
```

```dax
Warehouse Health Score =
VAR StockoutPct = [Stockout %]
RETURN
SWITCH(
    TRUE(),
    StockoutPct > 0.20, 1,
    StockoutPct > 0.10, 2,
    3
)
```

### Time Intelligence

```dax
Inventory Value PY =
CALCULATE(
    [Current Inventory Value],
    SAMEPERIODLASTYEAR(DimDate[Date])
)
```

```dax
Inventory Value YoY % =
DIVIDE(
    [Current Inventory Value] - [Inventory Value PY],
    [Inventory Value PY],
    BLANK()
)
```

### Dynamic Titles

```dax
Report Title =
VAR SelectedRegion =
    IF(
        HASONEVALUE(DimWarehouse[Region]),
        SELECTEDVALUE(DimWarehouse[Region]),
        "All Regions"
    )
VAR SelectedQuarter =
    IF(
        HASONEVALUE(DimDate[Quarter]),
        "Q" & SELECTEDVALUE(DimDate[Quarter]),
        "All Quarters"
    )
VAR SelectedYear =
    IF(
        HASONEVALUE(DimDate[Year]),
        FORMAT(SELECTEDVALUE(DimDate[Year]), "0"),
        "All Years"
    )
RETURN
"Supply Chain Overview — " & SelectedRegion & " — " & SelectedQuarter & " " & SelectedYear
```

```dax
Report Subtitle =
VAR InvValue = FORMAT([Current Inventory Value], "$#,0,,M")
VAR FillRateVal = FORMAT([Fill Rate], "0.0%")
VAR Stockouts = [Current Stockout Products]
RETURN
"Inventory: " & InvValue & " | Fill Rate: " & FillRateVal & " | Stockout Products: " & Stockouts
```

---

## 5. Key Exam Concepts Summary

| Concept | Domain | Key Takeaway |
|---------|--------|-------------|
| **KPI Card visuals** | Visualize | Display single metrics; use DAX color measures with **fx** for threshold-based conditional formatting |
| **Line charts with PY comparison** | Visualize | SAMEPERIODLASTYEAR shifts date context; add constant lines via Analytics pane for targets |
| **Azure Maps visual** | Visualize | Use Latitude/Longitude for precise positioning; bubble size and color for dual KPI encoding |
| **Matrix with gradient formatting** | Visualize | Supplier scorecards with gradient background + data bars; configure per-column via Cell elements |
| **Waterfall charts** | Visualize | Show component contributions to a total; sentiment colors for positive/negative; mark totals |
| **Clustered column charts** | Visualize | Compare values across categories; use Analytics pane for reference lines; conditional bar colors |
| **Gauge visuals** | Visualize | Value against target/maximum; color zones via rules-based conditional formatting |
| **Matrix heatmaps** | Visualize | Gradient background on matrix cells; two-dimensional pattern identification (Category × Warehouse) |
| **Decomposition Tree** | Visualize | AI-powered root cause analysis; Analyze + Explain By fields; High/Low value AI splits |
| **Anomaly detection** | Visualize | Built-in on line charts; Analytics pane; SR-CNN algorithm; sensitivity controls false positive rate |
| **Forecasting** | Visualize | ETS algorithm in Analytics pane; requires continuous date axis; configurable confidence interval |
| **Drill-through** | Visualize | Configured on target page; carries filter context; Back button for navigation; right-click triggered |
| **Treemaps** | Visualize | Hierarchical part-to-whole; Category + Details for drill-down; color encodes second dimension |
| **Bookmarks** | Visualize | Save Data + Display state; link to buttons via Action property; combine with synced slicers |
| **Dynamic titles & Smart Narrative** | Visualize | HASONEVALUE/SELECTEDVALUE for filter-aware titles; Smart Narrative uses AI + custom **+Value** tokens |

---

*This dashboard guide is a companion to [Case Study 4 – Supply Chain Analytics](case-study-4-supply-chain.md). Complete both for full PL-300 exam preparation.*
