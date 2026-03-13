# PL-300 Full Mock Exam — 60-Question Simulation

**⏱ Time Allowed:** 120 minutes  
**Questions:** 60 (multiple choice, single answer unless noted)  
**Passing Score:** ~700/1000 (approximately 44/60)

**Instructions:** Answer all 60 questions before checking the Answer Key at the bottom. Simulate real exam conditions — no notes, no lookups.

Domain breakdown:
- Q1–Q15: Prepare the Data (~25%)
- Q16–Q33: Model the Data (~30%)
- Q34–Q51: Visualize and Analyze (~30%)
- Q52–Q60: Manage and Secure (~15%)

---

## Q1

**You connect Power BI Desktop to an Azure SQL Database and load a 50-million-row fact table. Query performance in reports is very slow. Which storage mode change would MOST improve interactive query performance without requiring a gateway?**

A) Switch to DirectQuery mode  
B) Switch to Import mode  
C) Switch to Dual mode  
D) Enable incremental refresh on the table  

---

## Q2

**In Power Query Editor, you have a column called "Revenue" that contains some null values. You want to replace all nulls with 0. Which transformation should you use?**

A) Remove rows with nulls  
B) Replace values — replace null with 0  
C) Fill down  
D) Change the column data type to Whole Number  

---

## Q3

**You are combining data from three regional Excel files (North.xlsx, South.xlsx, East.xlsx) that all have identical column structures. Which Power Query feature is MOST efficient?**

A) Merge queries (left outer join)  
B) Append queries  
C) Use the "Combine files" feature from a folder connector  
D) Import each file separately and create relationships  

---

## Q4

**A date column in your source data is formatted as "20240315" (YYYYMMDD as text). You need it as a proper Date type. What is the correct Power Query approach?**

A) Change data type directly to Date  
B) Use Text.Middle and Text.Start to split, then combine and convert  
C) Use Date.FromText after formatting with Text.Insert to add separators  
D) Create a calculated column in DAX using DATEVALUE()  

---

## Q5

**You want to prevent report consumers from seeing certain Power Query transformation steps when they view the query in Power BI Desktop. Which feature enables this?**

A) Lock the .pbix file with a password  
B) Enable query folding on sensitive steps  
C) Mark the query steps as private using the Query privacy level  
D) Enable "Hide in report view" on the query  

---

## Q6

**Which Power Query step type ensures that transformations are pushed back to the source database as SQL, reducing data transfer volume?**

A) Query folding  
B) Query caching  
C) Native query  
D) Direct lake  

---

## Q7

**You have a CSV file where the first row contains metadata (not column headers) and the second row contains the actual headers. How should you handle this in Power Query?**

A) Use "Use first row as headers" immediately  
B) Remove the first row, then use "Use first row as headers"  
C) Skip rows in the connector settings  
D) Both B and C are valid approaches  

---

## Q8

**A "Sales" table has columns: OrderID, CustomerID, ProductID, Amount, OrderDate. A "Customers" table has: CustomerID, CustomerName, Region. To combine them so each sales row includes the customer's Region, which Power Query operation is correct?**

A) Append queries — Sales appended with Customers  
B) Merge queries — Sales left outer joined to Customers on CustomerID  
C) Merge queries — Sales full outer joined to Customers on CustomerID  
D) Group by CustomerID in the Sales table  

---

## Q9

**You need to unpivot a table that has sales amounts as columns for each month (Jan, Feb, Mar...) into rows with Month and Amount columns. Which transformation should you use?**

A) Pivot column  
B) Unpivot columns  
C) Transpose  
D) Group by  

---

## Q10

**What does setting a data source privacy level to "Organizational" mean in Power Query?**

A) The data source is accessible to all internet users  
B) The data source can be combined with other Organizational or Public sources but not Private ones  
C) The data source can only be combined with other Organizational sources  
D) The data source cannot be combined with any other sources  

---

## Q11

**Which of the following is a benefit of enabling query folding in Power Query?**

A) Reduces the number of applied steps shown in the query editor  
B) Pushes data transformations to the source system, reducing data transferred to Power BI  
C) Enables incremental refresh automatically  
D) Both B and C are correct  

---

## Q12

**You have a parameter in Power Query called "ServerName" used in a data source connection. In Power BI Service, how can this parameter be changed without republishing from Desktop?**

A) Edit the parameter in the Power BI Service semantic model settings  
B) Parameters cannot be changed in the Service; must be edited in Desktop and republished  
C) Use a deployment rule in a deployment pipeline  
D) Both A and C can change parameter values in different scenarios  

---

## Q13

**A column contains values like "  New York  " (with leading/trailing spaces). Which Power Query transformation cleans this?**

A) Replace values  
B) Trim  
C) Clean  
D) Lowercase  

---

## Q14

**You want to profile a large dataset in Power Query to see the distribution of values, count of distinct values, and identify errors without loading all rows. Which Power Query feature helps?**

A) Query diagnostics  
B) Column distribution and Column profile (column profiling)  
C) Data preview refresh  
D) Applied steps pane  

---

## Q15

**Which of the following data connectors in Power BI supports DirectQuery mode? (Select the BEST answer)**

A) Excel (.xlsx)  
B) Azure SQL Database  
C) SharePoint List  
D) JSON file  

---

## Q16

**In Power BI, what is a "star schema"?**

A) A data model with a single table containing all data  
B) A data model with one or more fact tables connected to multiple dimension tables  
C) A schema used only in Azure Synapse  
D) A schema where every table is connected to every other table  

---

## Q17

**You have a Sales fact table and a Date dimension table. The Date table has a continuous range of dates with no gaps. What property must you set on the Date table in Power BI?**

A) Set the table as a "dimension table" in the model view  
B) Mark the table as a Date table and specify the date column  
C) Set the date column's data type to Date/Time  
D) Enable auto date/time for the date column  

---

## Q18

**What is the difference between a calculated column and a measure in Power BI DAX?**

A) Calculated columns are evaluated at query time; measures are stored in the model  
B) Calculated columns are stored in the model and computed row-by-row; measures are computed dynamically at query time in filter context  
C) Measures can use ALL DAX functions; calculated columns cannot use aggregation functions  
D) There is no difference — they are interchangeable  

---

## Q19

**What does the ALL() function do in a DAX measure?**

A) Returns all rows in a table, ignoring all filter context  
B) Aggregates all values in a column with no filter  
C) Marks a table as the "all data" source  
D) Returns only non-blank values  

---

## Q20

**You write this measure: `% of Total = DIVIDE([Sales], CALCULATE([Sales], ALL(Sales)))`. What does it calculate?**

A) The average sales per row  
B) Each category's sales as a percentage of total sales, ignoring all filters  
C) Sales for all years combined  
D) The year-over-year percentage change  

---

## Q21

**You need a measure that calculates sales for the same period in the previous year. Which DAX function is MOST appropriate?**

A) PREVIOUSYEAR()  
B) SAMEPERIODLASTYEAR()  
C) DATEADD([Date], -1, YEAR)  
D) Both B and C are correct  

---

## Q22

**What is the purpose of CALCULATE() in DAX?**

A) To perform mathematical calculations within a table  
B) To evaluate an expression in a modified filter context  
C) To create a new calculated table  
D) To loop through each row and calculate a value  

---

## Q23

**A relationship between Sales[ProductID] and Products[ProductID] is set to "Both" cross-filter direction. What risk does this introduce?**

A) The relationship becomes inactive  
B) Potential circular ambiguity in filter propagation, which can cause incorrect results  
C) DirectQuery stops working on the tables  
D) Both tables are merged into one  

---

## Q24

**You have two tables related by a many-to-many relationship: Products and Promotions (each product can have many promotions, each promotion applies to many products). How should this be modeled in Power BI?**

A) Create a direct many-to-many relationship with cross-filter Both  
B) Create a bridge/junction table (ProductPromotion) and connect it to both tables  
C) Merge the tables into one flat table  
D) Use a calculated table to flatten the relationship  

---

## Q25

**What does the RELATED() function do in a DAX calculated column?**

A) Returns the sum of a related table's column  
B) Returns a value from the "one" side of a relationship for the current row  
C) Creates a new relationship between two tables  
D) Returns all related rows from a table  

---

## Q26

**You need to calculate a running total of sales by date. Which DAX pattern is CORRECT?**

A) `Running Total = SUMX(Sales, [Sales Amount])`  
B) `Running Total = CALCULATE([Sales Amount], DATESYTD(Date[Date]))`  
C) `Running Total = CALCULATE([Sales Amount], FILTER(ALL(Date), Date[Date] <= MAX(Date[Date])))`  
D) Both B and C compute running totals (B for YTD, C for all-time running total)  

---

## Q27

**A calculation group in Power BI Tabular is used for which purpose?**

A) To group calculated columns together for display in the Fields pane  
B) To apply a set of time intelligence or format calculations to multiple measures dynamically  
C) To create security groups for RLS  
D) To organize tables into display folders  

---

## Q28

**You want to prevent a column called "Internal Notes" from being visible to report authors in Power BI Desktop, while keeping it available for use in DAX. What should you do?**

A) Delete the column from the model  
B) Set the column as "Hidden" in Model view  
C) Apply Object-Level Security to hide it  
D) Move the column to a separate hidden table  

---

## Q29

**What is a composite model in Power BI?**

A) A model that combines Import and DirectQuery tables, or multiple DirectQuery sources  
B) A model created from multiple .pbix files  
C) A model that uses calculation groups and measures together  
D) A model with more than 100 tables  

---

## Q30

**The USERELATIONSHIP() function in DAX is used for what purpose?**

A) To create a new relationship between two tables at runtime  
B) To activate an inactive relationship for the duration of a measure's evaluation  
C) To define the direction of cross-filtering in a relationship  
D) To override RLS within a DAX calculation  

---

## Q31

**You have a Date table with a "Fiscal Year" hierarchy. Your measure `FY Sales = CALCULATE([Sales], DATESYTD(Date[Date], "6/30"))` correctly calculates fiscal year-to-date with a June 30 fiscal year end. What does the second argument "6/30" represent?**

A) The fiscal year start date  
B) The fiscal year end date  
C) A filter to exclude June 30 data  
D) The number of months offset  

---

## Q32

**Which of the following statements about RANKX() is CORRECT?**

A) RANKX always returns a unique rank; no ties are possible  
B) RANKX requires a table argument and an expression, and can produce ties with optional dense/skip ranking  
C) RANKX can only rank numeric columns in a table  
D) RANKX is evaluated row-by-row and does not require filter context  

---

## Q33

**What is the purpose of the TREATAS() function in DAX?**

A) To filter a tree hierarchy by a specific level  
B) To apply a table's values as filter context on an unrelated table, treating it as if they were related  
C) To create a virtual calculated table  
D) To activate a relationship based on a condition  

---

## Q34

**You need to show market share of each product as a percentage of all products' total sales. The bar chart should show 100% height for every category bar but the color segments show proportional share. Which chart type should you use?**

A) Clustered bar chart  
B) Stacked bar chart  
C) 100% stacked bar chart  
D) Waterfall chart  

---

## Q35

**A report has a slicer on Page 1 that needs to control visuals on Pages 1, 2, and 4 but NOT Page 3. Which feature enables this selective cross-page filtering?**

A) Report-level filters  
B) Edit interactions  
C) Sync slicers with page-specific sync settings  
D) Drill-through filters  

---

## Q36

**You want users to be able to click a button labeled "Show Details" that reveals a hidden table visual and hides a summary card visual. Which combination of features enables this?**

A) Drill-through + bookmarks  
B) Bookmarks + Selection pane + Buttons with bookmark actions  
C) Edit interactions + page navigation  
D) Slicers + conditional formatting  

---

## Q37

**Which visual type is BEST for exploring the root cause of an unexpectedly low KPI by breaking it down across multiple dimensions interactively?**

A) Q&A visual  
B) Key Influencers  
C) Decomposition Tree  
D) Scatter plot with clustering  

---

## Q38

**A user wants to add a projected trend for the next quarter to a line chart. Which Power BI Analytics pane feature should they use?**

A) Average line  
B) Trend line  
C) Forecast  
D) Constant line  

---

## Q39

**Which of the following is TRUE about custom tooltip pages in Power BI?**

A) Tooltip pages count toward the report page limit  
B) Tooltip pages must be hidden to function as tooltips  
C) Tooltip pages can contain any visuals including sparklines and images  
D) Both B and C are correct  

---

## Q40

**A conditional formatting rule applies a red font color when a value is below 0, yellow between 0 and 100, and green above 100. A cell shows the value -5. What color will the font be?**

A) Green  
B) Yellow  
C) Red  
D) Black (no rule matches)  

---

## Q41

**Arrange the following steps in the correct order to configure drill-through in a Power BI report:**

A) Add fields to the visual on the source page → Create a drill-through target page → Add a drill-through field to the target page's Drill-through well → Right-click a data point on the source page  
B) Create a drill-through target page → Add a drill-through field to the target page's Drill-through well → Add fields to the visual on the source page → Right-click a data point on the source page  
C) Right-click a data point → Add a drill-through page → Add the field to the drill-through well  
D) Add the field to the source page → Add the field to the report filter → Enable drill-through in visual settings  

**✅ Note: This is a sequencing question — choose the option with the correct order.**

A) Option A  
B) Option B  
C) Option C  
D) Option D  

---

## Q42

**Which of the following is NOT a capability of the Q&A visual in Power BI?**

A) Accepting natural language questions typed by the user  
B) Generating a visual automatically based on the question  
C) Training the Q&A engine with synonyms for field names  
D) Applying Row-Level Security to limit which data the Q&A visual can access  

---

## Q43

**You want to add automatic page refresh every 15 minutes to a report page connected to a DirectQuery source. Where is this configured?**

A) In the semantic model's scheduled refresh settings in Power BI Service  
B) In the report page's Format settings under Page information > Page refresh  
C) In the Power BI admin portal > Refresh settings  
D) In Power Query Editor > Query settings  

---

## Q44

**A scatter chart in Power BI has Sales on the X-axis, Profit on the Y-axis, and Region as the Legend. What additional field would enable animated playback of how the relationship changes over time?**

A) A date/time field placed in the "Details" well  
B) A date/time field placed in the "Play axis" well  
C) A date field used as a visual filter  
D) A calculated column for year placed in the Tooltips well  

---

## Q45

**Which feature in Power BI allows a report author to configure the report so that each end user can personally change visual types and add fields to visuals without needing edit access?**

A) Personal bookmarks  
B) Personalize visuals  
C) Edit interactions  
D) Report themes  

---

## Q46

**You need to create a report that exports 10,000+ rows of transactional data in a precisely formatted table, spanning multiple pages, with page numbers and headers/footers on each page. Which type of report should you create?**

A) Standard Power BI report  
B) Paginated report (.rdl)  
C) Dashboard with table tiles  
D) Power BI Datamart report  

---

## Q47

**An anomaly is detected in a line chart using Power BI's anomaly detection. A user clicks on the anomaly marker. What does Power BI display?**

A) A decomposition tree showing contributing factors to the anomaly  
B) An explanation panel showing which fields may explain the anomaly  
C) A Key Influencers visual automatically generated for the anomaly date  
D) A data alert notification sent to the user's email  

---

## Q48

**You have a matrix visual with a row hierarchy: Year > Quarter > Month. A user clicks the expand arrow on "2024." What happens?**

A) The report navigates to a new page showing 2024 data  
B) The 2024 row expands to show Q1, Q2, Q3, Q4 sub-rows (drill-down)  
C) A tooltip appears with 2024 quarterly details  
D) A bookmark is triggered showing 2024 filters  

---

## Q49

**Which of the following statements about the Selection pane is CORRECT?**

A) The Selection pane is only available in Power BI Service, not Desktop  
B) Objects listed higher in the Selection pane are rendered behind objects lower in the list  
C) The Selection pane allows you to set the tab order for keyboard accessibility  
D) The Selection pane only shows slicers and filter visuals  

---

## Q50

**You are designing a mobile layout in Power BI. Which statement is TRUE?**

A) All visuals on the desktop layout are automatically arranged for mobile  
B) You must re-create all visuals from scratch for the mobile layout  
C) The mobile layout lets you select which existing visuals to include and rearrange them for a phone screen  
D) Mobile layouts require a Power BI Premium license  

---

## Q51

**Match the following visual types to their BEST use case. Which answer correctly matches ALL four?**

A) Waterfall → Show proportion of total; Treemap → Show sequential change; Funnel → Show trends over time; Line chart → Show pipeline stages  
B) Waterfall → Show sequential increase/decrease to a net result; Treemap → Show proportional hierarchy; Funnel → Show progressive stage reduction; Line chart → Show trends over time  
C) Waterfall → Show trends; Treemap → Show funnel stages; Funnel → Show proportional data; Line chart → Show net changes  
D) All four visuals serve the same purpose and are interchangeable  

---

## Q52

**A new employee joins the Sales team in the East region. Your dynamic RLS implementation uses a SecurityTable with [Email] and [Region] columns. What must you do to grant this user the correct data access?**

A) Create a new RLS role for the new employee  
B) Add the user's email and region mapping to the SecurityTable in the data source  
C) Add the user to the workspace as a Contributor  
D) Re-publish the semantic model from Power BI Desktop  

---

## Q53

**Workspace role permissions — which of the following is CORRECT?**

A) Contributors can publish workspace apps  
B) Members can add new members to the workspace  
C) Viewers can configure scheduled refresh  
D) Members can publish the workspace app but cannot add new workspace members  

---

## Q54

**You want to promote a semantic model so other teams know it is a trusted, high-quality source. You don't have an organizational certification authority configured. What is the HIGHEST endorsement level you can apply?**

A) Certified  
B) Promoted  
C) Verified  
D) Featured  

---

## Q55

**An organization needs reports to be refreshed 30 times per day. Which license/capacity tier supports this?**

A) Power BI Free  
B) Power BI Pro  
C) Power BI Premium capacity  
D) Power BI Desktop  

---

## Q56

**You are sharing a report with external users at a partner company using Azure AD B2B. The admin setting "Allow invitations to external users" is enabled. What license does the external user need to view the report if it is in a Premium capacity workspace?**

A) Power BI Pro  
B) Power BI Premium Per User (PPU)  
C) A free Power BI license (or no Power BI license — they access via their own AAD)  
D) A Microsoft 365 E3 license  

---

## Q57

**A developer wants to configure RLS with a role that restricts data based on the logged-in user's email. They test the report in Power BI Desktop by clicking "View as" and entering their email. The data appears unfiltered. What is the MOST likely cause?**

A) The DAX filter expression has a syntax error  
B) The SecurityMapping table is empty in the local Desktop file  
C) View as does not support dynamic RLS testing in Desktop  
D) The relationship between the SecurityMapping table and the fact table is missing or inactive  

---

## Q58

**Which of the following is a valid use for deployment pipeline deployment rules?**

A) Enforcing that only certified content can be deployed to Production  
B) Automatically replacing the data source server name when deploying from Test to Production  
C) Requiring manager approval before deployment to Production  
D) Copying workspace member roles from Development to Production  

---

## Q59

**A sensitivity label "Highly Confidential" is applied to a semantic model. A report author creates a new report using this semantic model. The author tries to apply a "Public" label to the report. What happens?**

A) The "Public" label is applied successfully  
B) Power BI blocks the label downgrade — the report inherits "Highly Confidential" and cannot be changed to a lower label without appropriate permissions  
C) The report has no label applied automatically  
D) The semantic model's label is automatically removed when a lower label is applied to the report  

---

## Q60

**Your company creates a Power BI embedded application for paying customers who do NOT have Power BI accounts. The application must display Power BI reports. Which embedding approach and authentication method is correct?**

A) Embed for your organization (User Owns Data) with Azure AD user authentication  
B) Embed for your customers (App Owns Data) with a service principal or master user account  
C) Publish to web with public embed code  
D) Embed using Analyze in Excel  

---

---

# ✅ ANSWER KEY

*Review each answer only after completing all 60 questions.*

---

| Q | Answer | Q | Answer | Q | Answer |
|---|--------|---|--------|---|--------|
| 1 | B | 21 | D | 41 | B |
| 2 | B | 22 | B | 42 | D |
| 3 | C | 23 | B | 43 | B |
| 4 | C | 24 | B | 44 | B |
| 5 | D | 25 | B | 45 | B |
| 6 | A | 26 | D | 46 | B |
| 7 | D | 27 | B | 47 | B |
| 8 | B | 28 | B | 48 | B |
| 9 | B | 29 | A | 49 | C |
| 10 | B | 30 | B | 50 | C |
| 11 | D | 31 | B | 51 | B |
| 12 | D | 32 | B | 52 | B |
| 13 | B | 33 | B | 53 | D |
| 14 | B | 34 | C | 54 | B |
| 15 | B | 35 | C | 55 | C |
| 16 | B | 36 | B | 56 | C |
| 17 | B | 37 | C | 57 | D |
| 18 | B | 38 | C | 58 | B |
| 19 | A | 39 | D | 59 | B |
| 20 | B | 40 | C | 60 | B |

---

## Detailed Explanations

**Q1 — B:** Import mode loads data into Power BI's in-memory engine (VertiPaq), delivering the fastest query performance for interactive reports. DirectQuery re-queries the source on every interaction, which is slower for 50M rows on a shared Azure SQL instance.

**Q2 — B:** Replace values in Power Query can target null values specifically and replace them with 0. Fill down copies the previous non-null value downward, which is inappropriate here.

**Q3 — C:** The **Combine files** feature from a folder connector automatically detects matching schemas and appends all files in the folder. Append queries (B) also works but requires importing each file individually first — the folder approach is more automated and maintainable.

**Q4 — C:** Since the date is text (20240315), Power BI can't directly parse it as Date. You must reformat the string (e.g., insert separators to get "2024-03-15") then convert. DAX DATEVALUE() (D) can also work as a calculated column fallback, but the Power Query approach is preferred at the transformation layer.

**Q5 — D:** "Hide in report view" prevents a table/column from appearing in the Fields pane for report authors, effectively hiding it from the report view without deleting it from the model. This hides Power Query queries from the report layer.

**Q6 — A:** Query folding pushes M/Power Query transformation steps back to the source system as native SQL (or equivalent), reducing data transferred over the network to only the needed, pre-filtered result set.

**Q7 — D:** Both approaches are valid: you can remove the first row via "Remove top rows" and then promote headers, or configure row-skipping in the connector's advanced options. Both achieve the same result.

**Q8 — B:** A **left outer join** (Merge) on CustomerID keeps all Sales rows and brings in matching Customer columns (including Region). Append stacks rows vertically — incorrect for adding columns. Full outer join returns non-matching rows from both sides unnecessarily.

**Q9 — B:** **Unpivot columns** transforms month columns (wide format) into rows with attribute/value pairs (long format), creating a Month column and an Amount column — standard normalization for analysis.

**Q10 — B:** "Organizational" privacy level means the data can be shared with other Organizational or Public level sources within Power Query combine operations, but cannot be mixed with Private sources without risking data leakage warnings.

**Q11 — D:** Query folding reduces data transfer by pushing transformations to the source (B), AND it is a prerequisite for incremental refresh to function correctly (C) — both benefits apply.

**Q12 — D:** Parameters can be modified in **Service semantic model settings** (A) for data source parameters, AND via **deployment rules** in deployment pipelines (C) when deploying between stages. Both are valid scenarios.

**Q13 — B:** **Trim** removes leading and trailing whitespace from text values. Clean removes non-printable characters. Lowercase converts case. Replace values replaces specific strings.

**Q14 — B:** **Column distribution** shows value count and distinct count charts. **Column profile** shows detailed statistics per column (min, max, average, error count, empty count). Together these are the data profiling tools in Power Query Editor.

**Q15 — B:** **Azure SQL Database** fully supports DirectQuery mode in Power BI. Excel, JSON files, and SharePoint Lists are file/service connectors that only support Import mode — DirectQuery requires a relational or analytical database engine.

**Q16 — B:** A **star schema** has a central fact table (containing metrics and foreign keys) surrounded by dimension tables (containing descriptive attributes). It is the recommended modeling pattern for Power BI for performance and simplicity.

**Q17 — B:** You must **mark the table as a Date table** (Table tools > Mark as date table) and specify the date column to enable Power BI's built-in time intelligence functions to work correctly and to disable the auto date/time hierarchy for that column.

**Q18 — B:** Calculated columns are materialized (stored in the model, computed at refresh time) row by row using row context. Measures are virtual — they exist only in metadata and are computed dynamically at report query time using filter context. This distinction is fundamental to DAX.

**Q19 — A:** ALL() removes all filters from the specified table or column, returning all rows regardless of current filter context. It is commonly used as a CALCULATE modifier to compute totals that ignore slicers or other filters.

**Q20 — B:** The measure divides the current filtered [Sales] by total Sales across ALL(Sales) rows (all filters removed). This produces each item's percentage share of the grand total, dynamically updating as filters change.

**Q21 — D:** Both SAMEPERIODLASTYEAR() (a dedicated time intelligence function) and DATEADD([Date], -1, YEAR) (a more flexible date shift function) correctly return the equivalent prior-year period. Both are correct approaches for year-over-year calculations.

**Q22 — B:** CALCULATE() evaluates its first argument (an expression) after modifying the current filter context with the filter arguments provided. It is the most powerful and commonly used DAX function for context manipulation.

**Q23 — B:** Bidirectional cross-filtering (Both) can cause filter ambiguity when multiple paths exist between tables, potentially leading to circular filter propagation and incorrect aggregation results. It should be used carefully and only when necessary.

**Q24 — B:** Many-to-many relationships in the real world should be resolved using a **bridge/junction table** (e.g., ProductPromotion with ProductID and PromotionID) that creates two one-to-many relationships. Direct many-to-many with Both cross-filter can work but introduces risk.

**Q25 — B:** RELATED() navigates from the "many" side to the "one" side of a relationship and returns the value from the related table's column for the current row. It only works in the row context of a calculated column.

**Q26 — D:** DATESYTD() (B) gives a year-to-date running total (resets each year). The FILTER/ALL/MAX pattern (C) gives an all-time running total from the earliest date. Both are correct for their respective "running total" use cases — the question acknowledges both are valid.

**Q27 — B:** Calculation groups allow you to define a set of calculation items (e.g., YTD, MTD, Prior Year, % Change) that can be dynamically applied to any measure in the model, eliminating the need to write redundant time intelligence measures for every base measure.

**Q28 — B:** Setting a column as **Hidden** in Model view removes it from the Fields pane (report authors can't see or use it directly) while keeping it in the model for DAX calculations and relationships. Deleting removes it entirely. OLS is more security-focused.

**Q29 — A:** A **composite model** combines Import-mode tables with DirectQuery tables in the same model, or connects to multiple DirectQuery sources. This allows mixing pre-aggregated imported data with real-time DirectQuery data for performance optimization.

**Q30 — B:** USERELATIONSHIP() activates an **inactive relationship** for the scope of a CALCULATE expression. For example, if you have two date columns (Order Date and Ship Date) but only one active relationship to the Date table, USERELATIONSHIP() activates the inactive one temporarily.

**Q31 — B:** The second argument to DATESYTD() specifies the **fiscal year end date**. "6/30" means the fiscal year ends June 30, so the YTD calculation resets on July 1 each year (beginning of the new fiscal year).

**Q32 — B:** RANKX() ranks values in a table by an expression, supports ties, and can use Dense (no gaps) or Skip (standard competition) ranking via optional arguments. It returns numeric ranks and handles the full range of ranking scenarios.

**Q33 — B:** TREATAS() applies a list of values (from one table) as filter context on a column in another table that has no physical relationship. It treats the values "as if" they were related, creating a virtual filter context bridge.

**Q34 — C:** A **100% stacked bar chart** normalizes each bar to 100% height, showing the proportional composition of categories within each bar. This is the correct choice for comparing relative market share across categories.

**Q35 — C:** **Sync slicers** with page-specific settings allow a slicer to be synchronized to specific pages (Pages 1, 2, 4) while not syncing to others (Page 3). This is configured in View > Sync slicers.

**Q36 — B:** The standard show/hide pattern uses: **Selection pane** (to set visual visibility states) + **Bookmarks** (to capture two states: one with table visible, one with card visible) + **Buttons** with bookmark actions to toggle between them.

**Q37 — C:** The **Decomposition Tree** is purpose-built for interactive root cause analysis — users can drill into a metric by different dimensions sequentially, with AI-assist for "High value" and "Low value" suggestions to find contributing factors.

**Q38 — C:** The **Forecast** option in the Analytics pane projects future values forward with a configurable forecast length and confidence interval. Trend lines show historical direction but don't project future values.

**Q39 — D:** Both B and C are correct: Tooltip pages must be set to **Hidden** (in the page visibility settings) so they don't appear as navigable pages, but Power BI still uses them as tooltips. They can contain any visuals including sparklines, images, and KPI cards.

**Q40 — C:** The value -5 is below 0, which matches the "below 0 = red font" rule. Conditional formatting rules are evaluated in order and the first matching rule is applied.

**Q41 — B:** The correct sequence is: (1) Create the drill-through target page, (2) Add the drill-through field to that page's Drill-through well, (3) Build the source visual, (4) Right-click a data point. The target page setup must exist before the right-click drill-through option appears on source visuals.

**Q42 — D:** Q&A visual does respect Row-Level Security — it only shows data the user is authorized to see based on their RLS role. It DOES accept natural language (A), generate visuals (B), and support synonym training via Q&A setup (C). Therefore D is the false statement — making it the "NOT a capability" answer.

**Q43 — B:** Automatic page refresh is configured in the report page's **Format pane > Page information > Page refresh** in Power BI Desktop (or Page settings in Service). It is a page-level visual setting, not a semantic model or admin setting.

**Q44 — B:** The **Play axis** well on a scatter chart accepts a date/time field and enables animated playback — the chart animates through each time period, showing how the X/Y relationship evolves over time (like a Gapminder-style animation).

**Q45 — B:** **Personalize visuals** is the feature that allows report consumers to modify visual types and field assignments in published reports without edit permissions. It must be enabled by the report author in report settings.

**Q46 — B:** **Paginated reports** (.rdl) are designed for precisely formatted, multi-page, print-ready documents with headers, footers, and page numbers — exactly what's needed for large transactional data exports. Standard reports don't paginate cleanly for print.

**Q47 — B:** When clicking an anomaly marker in Power BI's anomaly detection, an **explanation panel** appears that shows which dimensions or field values may explain the anomaly (e.g., "Region: West contributed most to this anomaly"). It uses AI to surface the most likely explanatory factors.

**Q48 — B:** Clicking the expand arrow on a matrix row hierarchy node **expands that node to show sub-rows** at the next level (drill-down within the matrix visual). This is the standard matrix hierarchy interaction — no page navigation occurs.

**Q49 — C:** The Selection pane's **tab order** management (selecting "Tab order" view in the Selection pane) allows authors to set keyboard navigation order for accessibility. Objects higher in the Selection pane list are rendered IN FRONT (higher z-order), not behind — option B has this reversed.

**Q50 — C:** Mobile layout mode lets you **choose which visuals from the desktop layout to include** and rearrange them on a phone-shaped canvas. You don't recreate visuals (B), and they aren't auto-arranged (A). No Premium license needed (D).

**Q51 — B:** The correct visual-to-use-case matches: Waterfall → sequential +/- to net result (financial bridge), Treemap → proportional hierarchy (parts of a whole), Funnel → progressive stage reduction (pipeline/conversion), Line chart → trends over time.

**Q52 — B:** In dynamic RLS, authorization is data-driven — access is controlled by the SecurityTable contents. To grant the new employee access, **add their email and region to the SecurityTable** in the data source. The RLS role and DAX filter remain unchanged. No Desktop republish needed unless the model structure changed.

**Q53 — D:** Members can publish the workspace app (and update it), but only Admins can add new workspace members. Contributors cannot publish the app. Viewers cannot configure refresh. This is the precise Member vs. Contributor vs. Admin boundary.

**Q54 — B:** Without an organizational certification authority configured in tenant settings, users cannot apply **Certified** endorsement. The highest self-service endorsement available is **Promoted**, which any workspace Admin or Member can apply. "Verified" and "Featured" are not Power BI endorsement tiers.

**Q55 — C:** Power BI Pro allows up to **8 refreshes per day**. **Power BI Premium** capacity supports up to **48 refreshes per day**. 30 refreshes/day falls within the Premium limit. Free doesn't support scheduled refresh for shared content. Desktop can't refresh published models.

**Q56 — C:** When a workspace is on **Premium capacity**, external B2B users can view content with a free Power BI license (or in some cases just their AAD identity). Premium capacity removes the Pro license requirement for consumers, including external B2B guests.

**Q57 — D:** If dynamic RLS returns unfiltered data, the most common cause is a **missing or inactive relationship** between the SecurityMapping table and the fact table. Without the relationship, the DAX filter on SecurityMapping cannot propagate to filter the Sales data. A syntax error (A) would cause an error, not unfiltered data.

**Q58 — B:** **Deployment rules** automatically replace data source connection strings (server names, database names) and parameter values when deploying from one pipeline stage to another. They are not approval workflows, label enforcers, or membership copiers.

**Q59 — B:** Microsoft Purview's **label downgrade protection** prevents users from reducing the sensitivity classification of content (e.g., from Highly Confidential to Public) without appropriate justification or permissions. Label inheritance and protection flow downstream; downgrading requires elevated permissions.

**Q60 — B:** **"Embed for your customers" (App Owns Data)** using a **service principal** is the correct pattern for ISV/customer-facing apps where end users don't have Power BI accounts. The app authenticates on behalf of all users. "Embed for your organization" requires users to have their own Power BI accounts and authenticate themselves.

---

## Score Interpretation

| Score | Result |
|-------|--------|
| 50–60 | Excellent — Ready to take the exam |
| 44–49 | Passing threshold — Review weak areas |
| 38–43 | Close — Focused review needed on lowest-scoring domain |
| Below 38 | More preparation needed — revisit study materials |

**Domain Scores:**
- Q1–Q15 (Prepare Data): ___/15
- Q16–Q33 (Model Data): ___/18
- Q34–Q51 (Visualize & Analyze): ___/18
- Q52–Q60 (Manage & Secure): ___/9
