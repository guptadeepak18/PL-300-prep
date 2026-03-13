# Case Study 4: Fabrikam Manufacturing — Supply Chain Analytics

> 📋 **Exam Domains Covered:** Prepare the Data | Model the Data | Visualize & Analyze | Deploy & Maintain
> 🕐 **Estimated Study Time:** 90 minutes | **Questions:** 12 scenario-based

---

## Table of Contents

1. [Company Background](#1-company-background)
2. [Data Sources and Description](#2-data-sources-and-description)
3. [Data Model Overview](#3-data-model-overview)
4. [Scenario Questions](#4-scenario-questions)
   - [Q1 – DirectQuery vs Import for Inventory Data](#question-1--directquery-vs-import-for-inventory-data)
   - [Q2 – Incremental Refresh for Order History](#question-2--incremental-refresh-for-order-history)
   - [Q3 – Cleaning and Merging Supplier Data](#question-3--cleaning-and-merging-supplier-data)
   - [Q4 – Modeling Multiple Fact Tables with Shared Dimensions](#question-4--modeling-multiple-fact-tables-with-shared-dimensions)
   - [Q5 – Inventory Turnover and Days of Supply](#question-5--inventory-turnover-and-days-of-supply)
   - [Q6 – Order Fulfillment and Lead Time Measures](#question-6--order-fulfillment-and-lead-time-measures)
   - [Q7 – Fill Rate and Stockout Analysis](#question-7--fill-rate-and-stockout-analysis)
   - [Q8 – Map Visuals for Warehouse Locations](#question-8--map-visuals-for-warehouse-locations)
   - [Q9 – Supply Chain KPI Dashboard](#question-9--supply-chain-kpi-dashboard)
   - [Q10 – Anomaly Detection and Forecasting](#question-10--anomaly-detection-and-forecasting)
   - [Q11 – Alerts and Subscriptions for KPI Thresholds](#question-11--alerts-and-subscriptions-for-kpi-thresholds)
   - [Q12 – Composite Model and Aggregations](#question-12--composite-model-and-aggregations)
5. [Key Exam Concepts Summary](#5-key-exam-concepts-summary)

---

## 1. Company Background

**Fabrikam Manufacturing** is a global manufacturer of industrial components with operations across North America, Europe, and Asia-Pacific. The company operates a complex supply chain involving raw material procurement, manufacturing, warehousing, and distribution to B2B customers.

**Key operational facts:**

- **8 warehouses** across 3 continents (3 NA, 3 EU, 2 APAC)
- **250+ suppliers** providing raw materials and sub-components
- **15,000 SKUs** (stock-keeping units) in the product catalog
- **~500,000 purchase orders** per year
- **~1.2 million shipment records** per year
- Manufacturing runs 24/7 with 3 production shifts

**Key business stakeholders:**

- **VP of Supply Chain** — Needs global visibility into inventory, orders, and supplier performance
- **Warehouse Managers (8)** — Monitor inventory levels, picking efficiency, and space utilization
- **Procurement Team** — Tracks supplier performance, lead times, and cost trends
- **Logistics Team** — Monitors shipment status, carrier performance, and delivery times
- **Executive Team** — Wants high-level KPIs and exception-based alerting

**Business pain points:**

- Inventory data is spread across multiple ERP systems (different per region)
- No unified view of supplier performance across regions
- Stockouts are causing production delays — need proactive alerts
- Manual weekly Excel reports are outdated by the time they reach leadership

---

## 2. Data Sources and Description

| Source | System | Format | Refresh Frequency | Approximate Size |
|--------|--------|--------|--------------------|------------------|
| **Inventory Snapshots** | Azure SQL Database | Relational table | **Every 15 minutes** | ~120,000 rows (current) |
| **Purchase Orders** | SAP via Azure Data Factory | Parquet files in Data Lake | Daily | ~500,000 orders/year |
| **Shipments** | Azure SQL Database | Relational table | Hourly | ~1.2 million rows/year |
| **Supplier Master** | CSV + SQL Server | Mixed sources | Monthly | ~250 rows |
| **Product Catalog (SKUs)** | SQL Server (on-premises) | Relational table | Weekly | ~15,000 rows |
| **Warehouse Directory** | Excel workbook | `.xlsx` on SharePoint | Rarely changes | 8 rows |
| **Carrier Performance** | REST API (logistics partner) | JSON | Daily | ~50,000 records/year |

### Inventory Snapshots (FactInventory)

| Column | Data Type | Description |
|--------|-----------|-------------|
| SnapshotID | Integer | Primary key |
| SnapshotDateTime | DateTime | Timestamp of the snapshot |
| WarehouseID | Text | Foreign key to DimWarehouse |
| ProductID | Text | Foreign key to DimProduct (SKU) |
| QuantityOnHand | Integer | Current stock level |
| QuantityReserved | Integer | Reserved for pending orders |
| QuantityAvailable | Integer | OnHand minus Reserved |
| ReorderPoint | Integer | Minimum stock before reorder trigger |
| UnitCost | Decimal | Current average cost per unit |

### Purchase Orders (FactOrders)

| Column | Data Type | Description |
|--------|-----------|-------------|
| OrderID | Text | Primary key (e.g., "PO-2024-001234") |
| OrderDate | Date | Date order was placed |
| ExpectedDeliveryDate | Date | Supplier-promised delivery date |
| ActualDeliveryDate | Date | Actual delivery date (null if pending) |
| SupplierID | Text | Foreign key to DimSupplier |
| ProductID | Text | Foreign key to DimProduct |
| WarehouseID | Text | Destination warehouse |
| QuantityOrdered | Integer | Units ordered |
| QuantityReceived | Integer | Units actually received |
| UnitPrice | Decimal | Price per unit |
| OrderStatus | Text | "Pending", "In Transit", "Delivered", "Cancelled" |

### Shipments (FactShipments)

| Column | Data Type | Description |
|--------|-----------|-------------|
| ShipmentID | Text | Primary key |
| OrderID | Text | Related purchase order |
| ShipDate | Date | Date shipped from supplier |
| DeliveryDate | Date | Date delivered to warehouse |
| CarrierName | Text | Logistics carrier |
| OriginCountry | Text | Supplier country |
| DestinationWarehouseID | Text | FK to DimWarehouse |
| WeightKg | Decimal | Shipment weight |
| FreightCost | Decimal | Shipping cost |
| ShipmentStatus | Text | "In Transit", "Delivered", "Delayed", "Lost" |

### Supplier Master (DimSupplier)

| Column | Data Type | Description |
|--------|-----------|-------------|
| SupplierID | Text | Primary key |
| SupplierName | Text | Company name |
| Country | Text | Country of origin |
| Region | Text | NA, EU, APAC |
| Category | Text | "Raw Materials", "Sub-Components", "Packaging" |
| LeadTimeDays | Integer | Contracted lead time in days |
| QualityRating | Decimal | Rating 1.0 - 5.0 |
| ContactEmail | Text | Primary contact |

### Product Catalog (DimProduct)

| Column | Data Type | Description |
|--------|-----------|-------------|
| ProductID | Text | Primary key (SKU code) |
| ProductName | Text | Display name |
| ProductCategory | Text | Category grouping |
| ProductSubCategory | Text | Sub-category |
| UnitOfMeasure | Text | "Each", "Box", "Pallet" |
| Weight | Decimal | Unit weight in kg |
| IsActive | Boolean | Active in catalog |

### Warehouse Directory (DimWarehouse)

| Column | Data Type | Description |
|--------|-----------|-------------|
| WarehouseID | Text | Primary key |
| WarehouseName | Text | Display name |
| City | Text | City name |
| Country | Text | Country name |
| Region | Text | NA, EU, APAC |
| Latitude | Decimal | GPS latitude |
| Longitude | Decimal | GPS longitude |
| CapacitySqFt | Integer | Total storage capacity |
| ManagerEmail | Text | Warehouse manager email |

---

## 3. Data Model Overview

This model features **three fact tables** sharing common dimensions.

```
                         ┌──────────────┐
                         │   DimDate    │
                         │──────────────│
                         │ Date (PK)    │
                         │ Year         │
                         │ Quarter      │
                         │ Month        │
                         │ WeekOfYear   │
                         │ DayOfWeek    │
                         └──────┬───────┘
                                │ 1
            ┌───────────────────┼───────────────────┐
            │                   │                   │
   ┌────────┴──────────┐ ┌─────┴────────────┐ ┌────┴──────────────┐
   │  FactInventory    │ │  FactOrders      │ │  FactShipments    │
   │───────────────────│ │──────────────────│ │───────────────────│
   │SnapshotID (PK)    │ │OrderID (PK)      │ │ShipmentID (PK)    │
   │SnapshotDateTime   │ │OrderDate         │ │ShipDate           │
   │WarehouseID (FK)   │ │ExpDeliveryDate   │ │DeliveryDate       │
   │ProductID (FK)     │ │ActDeliveryDate   │ │CarrierName        │
   │QuantityOnHand     │ │SupplierID (FK)   │ │DestWarehouseID(FK)│
   │QuantityAvailable  │ │ProductID (FK)    │ │WeightKg           │
   │ReorderPoint       │ │WarehouseID (FK)  │ │FreightCost        │
   │UnitCost           │ │QuantityOrdered   │ │ShipmentStatus     │
   └──┬─────────┬──────┘ │QuantityReceived  │ └──┬─────────┬──────┘
      │         │        │UnitPrice         │    │         │
      │         │        │OrderStatus       │    │         │
      │         │        └─┬──────┬────┬────┘    │         │
      │         │          │      │    │         │         │
      │    ┌────┴──────────┴──────┘    │    ┌────┘         │
      │    │                           │    │              │
   ┌──┴────┴────────┐  ┌──────────────┴┐  ┌┴──────────────┴──┐
   │  DimProduct    │  │ DimSupplier   │  │  DimWarehouse    │
   │────────────────│  │───────────────│  │──────────────────│
   │ProductID (PK)  │  │SupplierID(PK) │  │WarehouseID (PK)  │
   │ProductName     │  │SupplierName   │  │WarehouseName     │
   │ProductCategory │  │Country        │  │City              │
   │SubCategory     │  │Region         │  │Country           │
   │UnitOfMeasure   │  │Category       │  │Latitude          │
   │IsActive        │  │LeadTimeDays   │  │Longitude         │
   └────────────────┘  │QualityRating  │  │CapacitySqFt      │
                       └───────────────┘  │ManagerEmail      │
                                          └──────────────────┘
```

**Relationship summary:**

| From (Many) | To (One) | Key Column | Cardinality |
|-------------|----------|------------|-------------|
| FactInventory | DimDate | SnapshotDateTime (date part) → Date | Many-to-One |
| FactInventory | DimProduct | ProductID | Many-to-One |
| FactInventory | DimWarehouse | WarehouseID | Many-to-One |
| FactOrders | DimDate | OrderDate → Date | Many-to-One (active) |
| FactOrders | DimDate | ExpectedDeliveryDate → Date | Many-to-One (inactive) |
| FactOrders | DimDate | ActualDeliveryDate → Date | Many-to-One (inactive) |
| FactOrders | DimSupplier | SupplierID | Many-to-One |
| FactOrders | DimProduct | ProductID | Many-to-One |
| FactOrders | DimWarehouse | WarehouseID | Many-to-One |
| FactShipments | DimDate | ShipDate → Date | Many-to-One (active) |
| FactShipments | DimDate | DeliveryDate → Date | Many-to-One (inactive) |
| FactShipments | DimWarehouse | DestinationWarehouseID | Many-to-One |

> **Exam Tip:** When a fact table has multiple date columns (OrderDate, ExpectedDeliveryDate, ActualDeliveryDate), only one can be the active relationship to DimDate. Use `USERELATIONSHIP()` in DAX measures to activate the inactive ones when needed.

---

## 4. Scenario Questions

---

### Question 1 — DirectQuery vs Import for Inventory Data

**The inventory snapshot data refreshes every 15 minutes in Azure SQL Database. The VP of Supply Chain wants the Power BI dashboard to show near-real-time inventory levels. However, the purchase order and shipment data (refreshed daily) can tolerate some latency.**

**Should you use Import, DirectQuery, or a Composite Model? Justify your choice.**

---

#### ✅ Answer

**Recommendation: Composite Model**

Use a **composite model** that combines DirectQuery and Import:

| Table | Storage Mode | Rationale |
|-------|-------------|-----------|
| **FactInventory** | **DirectQuery** | Near-real-time data (15-min refresh); users always see current stock |
| **FactOrders** | **Import** | Daily refresh is sufficient; complex DAX measures perform better in Import |
| **FactShipments** | **Import** | Daily refresh; historical analysis benefits from in-memory speed |
| **DimProduct** | **Dual** | Referenced by both DirectQuery and Import tables |
| **DimWarehouse** | **Dual** | Referenced by both DirectQuery and Import tables |
| **DimSupplier** | **Import** | Only referenced by Import tables |
| **DimDate** | **Dual** | Referenced by all tables |

**Setting storage modes in Power BI Desktop:**

1. In Model view, select the table
2. In the **Properties** pane → **Storage mode**
3. Set to **DirectQuery**, **Import**, or **Dual**

**Key rules for composite models:**

- A **DirectQuery** fact table requires its related dimensions to be **Dual** or **DirectQuery**
- **Dual** tables store data in memory AND can serve DirectQuery queries — best for shared dimensions
- Import tables connected to DirectQuery tables via a **limited relationship** (many-to-many behavior) may show aggregate-only results

**Performance considerations:**

- DirectQuery visuals are slower (each interaction queries Azure SQL)
- Apply **aggregations** to pre-aggregate common inventory queries for faster response
- Use **query reduction** settings: enable "Apply" buttons on slicers to batch filter changes
- Set **automatic page refresh** on the inventory page (minimum interval: 30 seconds for Pro, 1 second for Premium)

**Automatic page refresh configuration:**

1. Select the report page with DirectQuery visuals
2. **Format** pane → **Page refresh** → toggle **On**
3. Set refresh type to **Auto page refresh**
4. Set interval: **5 minutes** (balances freshness vs. database load)

> **Exam Tip:** Composite models combine DirectQuery and Import tables in the same model. Shared dimensions must use **Dual** storage mode. DirectQuery enables near-real-time data but is slower per query. Automatic page refresh requires DirectQuery or a streaming dataset. Know the storage mode rules: DirectQuery → Dual → Import (direction of relationship).

---

### Question 2 — Incremental Refresh for Order History

**The FactOrders table contains 3 years of history (~1.5 million rows) and grows daily. Full refresh takes over 30 minutes. The team wants faster refreshes that only load new or changed data.**

**How do you configure incremental refresh?**

---

#### ✅ Answer

**Step 1: Create Power Query parameters**

In Power Query, create two parameters (exact names required):

| Parameter | Type | Suggested Value |
|-----------|------|-----------------|
| **RangeStart** | DateTime | 1/1/2024 12:00:00 AM |
| **RangeEnd** | DateTime | 1/2/2024 12:00:00 AM |

**Step 2: Filter the table using parameters**

```m
let
    Source = Sql.Database("fabrikam-server", "SupplyChainDB"),
    FactOrders = Source{[Schema="dbo", Item="PurchaseOrders"]}[Data],
    FilteredRows = Table.SelectRows(FactOrders, each
        [OrderDate] >= RangeStart and [OrderDate] < RangeEnd
    )
in
    FilteredRows
```

> **Important:** The filter must use `>=` for RangeStart and `<` for RangeEnd. This pattern enables query folding.

**Step 3: Configure incremental refresh policy**

1. Right-click the table in the Fields pane → **Incremental refresh**
2. Configure:

| Setting | Value |
|---------|-------|
| Archive data starting | **3 Years** before refresh date |
| Incrementally refresh data starting | **10 Days** before refresh date |
| Detect data changes | Optional — use a `LastModifiedDate` column |
| Only refresh complete periods | ✅ Enabled (avoids partial-day data) |

**How it works:**

- Power BI partitions the table by date ranges
- Historical partitions (older than 10 days) are **not refreshed**
- Recent partitions (last 10 days) are refreshed on every scheduled refresh
- New data is added to new partitions automatically
- Refresh time drops from 30+ minutes to a few minutes

**Detect data changes (optional):**

If orders can be updated after initial entry (e.g., status changes), enable change detection:

```m
// Add LastModifiedDate to the filter
FilteredRows = Table.SelectRows(FactOrders, each
    [OrderDate] >= RangeStart and [OrderDate] < RangeEnd
)
```

Set the **Detect data changes** column to `LastModifiedDate` — partitions are only refreshed if the max `LastModifiedDate` has changed.

> **Exam Tip:** Incremental refresh requires two DateTime parameters named exactly `RangeStart` and `RangeEnd`. The filter must use `>=` and `<` for query folding. Only the incremental window is refreshed, dramatically reducing refresh time. This feature works with Pro and Premium licenses but Premium supports more partitions.

---

### Question 3 — Cleaning and Merging Supplier Data

**Supplier data comes from two sources:**
- **CSV file** with NA and EU suppliers (~200 rows) — has columns SupplierID, Name, Country, Region
- **SQL Server table** with APAC suppliers (~50 rows) — has columns Supplier_Code, SupplierName, Nation, Area

**The column names don't match. You need to combine them into a single DimSupplier table.**

---

#### ✅ Answer

**Step 1: Load and rename columns in the CSV source**

```m
let
    Source = Csv.Document(File.Contents("\\share\Suppliers_NA_EU.csv"), [Delimiter=",", Encoding=65001]),
    PromotedHeaders = Table.PromoteHeaders(Source, [PromoteAllScalars=true]),
    RenamedColumns = Table.RenameColumns(PromotedHeaders, {
        {"SupplierID", "SupplierID"},
        {"Name", "SupplierName"},
        {"Country", "Country"},
        {"Region", "Region"}
    })
in
    RenamedColumns
```

**Step 2: Load and rename columns in the SQL source**

```m
let
    Source = Sql.Database("apac-server", "SupplierDB"),
    Suppliers = Source{[Schema="dbo", Item="Suppliers"]}[Data],
    RenamedColumns = Table.RenameColumns(Suppliers, {
        {"Supplier_Code", "SupplierID"},
        {"SupplierName", "SupplierName"},
        {"Nation", "Country"},
        {"Area", "Region"}
    }),
    StandardizedRegion = Table.ReplaceValue(RenamedColumns, "Asia-Pacific", "APAC",
        Replacer.ReplaceText, {"Region"})
in
    StandardizedRegion
```

**Step 3: Append the two queries**

```m
let
    Combined = Table.Combine({Suppliers_NA_EU, Suppliers_APAC}),
    SetTypes = Table.TransformColumnTypes(Combined, {
        {"SupplierID", type text},
        {"SupplierName", type text},
        {"Country", type text},
        {"Region", type text}
    }),
    RemovedDuplicates = Table.Distinct(SetTypes, {"SupplierID"})
in
    RemovedDuplicates
```

**Key distinctions: Append vs. Merge**

| Operation | Purpose | Result |
|-----------|---------|--------|
| **Append** | Stack rows from multiple tables vertically | Combined table with all rows |
| **Merge** | Join tables horizontally on a key column | Enriched table with additional columns |

In this case, **Append** is correct because both sources contain supplier records that need to be stacked into one table.

**UI approach:**

1. **Home** → **Append Queries** → **Append Queries as New**
2. Select the two supplier queries
3. Power Query creates a new combined query
4. Rename columns and standardize values as needed

> **Exam Tip:** Use **Append** to combine tables with the same structure (stacking rows). Use **Merge** (join) to combine tables with different columns on a shared key. When appending, columns must have matching names — rename them first. Always remove duplicates after appending to handle overlapping records.

---

### Question 4 — Modeling Multiple Fact Tables with Shared Dimensions

**The model has three fact tables (FactInventory, FactOrders, FactShipments) sharing DimProduct, DimWarehouse, and DimDate. FactOrders has three date columns: OrderDate, ExpectedDeliveryDate, and ActualDeliveryDate.**

**How should you configure the relationships, especially the multiple date columns?**

---

#### ✅ Answer

**Standard relationships:**

| Relationship | Type | Active |
|-------------|------|--------|
| FactInventory[SnapshotDate] → DimDate[Date] | Many-to-One | ✅ Active |
| FactInventory[ProductID] → DimProduct[ProductID] | Many-to-One | ✅ Active |
| FactInventory[WarehouseID] → DimWarehouse[WarehouseID] | Many-to-One | ✅ Active |
| FactOrders[OrderDate] → DimDate[Date] | Many-to-One | ✅ Active |
| FactOrders[ExpectedDeliveryDate] → DimDate[Date] | Many-to-One | ❌ Inactive |
| FactOrders[ActualDeliveryDate] → DimDate[Date] | Many-to-One | ❌ Inactive |
| FactOrders[SupplierID] → DimSupplier[SupplierID] | Many-to-One | ✅ Active |
| FactOrders[ProductID] → DimProduct[ProductID] | Many-to-One | ✅ Active |
| FactOrders[WarehouseID] → DimWarehouse[WarehouseID] | Many-to-One | ✅ Active |
| FactShipments[ShipDate] → DimDate[Date] | Many-to-One | ✅ Active |
| FactShipments[DeliveryDate] → DimDate[Date] | Many-to-One | ❌ Inactive |
| FactShipments[DestWarehouseID] → DimWarehouse[WarehouseID] | Many-to-One | ✅ Active |

**Using inactive relationships in DAX:**

```dax
Orders by Expected Delivery =
CALCULATE(
    COUNTROWS(FactOrders),
    USERELATIONSHIP(FactOrders[ExpectedDeliveryDate], DimDate[Date])
)
```

```dax
Orders by Actual Delivery =
CALCULATE(
    COUNTROWS(FactOrders),
    USERELATIONSHIP(FactOrders[ActualDeliveryDate], DimDate[Date])
)
```

```dax
Shipments by Delivery Date =
CALCULATE(
    COUNTROWS(FactShipments),
    USERELATIONSHIP(FactShipments[DeliveryDate], DimDate[Date])
)
```

**Alternative: Role-playing date dimensions**

Create references to DimDate for each date role:

```m
// In Power Query
DimDate_OrderDate = DimDate        // Reference
DimDate_ExpDelivery = DimDate      // Reference
DimDate_ActDelivery = DimDate      // Reference
```

Then create active relationships from each fact date column to its dedicated date reference. This avoids USERELATIONSHIP but adds more tables to the model.

> **Exam Tip:** Only one active relationship is allowed between any two tables. Additional date columns use inactive relationships activated by `USERELATIONSHIP()` inside `CALCULATE()`. Role-playing dimensions (referenced copies) are an alternative but increase model complexity. The exam tests your understanding of both approaches.

---

### Question 5 — Inventory Turnover and Days of Supply

**The procurement team needs two key inventory KPIs:**
1. **Inventory Turnover Ratio** — How many times inventory is sold and replaced in a period
2. **Days of Supply (DOS)** — How many days the current inventory will last at the current usage rate

**Write DAX measures for both.**

---

#### ✅ Answer

**Inventory Turnover Ratio:**

Formula: **Cost of Goods Sold (COGS) ÷ Average Inventory Value**

```dax
COGS =
SUMX(
    FactOrders,
    FactOrders[QuantityReceived] * FactOrders[UnitPrice]
)
```

```dax
Current Inventory Value =
SUMX(
    FactInventory,
    FactInventory[QuantityOnHand] * FactInventory[UnitCost]
)
```

```dax
Avg Inventory Value =
AVERAGEX(
    VALUES(DimDate[Month]),
    [Current Inventory Value]
)
```

```dax
Inventory Turnover =
DIVIDE(
    [COGS],
    [Avg Inventory Value],
    0
)
```

**Days of Supply (DOS):**

Formula: **(Current Inventory ÷ Average Daily Usage) = Days**

```dax
Total Quantity On Hand =
SUM(FactInventory[QuantityOnHand])
```

```dax
Avg Daily Usage =
VAR DaysInPeriod =
    COUNTROWS(VALUES(DimDate[Date]))
VAR TotalUsage =
    SUM(FactOrders[QuantityReceived])
RETURN
    DIVIDE(TotalUsage, DaysInPeriod, 1)
```

```dax
Days of Supply =
DIVIDE(
    [Total Quantity On Hand],
    [Avg Daily Usage],
    BLANK()
)
```

**For a point-in-time inventory snapshot (latest available):**

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

**Stockout risk indicator:**

```dax
Stockout Risk =
IF(
    [Days of Supply] <= 7,
    "Critical",
    IF(
        [Days of Supply] <= 14,
        "Warning",
        "Healthy"
    )
)
```

> **Exam Tip:** Inventory turnover and days of supply are classic supply chain KPIs. Use `DIVIDE()` for safe division. Point-in-time metrics (like current inventory) often need `FILTER` with `MAX` to get the latest snapshot. `AVERAGEX` over a set of periods (months) is used for calculating average inventory.

---

### Question 6 — Order Fulfillment and Lead Time Measures

**The procurement team needs to track supplier performance:**
1. **Actual Lead Time** — days between OrderDate and ActualDeliveryDate
2. **Lead Time Variance** — Actual vs. contracted lead time
3. **On-Time Delivery %** — percentage of orders delivered by ExpectedDeliveryDate
4. **Average Lead Time by Supplier**

---

#### ✅ Answer

**Calculated column for lead time (on FactOrders):**

```dax
Actual Lead Time Days =
IF(
    NOT ISBLANK(FactOrders[ActualDeliveryDate]),
    DATEDIFF(FactOrders[OrderDate], FactOrders[ActualDeliveryDate], DAY),
    BLANK()
)
```

```dax
Lead Time Variance =
FactOrders[Actual Lead Time Days] - RELATED(DimSupplier[LeadTimeDays])
```

**Measures:**

```dax
Avg Lead Time =
AVERAGE(FactOrders[Actual Lead Time Days])
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

**Late delivery analysis:**

```dax
Avg Days Late =
AVERAGEX(
    FILTER(
        FactOrders,
        FactOrders[ActualDeliveryDate] > FactOrders[ExpectedDeliveryDate]
    ),
    DATEDIFF(FactOrders[ExpectedDeliveryDate], FactOrders[ActualDeliveryDate], DAY)
)
```

**Supplier scorecard measure:**

```dax
Supplier Score =
VAR OnTimePct = [On-Time Delivery %]
VAR QualityRating = SELECTEDVALUE(DimSupplier[QualityRating])
VAR NormQuality = DIVIDE(QualityRating, 5, 0)
RETURN
    (OnTimePct * 0.6) + (NormQuality * 0.4)  // 60% on-time, 40% quality
```

> **Exam Tip:** `DATEDIFF` calculates the difference between two dates in specified intervals (DAY, MONTH, YEAR). Use `RELATED()` in calculated columns to pull values from related dimension tables. Calculated columns are appropriate here because lead time is a row-level attribute. The `FILTER` function inside `AVERAGEX` restricts the iteration to only late deliveries.

---

### Question 7 — Fill Rate and Stockout Analysis

**The VP of Supply Chain needs to monitor:**
1. **Fill Rate** — percentage of customer demand fulfilled from stock
2. **Stockout Count** — number of products below reorder point
3. **Stockout by Product Category** — which categories have the most stockouts

---

#### ✅ Answer

**Fill Rate:**

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

**Stockout Count (products below reorder point):**

```dax
Stockout Products =
COUNTROWS(
    FILTER(
        FactInventory,
        FactInventory[QuantityAvailable] < FactInventory[ReorderPoint]
    )
)
```

**For the latest snapshot only:**

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

**Stockout product list (for a table visual):**

```dax
Is Below Reorder Point =
IF(
    FactInventory[QuantityAvailable] < FactInventory[ReorderPoint],
    "Below Reorder",
    "Sufficient"
)
```

**Percentage of SKUs in stockout:**

```dax
Stockout % =
DIVIDE(
    [Current Stockout Products],
    DISTINCTCOUNT(FactInventory[ProductID]),
    0
)
```

**Inventory health classification:**

```dax
Inventory Health =
SWITCH(
    TRUE(),
    FactInventory[QuantityAvailable] = 0, "Out of Stock",
    FactInventory[QuantityAvailable] < FactInventory[ReorderPoint], "Below Reorder",
    FactInventory[QuantityAvailable] < FactInventory[ReorderPoint] * 1.5, "Low Stock",
    "Healthy"
)
```

> **Exam Tip:** Supply chain metrics often compare actual vs. target values (fill rate, on-time %). Use `FILTER` inside `COUNTROWS` to count items meeting specific conditions. For snapshot-based data (inventory), always filter to the latest snapshot for current status, or use time-based analysis for trends.

---

### Question 8 — Map Visuals for Warehouse Locations

**The VP of Supply Chain wants a map showing:**
1. All 8 warehouse locations plotted on a world map
2. Bubble size represents current inventory value
3. Bubble color indicates inventory health (green = healthy, yellow = low, red = stockout)
4. Clicking a warehouse drills through to warehouse details

**How do you configure the map visual?**

---

#### ✅ Answer

**Use the Azure Maps visual (recommended) or the built-in Map visual.**

**Configuration with the Azure Maps visual:**

1. Insert an **Azure Maps** visual (or standard **Map** visual)
2. Configure the fields:

| Field Well | Column/Measure |
|-----------|---------------|
| **Latitude** | DimWarehouse[Latitude] |
| **Longitude** | DimWarehouse[Longitude] |
| **Size** | [Current Inventory Value] measure |
| **Color** | [Warehouse Health Score] measure (for gradient) |
| **Tooltips** | DimWarehouse[WarehouseName], [Total Quantity On Hand], [Stockout Products] |

**Why use Latitude/Longitude instead of City/Country:**

- **Precise positioning** — City names may be ambiguous (e.g., "Portland" in Oregon vs. Maine)
- **Faster rendering** — No geocoding needed
- **Works offline** — Does not require Bing Maps geocoding service

**Warehouse Health Score for color:**

```dax
Warehouse Health Score =
VAR StockoutPct = [Stockout %]
RETURN
SWITCH(
    TRUE(),
    StockoutPct > 0.20, 1,    // Red: >20% products in stockout
    StockoutPct > 0.10, 2,    // Yellow: 10-20% in stockout
    3                          // Green: <10% in stockout
)
```

**Configuring conditional color on the map:**

1. In the **Format** pane → **Bubbles** → **Color**
2. Click **fx** (conditional formatting)
3. Set **Format style** to **Rules**
4. Define rules:
   - Value = 1 → Red (#FF0000)
   - Value = 2 → Yellow (#FFC000)
   - Value = 3 → Green (#00B050)

**Adding drill-through:**

1. Create a **Warehouse Details** page
2. Add `DimWarehouse[WarehouseName]` to the **Drill-through filters** well
3. Add detail visuals: inventory breakdown by product, daily trend, top stockout items
4. Right-click a bubble on the map → **Drill through** → **Warehouse Details**

**Tooltip page (alternative to drill-through):**

1. Create a new page → set **Page size** to **Tooltip** (320×240 px)
2. Toggle **Allow use as tooltip** under page settings
3. Add mini-visuals: inventory summary, stockout count
4. On the map visual → **Format** → **Tooltip** → select the tooltip page

> **Exam Tip:** Map visuals work best with Latitude/Longitude for precise positioning. Bubble size and color can represent different measures. Azure Maps provides more features than the built-in Map visual. Drill-through on maps works the same as on other visuals — configure on the target page. Tooltip pages provide a hover-based detail view.

---

### Question 9 — Supply Chain KPI Dashboard

**Design a comprehensive supply chain dashboard with the following pages:**
1. **Executive Overview** — high-level KPIs with trend indicators
2. **Inventory Status** — current stock levels with alerts
3. **Supplier Performance** — scorecard with on-time delivery and quality

**Provide the visual layout for the Executive Overview page.**

---

#### ✅ Answer

**Executive Overview Page Layout:**

```
┌──────────────────────────────────────────────────────────────┐
│  [Slicer: Region ▼]  [Slicer: Product Category ▼]  [Date]  │
├──────────┬──────────┬──────────┬──────────┬─────────────────┤
│ Card:    │ Card:    │ Card:    │ Card:    │ Card:           │
│Inventory │Fill Rate │On-Time % │Avg Lead  │Stockout         │
│Value     │98.5%     │94.2%     │Time 12d  │Products: 23     │
│$42.5M    │          │          │          │                 │
├──────────┴──────────┴──────────┴──────────┴─────────────────┤
│                                                              │
│  Line Chart: Inventory Value Trend (12 months)               │
│  ─────────────────────────────────────────────               │
│                                                              │
├────────────────────────────┬─────────────────────────────────┤
│                            │                                 │
│  Azure Map:                │  Horizontal Bar Chart:          │
│  Warehouse Locations       │  Top 10 Stockout Products       │
│  (bubble = inv value)      │  (sorted by days below reorder) │
│                            │                                 │
├────────────────────────────┼─────────────────────────────────┤
│                            │                                 │
│  Donut Chart:              │  Table:                         │
│  Inventory by Region       │  Supplier Scorecard             │
│  (NA / EU / APAC)          │  (Name, On-Time%, Quality, Scr) │
│                            │                                 │
└────────────────────────────┴─────────────────────────────────┘
```

**KPI Cards configuration:**

| KPI | Measure | Target | Conditional Color |
|-----|---------|--------|-------------------|
| Inventory Value | `[Current Inventory Value]` | Budget | Blue |
| Fill Rate | `[Fill Rate]` | ≥ 98% | Green if met, Red if not |
| On-Time Delivery | `[On-Time Delivery %]` | ≥ 95% | Green if met, Red if not |
| Avg Lead Time | `[Avg Lead Time]` | ≤ 14 days | Green if met, Red if not |
| Stockout Products | `[Current Stockout Products]` | 0 | Red if > 0 |

**Supplier Scorecard Table:**

| Supplier | On-Time % | Quality | Fill Rate | Score |
|----------|-----------|---------|-----------|-------|
| Supplier A | 97% | 4.5 | 99% | 0.94 |
| Supplier B | 91% | 4.2 | 96% | 0.87 |
| Supplier C | 85% | 3.8 | 92% | 0.79 |

**Navigation buttons for multi-page dashboard:**

1. Insert **Buttons** for "Executive Overview", "Inventory Status", "Supplier Performance"
2. Assign **Page navigation** actions to each button
3. Format active page button with a different color/underline
4. Use **bookmarks** to create saved views (e.g., "NA Only", "Critical Stockouts")

> **Exam Tip:** Multi-page dashboards should have consistent navigation. Use page navigation buttons (not tabs) for a polished user experience. Bookmarks save filter states and visual visibility — combine with buttons for guided analytics. KPI cards with conditional formatting immediately draw attention to exceptions.

---

### Question 10 — Anomaly Detection and Forecasting

**The logistics team wants to:**
1. Detect anomalies in daily shipment volumes (unusual spikes or drops)
2. Forecast expected shipment volumes for the next 30 days

**Which Power BI features should you use?**

---

#### ✅ Answer

**Anomaly Detection:**

1. Create a **Line Chart** with:
   - X-axis: `DimDate[Date]`
   - Y-axis: `[Daily Shipment Count]` measure

```dax
Daily Shipment Count =
COUNTROWS(FactShipments)
```

2. In the **Analytics** pane of the line chart → toggle **Find anomalies** → **On**
3. Configure sensitivity (0-100):
   - **Higher sensitivity** = more anomalies detected (more false positives)
   - **Lower sensitivity** = fewer anomalies detected (only extreme outliers)
   - Recommended starting point: **75**

**How anomaly detection works:**

- Power BI uses **SR-CNN** (Spectral Residual and Convolutional Neural Network) algorithm
- It identifies data points that deviate significantly from expected patterns
- Anomalies appear as **highlighted dots** on the line chart
- Clicking an anomaly opens an **explanation pane** showing possible contributing factors

**Forecasting:**

1. On the same line chart → **Analytics** pane → toggle **Forecast** → **On**
2. Configure:

| Setting | Value | Description |
|---------|-------|-------------|
| Forecast length | 30 points | 30 days of forecast |
| Confidence interval | 95% | Shaded area showing prediction range |
| Seasonality | Auto-detect | Let Power BI detect weekly/monthly patterns |
| Ignore last | 0 points | Include all data in the model |

**The forecast uses exponential smoothing (ETS):**

- Automatically detects **trend** (increasing/decreasing shipment volumes)
- Detects **seasonality** (weekly patterns — lower weekends, higher mid-week)
- Displays a **confidence interval** band (wider = more uncertainty)

**Creating an anomaly alert measure:**

```dax
Shipment Volume Anomaly Flag =
VAR AvgVolume =
    AVERAGEX(
        DATESINPERIOD(DimDate[Date], MAX(DimDate[Date]), -30, DAY),
        [Daily Shipment Count]
    )
VAR StdDev =
    STDEVX.P(
        DATESINPERIOD(DimDate[Date], MAX(DimDate[Date]), -30, DAY),
        [Daily Shipment Count]
    )
VAR CurrentVolume = [Daily Shipment Count]
RETURN
IF(
    ABS(CurrentVolume - AvgVolume) > 2 * StdDev,
    "Anomaly",
    "Normal"
)
```

> **Exam Tip:** Anomaly detection is a built-in AI feature on line charts in the Analytics pane. It uses the SR-CNN algorithm and does not require any DAX. Forecasting uses ETS (Exponential Smoothing) and also appears in the Analytics pane. Both features require a continuous date axis on the X-axis. The exam tests your knowledge of where to find these features and how to configure them.

---

### Question 11 — Alerts and Subscriptions for KPI Thresholds

**The VP of Supply Chain wants to be notified when:**
1. **Fill Rate** drops below 95%
2. **Stockout Products** count exceeds 30
3. **On-Time Delivery %** drops below 90%

**How do you configure alerts and distribute reports?**

---

#### ✅ Answer

**Step 1: Pin KPI tiles to a dashboard**

1. In the published report, hover over each **Card** visual
2. Click the **pin icon** → pin to a dashboard named **"Supply Chain Alerts"**
3. Pin the Fill Rate, Stockout Products, and On-Time Delivery cards

**Step 2: Configure data-driven alerts**

For each pinned tile:

1. Hover over the tile → click **"..."** → **Manage alerts**
2. Click **+ Add alert rule**

| Alert | Condition | Threshold | Frequency |
|-------|-----------|-----------|-----------|
| Fill Rate | Below | 0.95 | At most every hour |
| Stockout Products | Above | 30 | At most every hour |
| On-Time Delivery | Below | 0.90 | At most every 24 hours |

3. Enable **Email notification** for each alert

**Step 3: Configure Power Automate flow for advanced notifications**

For notifications to multiple people or Teams channels:

1. Create a Power Automate flow
2. Trigger: **"When a data-driven alert is triggered"**
3. Select the alert from Power BI
4. Add actions:

```
Trigger: Power BI alert triggered
  → Action: Post message to Teams channel
      Channel: "Supply Chain Alerts"
      Message: "⚠️ Alert: {Alert Title} - Current value: {Tile Value}"
  → Action: Send email (V2)
      To: vp_supply@fabrikam.com; procurement@fabrikam.com
      Subject: "Supply Chain Alert: {Alert Title}"
      Body: "The {Alert Title} threshold has been breached. Current value: {Tile Value}."
```

**Step 4: Configure report subscriptions**

1. Open the report in the Power BI service
2. Click **Subscribe** → **Subscribe to this report**
3. Configure:

| Setting | Value |
|---------|-------|
| Recipients | VP, Warehouse Managers |
| Frequency | Daily at 7:00 AM |
| Start date | Immediately |
| Include | Screenshot of the report page |
| Subject | "Daily Supply Chain Report - {Date}" |

**Subscription for paginated reports:**

If a paginated report version exists, subscriptions can attach:
- PDF
- Excel
- Word
- PowerPoint

**Limitations to communicate:**

| Feature | Limitation |
|---------|-----------|
| Data alerts | Only on dashboard tiles (cards, gauges, KPIs) |
| Alert checks | Triggered on data refresh, not continuously |
| Subscriptions | Screenshot-based for Power BI reports; attachment for paginated |
| Recipient limits | Up to 24 email addresses per subscription |

> **Exam Tip:** Data-driven alerts work only on dashboard tiles (not report visuals). Alerts are evaluated when data refreshes. Power Automate extends alerting to Teams, multiple recipients, and custom workflows. Report subscriptions deliver screenshots (Power BI reports) or attachments (paginated reports). Know these limitations for the exam.

---

### Question 12 — Composite Model and Aggregations

**The FactShipments table has 1.2 million rows per year (3.6M total). DirectQuery queries on this table are slow when aggregating shipment counts by month and region. Users frequently need both summary views and detail-level drill-through.**

**How can you use aggregations to improve performance while maintaining detail access?**

---

#### ✅ Answer

**Aggregation tables pre-compute common aggregations for faster queries.**

**Step 1: Create an aggregation table in Power Query**

```m
let
    Source = FactShipments,
    GroupedRows = Table.Group(Source, {
        "ShipYear", "ShipMonth", "DestinationWarehouseID", "CarrierName", "ShipmentStatus"
    }, {
        {"ShipmentCount", each Table.RowCount(_), Int64.Type},
        {"TotalFreightCost", each List.Sum([FreightCost]), type number},
        {"TotalWeightKg", each List.Sum([WeightKg]), type number},
        {"AvgFreightCost", each List.Average([FreightCost]), type number}
    })
in
    GroupedRows
```

Name this table: **FactShipments_Agg**

**Step 2: Set storage modes**

| Table | Storage Mode |
|-------|-------------|
| FactShipments | **DirectQuery** (detail data, queried on demand) |
| FactShipments_Agg | **Import** (pre-aggregated, fast in-memory) |
| DimWarehouse | **Dual** (serves both) |
| DimDate | **Dual** (serves both) |

**Step 3: Configure aggregation mappings**

1. In Model view → right-click **FactShipments_Agg** → **Manage aggregations**
2. Map columns:

| Aggregation Column | Summarization | Detail Table | Detail Column |
|-------------------|---------------|-------------|---------------|
| ShipmentCount | Count | FactShipments | ShipmentID |
| TotalFreightCost | Sum | FactShipments | FreightCost |
| TotalWeightKg | Sum | FactShipments | WeightKg |
| DestinationWarehouseID | GroupBy | FactShipments | DestinationWarehouseID |
| ShipYear | GroupBy | FactShipments | (year from ShipDate) |
| ShipMonth | GroupBy | FactShipments | (month from ShipDate) |
| CarrierName | GroupBy | FactShipments | CarrierName |
| ShipmentStatus | GroupBy | FactShipments | ShipmentStatus |

**Step 4: Hide the aggregation table**

Right-click **FactShipments_Agg** → **Hide in report view**. Users never interact with it directly.

**How it works:**

1. When a visual requests **shipment count by month and region** → Power BI checks the aggregation table → hit found → returns from Import (fast)
2. When a visual requests **individual shipment details** → no aggregation match → falls back to DirectQuery on FactShipments (slower but accurate)
3. The query engine automatically decides — no DAX changes needed

**Monitoring aggregation hits:**

Use **Performance Analyzer** in Power BI Desktop:

1. **View** → **Performance Analyzer** → **Start recording**
2. Interact with visuals
3. Check the **DAX query** results — if the aggregation was used, the query plan references the Import table

**Example performance improvement:**

| Query | Without Aggregation | With Aggregation |
|-------|-------------------|------------------|
| Monthly shipment count by region | ~8 seconds (DirectQuery) | ~0.3 seconds (Import) |
| Shipment detail for one month | ~3 seconds (DirectQuery) | ~3 seconds (DirectQuery fallback) |

> **Exam Tip:** Aggregation tables provide Import-mode speed for summary queries while preserving DirectQuery access to detail data. Configure them via **Manage Aggregations** on the aggregated table. The aggregation table should be hidden from report view. Power BI's query engine automatically matches queries to aggregations — no measure changes needed. This is a Premium/PPU feature.

---

## 5. Key Exam Concepts Summary

| Concept | Domain | Key Takeaway |
|---------|--------|-------------|
| **Composite Models** | Prepare Data | Combine DirectQuery (real-time) and Import (performance) in one model |
| **Dual storage mode** | Prepare Data | Shared dimensions must be Dual when referenced by both DQ and Import tables |
| **Incremental refresh** | Prepare Data | RangeStart/RangeEnd parameters; only refreshes recent partitions |
| **Append vs. Merge** | Prepare Data | Append stacks rows vertically; Merge joins horizontally on a key |
| **Multiple date relationships** | Model Data | One active, others inactive; use USERELATIONSHIP() in CALCULATE |
| **Role-playing dimensions** | Model Data | Alternative to inactive relationships — separate references to same table |
| **Inventory KPIs** | Model Data | Turnover = COGS ÷ Avg Inventory; DOS = Inventory ÷ Daily Usage |
| **DATEDIFF** | Model Data | Calculates date differences; used for lead time and delivery metrics |
| **FILTER in COUNTROWS** | Model Data | Count rows meeting specific conditions (stockouts, late deliveries) |
| **Map visuals** | Visualize | Use Latitude/Longitude for precision; bubble size and color for KPIs |
| **Anomaly detection** | Visualize | Built-in AI on line charts; Analytics pane; SR-CNN algorithm |
| **Forecasting** | Visualize | ETS in Analytics pane; requires continuous date axis |
| **Data-driven alerts** | Deploy & Maintain | Dashboard tiles only; triggered on refresh; Power Automate extends |
| **Aggregation tables** | Deploy & Maintain | Pre-computed summaries; Import for speed; DQ fallback for details |
| **Automatic page refresh** | Deploy & Maintain | Requires DirectQuery; minimum intervals vary by license tier |
