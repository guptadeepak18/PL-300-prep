# PL-300 Practice Questions — Section 1: Prepare the Data

> **55 Multiple Choice Questions** covering the "Prepare the Data" domain of the Microsoft Power BI Data Analyst (PL-300) certification exam.  
> Topics include: Power Query data sources, connection modes, data profiling, transformations, M code, query folding, incremental refresh, privacy levels, and more.

---

## Table of Contents

1. [Data Sources & Connections (Q1–Q10)](#data-sources--connections)
2. [Connection Modes (Q11–Q18)](#connection-modes)
3. [Data Profiling & Quality (Q19–Q25)](#data-profiling--quality)
4. [Data Transformations (Q26–Q36)](#data-transformations)
5. [Merge & Append Queries (Q37–Q44)](#merge--append-queries)
6. [M Code & Advanced Transformations (Q45–Q49)](#m-code--advanced-transformations)
7. [Query Folding & Incremental Refresh (Q50–Q53)](#query-folding--incremental-refresh)
8. [Privacy Levels & Parameters (Q54–Q55)](#privacy-levels--parameters)

---

## Data Sources & Connections

---

## Question 1

**A data analyst needs to connect Power BI Desktop to a company SharePoint Online document library to load an Excel workbook stored there. Which connector should they use?**

A) Web connector with the SharePoint file URL  
B) SharePoint Online List connector  
C) SharePoint Folder connector  
D) Excel Workbook connector with a local file path  

**✅ Correct Answer: C**

**Explanation:** The **SharePoint Folder** connector is the correct choice when connecting to a SharePoint document library. It lets you navigate the library, select the specific file, and then combine or expand its contents. The **SharePoint Online List** connector (B) is for SharePoint lists, not document libraries. The **Web** connector (A) would require authentication complexity and is not optimised for SharePoint file access. The **Excel Workbook** connector (D) requires a local file path, which is incompatible with a SharePoint URL.

---

## Question 2

**A developer wants to pull data from a REST API that returns JSON. The API requires a Bearer token in the Authorization header on every request. Which Power BI connector and authentication method best supports this scenario?**

A) OData Feed connector with Windows authentication  
B) Web connector with Anonymous authentication and a custom header added in Power Query  
C) JSON connector with OAuth2 authentication  
D) Web connector with API Key authentication, placing the token in the key field  

**✅ Correct Answer: B**

**Explanation:** The **Web** connector supports REST APIs that return JSON. When the API requires a Bearer token header, you use **Anonymous** authentication in the connection dialog and then manually add the `Authorization: Bearer <token>` header in the advanced HTTP request options within Power Query. Option D (API Key) places the key as a query parameter or header but is a distinct authentication type not universally supported by all web connectors. OData (A) is for OData-compliant endpoints. There is no standalone "JSON connector" (C) with OAuth2 in Power BI Desktop.

---

## Question 3

**Your organisation stores operational data in an Azure SQL Database. You want to build a Power BI report that always shows the latest data without manual refreshes, but the dataset can be very large (200 GB). Which connection approach is most appropriate?**

A) Import mode — schedule hourly refreshes  
B) DirectQuery mode connected to Azure SQL Database  
C) Live Connection to an Analysis Services model  
D) Composite model combining Import and DirectQuery  

**✅ Correct Answer: B**

**Explanation:** **DirectQuery** queries the Azure SQL Database directly at report render time, so users always see the latest data without any dataset refresh. Because data is not imported into Power BI, the 200 GB size is not a constraint. Import mode (A) would require storing 200 GB in the Power BI service (exceeding Premium limits) and hourly refreshes would still lag. Live Connection (C) requires a published Analysis Services model, which is a separate layer. A Composite model (D) could work but adds complexity and the DirectQuery portion still hits the DB directly.

---

## Question 4

**A report author connects to an on-premises SQL Server database from Power BI Desktop. When they publish to the Power BI service and attempt a scheduled refresh, it fails. What is the most likely cause?**

A) SQL Server databases are not supported in the Power BI service  
B) The on-premises data gateway has not been installed and configured  
C) The dataset must use DirectQuery to refresh from on-premises sources  
D) Scheduled refresh requires a Premium capacity licence  

**✅ Correct Answer: B**

**Explanation:** On-premises data sources require an **on-premises data gateway** to bridge communication between the Power BI service (cloud) and the on-premises SQL Server. Without a gateway, the service cannot reach the data source, causing the refresh to fail. SQL Server is fully supported in the service (A is wrong). Import mode with scheduled refresh works fine for on-premises sources via gateway (C is wrong). Scheduled refresh is available on shared capacity with a Pro licence (D is wrong).

---

## Question 5

**A Power BI report must connect to a folder of CSV files that land in an Azure Data Lake Storage Gen2 container each day. New files are added daily. Which connector lets you automatically combine all CSV files in that container?**

A) Azure Blob Storage connector  
B) Azure Data Lake Storage Gen2 connector, using the Combine Files option  
C) Text/CSV connector pointing to the container URL  
D) Web connector with the container endpoint  

**✅ Correct Answer: B**

**Explanation:** The **Azure Data Lake Storage Gen2** connector connects to an ADLS Gen2 container and returns a list of all files. The **Combine Files** (formerly "Combine Binaries") feature in Power Query automatically applies a sample transformation to every file in the folder and unions the results. As new CSV files are added, the next refresh will pick them up automatically. Azure Blob Storage (A) works similarly but is for Blob, not ADLS Gen2. The Text/CSV connector (C) requires a single file path. The Web connector (D) is not designed for ADLS container browsing.

---

## Question 6

**While building a dataset in Power BI Desktop, a developer wants to connect to a published Power BI dataset to use it as a data source for additional calculated tables and measures. Which connector enables this?**

A) Analysis Services connector  
B) Power BI Datasets connector (also shown as "Power BI semantic models")  
C) OData Feed connector pointed at the XMLA endpoint  
D) SQL Server connector using the workspace connection string  

**✅ Correct Answer: B**

**Explanation:** The **Power BI Datasets** connector (labelled "Power BI semantic models" in newer versions) allows a Power BI Desktop file to connect to a published dataset in the Power BI service and build additional layers on top of it — a pattern called **chaining** or **composite models over published datasets**. The Analysis Services connector (A) can also reach the XMLA endpoint but is typically used for external AS models. OData (C) does not map to a Power BI dataset endpoint. SQL Server (D) cannot connect to a Power BI dataset.

---

## Question 7

**A report developer connects Power BI to a live website's HTML table using the Web connector. The website requires the user to log in before the table is visible. Which authentication type should they select in the Web connector dialog?**

A) Anonymous  
B) Windows  
C) Basic  
D) Organisational Account  

**✅ Correct Answer: D**

**Explanation:** If the website uses Azure Active Directory / Microsoft 365 sign-in, **Organisational Account** authentication allows the user to sign in with their work credentials. **Anonymous** (A) works only for public pages. **Windows** (B) is for Windows-integrated authentication (intranet). **Basic** (C) sends a username/password in plain HTTP headers and is used for sites that explicitly implement HTTP Basic auth, not typical consumer or corporate login pages.

---

## Question 8

**An analyst imports data from a SQL Server stored procedure that accepts an input parameter for the reporting month. They want report consumers to be able to select the month from a dropdown and have Power BI pass that value to the stored procedure. What is the correct approach in Power Query?**

A) Create a Power Query parameter and reference it in the SQL command text  
B) Use a report-level filter in Power BI Desktop  
C) Create a calculated column in DAX to filter the month  
D) Duplicate the query for each month value  

**✅ Correct Answer: A**

**Explanation:** Power Query **parameters** hold a value that can be referenced anywhere in the M code, including within a dynamic SQL string or function argument. By creating a parameter (e.g., `ReportMonth`) and embedding it in the native SQL query or function call, the stored procedure receives the dynamic value. Report-level filters (B) operate post-import on imported data. DAX calculated columns (C) run after data is loaded, not during the query execution. Duplicating queries (D) is not scalable and does not allow dynamic selection.

---

## Question 9

**A company uses an Excel workbook with multiple named tables stored on a SharePoint site. When the analyst connects via the Excel Workbook connector locally and then publishes to the service, the refresh fails because the file path is local (C:\Users\...). How should they fix this?**

A) Re-connect using the SharePoint Folder connector and change the data source settings in the service  
B) Upload the Excel file to the Power BI service directly  
C) Change the file extension from .xlsx to .csv  
D) Use the "Enter Data" feature to copy the table into Power BI  

**✅ Correct Answer: A**

**Explanation:** The correct fix is to reconnect using the **SharePoint Folder** connector (or update the data source settings in the Power BI service to point to the SharePoint URL instead of a local path). This allows the service to access the file directly via SharePoint Online without a gateway. Uploading the Excel file to the service (B) would create a separate dataset, not a connected one. Changing to CSV (C) loses formatting and tables. "Enter Data" (D) is a manual static import, not a connected refresh.

---

## Question 10

**A data analyst needs to connect to a Google Analytics account to bring in website traffic data into Power BI. Which connector should they use?**

A) Web connector with the Google Analytics reporting URL  
B) OData Feed connector  
C) Google Analytics connector (under Online Services)  
D) JSON connector with the Google Analytics API response  

**✅ Correct Answer: C**

**Explanation:** Power BI includes a dedicated **Google Analytics** connector under the "Online Services" category. It handles OAuth authentication to Google and exposes dimensions and metrics in a structured navigator. The Web connector (A) would require manually constructing API calls. OData (B) is not how Google Analytics exposes its data. The JSON connector (D) would require raw API handling without the convenience of the built-in connector.

---

## Connection Modes

---

## Question 11

**A retail company has a transactional SQL database that grows by 50,000 rows per day. Reports must always reflect data as of the current hour. The total table size is 10 million rows. Which connection mode is most appropriate?**

A) Import with a once-daily scheduled refresh  
B) DirectQuery  
C) Import with hourly scheduled refresh (Power BI Pro)  
D) Live Connection  

**✅ Correct Answer: B**

**Explanation:** Because reports must always show near-real-time data (current hour), **DirectQuery** is the best fit — every visual query is sent directly to the SQL database so data is always current. Import with daily refresh (A) would be 24 hours behind. Hourly refresh (C) with Power BI Pro is limited to 8 refreshes per day, and data could still be up to 1 hour stale; also, Pro allows a maximum of 8 scheduled refreshes/day. Live Connection (D) requires an Analysis Services or Power BI dataset intermediary, not a direct SQL connection.

---

## Question 12

**A Power BI developer is building a report on top of an Azure Analysis Services (AAS) tabular model that the BI team manages. The developer needs to add organisation-specific DAX measures not present in the AAS model. Which connection mode supports this requirement?**

A) DirectQuery to AAS  
B) Live Connection to AAS  
C) Import from AAS  
D) Composite model: Live Connection to AAS with local model enhancements  

**✅ Correct Answer: D**

**Explanation:** A **Composite model over a Live Connection** allows a Power BI Desktop file to connect via Live Connection to AAS and then add local calculated tables, calculated columns, and measures on top of the remote model — something pure Live Connection (B) prohibits. DirectQuery to AAS (A) is technically possible but does not allow local model additions in the same way. Import from AAS (C) breaks the live connection semantics and the data governance the AAS model provides.

---

## Question 13

**Which of the following is a known limitation of DirectQuery mode in Power BI?**

A) Data cannot be filtered in the report  
B) The data model cannot have relationships between tables  
C) Certain DAX functions and complex calculated columns may not be supported  
D) DirectQuery reports cannot be published to the Power BI service  

**✅ Correct Answer: C**

**Explanation:** In DirectQuery mode, Power BI translates DAX into native SQL queries sent to the data source. Not all DAX functions translate to SQL equivalents, so **certain calculated columns and complex DAX expressions are unsupported or restricted**. Filtering works in DirectQuery (A is wrong). Relationships between tables are fully supported in DirectQuery (B is wrong). DirectQuery reports can absolutely be published to the Power BI service (D is wrong).

---

## Question 14

**A developer wants to combine a large fact table from a SQL Server data warehouse (using DirectQuery) with a small lookup table imported from an Excel file. Which Power BI feature enables this?**

A) Dataflow  
B) Composite model  
C) Query merging in Power Query  
D) A calculated table in DAX  

**✅ Correct Answer: B**

**Explanation:** A **Composite model** allows a single Power BI dataset to have tables in both Import and DirectQuery storage modes simultaneously. The large fact table remains in DirectQuery (no import performance hit) while the small Excel lookup table is fully imported for fast filtering. Dataflows (A) are used for data preparation but do not combine storage modes. Power Query merge (C) requires both tables to be in the same storage mode. A DAX calculated table (D) is evaluated post-import and cannot reference DirectQuery tables directly.

---

## Question 15

**In a Composite model, what is the key benefit of setting a table to "Dual" storage mode?**

A) The table is stored in both the VertiPaq in-memory engine and queried live simultaneously, allowing Power BI to choose the most efficient execution path  
B) The table is duplicated into two separate datasets  
C) The table can be refreshed twice per day  
D) The table supports both row-level security and object-level security at the same time  

**✅ Correct Answer: A**

**Explanation:** **Dual storage mode** causes a table to be stored both in the VertiPaq in-memory engine (Import) and to be treated as a DirectQuery table depending on which other tables it is queried with. When combined with an Import table, VertiPaq handles the query; when combined with a DirectQuery table, it queries the source directly. This flexibility optimises performance. It does not create two datasets (B), affect refresh frequency (C), or relate to security layers (D).

---

## Question 16

**An organisation requires that a Power BI dataset always reads data from Azure Synapse Analytics in real-time and that no data is stored within the Power BI service. Which connection mode satisfies this requirement?**

A) Import  
B) Streaming dataset  
C) DirectQuery  
D) Push dataset  

**✅ Correct Answer: C**

**Explanation:** **DirectQuery** sends queries to the source (Azure Synapse Analytics) at report interaction time and stores **no data** in the Power BI service. Import (A) copies data into the Power BI storage engine, violating the "no data stored" requirement. Streaming datasets (B) and push datasets (D) are designed for real-time tile updates on dashboards using streaming data pipelines, not for querying a data warehouse on-demand.

---

## Question 17

**A Live Connection to a Power BI published dataset differs from a Live Connection to Azure Analysis Services in which key way?**

A) Live Connection to a Power BI dataset allows adding local measures; Live Connection to AAS does not  
B) Live Connection to a Power BI dataset does not allow adding local measures without upgrading to a Composite model; Live Connection to AAS also requires Composite model for local additions  
C) Live Connection to AAS supports row-level security; Live Connection to a Power BI dataset does not  
D) There is no difference; both behave identically  

**✅ Correct Answer: B**

**Explanation:** In both cases — Live Connection to a **Power BI published dataset** and to **Azure Analysis Services** — you cannot add local measures, calculated columns, or tables in the connecting .pbix file by default. To do so, you must upgrade to a **Composite model**, which "unlocks" local additions for both connection types. Therefore B is correct. A is incorrect because neither allows local additions without a Composite model. RLS is supported on Power BI datasets too (C is wrong). D is wrong because there are technical differences in refresh and management.

---

## Question 18

**A company's Power BI dataset uses Import mode and is 8 GB in size after compression in VertiPaq. The dataset refresh is failing because it exceeds the 1 GB dataset size limit on shared capacity. What is the recommended solution?**

A) Switch to DirectQuery mode  
B) Move the workspace to a Power BI Premium capacity or Premium Per User (PPU)  
C) Split the dataset into multiple .pbix files  
D) Compress the source data before importing  

**✅ Correct Answer: B**

**Explanation:** Power BI **shared capacity (Pro)** has a 1 GB dataset size limit. **Premium capacity and PPU** support datasets up to 25 GB (or larger with large dataset storage format enabled). Moving the workspace to Premium/PPU directly resolves the size limitation. Switching to DirectQuery (A) avoids the size limit but changes the data access pattern significantly and may not suit all scenarios. Splitting into multiple .pbix files (C) causes modelling fragmentation. Compressing source data (D) may reduce the raw file size but the VertiPaq engine already compresses aggressively; source compression doesn't control the in-memory model size.

---

## Data Profiling & Quality

---

## Question 19

**A data analyst opens the Power Query Editor and wants to see what percentage of values in a column are valid, empty, or errors. Which Power Query feature should they enable?**

A) Column Distribution  
B) Column Quality  
C) Column Profile  
D) Data Preview  

**✅ Correct Answer: B**

**Explanation:** **Column Quality** displays three statistics for each column in the data preview header: **Valid**, **Error**, and **Empty** percentages. Column Distribution (A) shows the count of distinct vs. total values and a visual histogram of value frequencies. Column Profile (C) shows detailed statistics (min, max, average, count of distinct values, value distribution) for a selected column. Data Preview (D) is a generic term for the grid that shows rows, not quality metrics specifically.

---

## Question 20

**By default, Power Query's column profiling shows statistics based on only the first 1,000 rows. How can an analyst ensure that profiling statistics reflect the entire dataset?**

A) Increase the data preview row limit in Power Query settings  
B) Click "Column profiling based on entire data set" in the View tab of Power Query Editor  
C) Use a native SQL query to pre-aggregate the data  
D) Import all data first and then enable profiling in Power BI Desktop  

**✅ Correct Answer: B**

**Explanation:** In the **View** tab of Power Query Editor there is a status-bar option labelled **"Column profiling based on entire data set"** (vs. "based on top 1000 rows"). Selecting this option causes Power Query to scan all rows when computing profiling statistics. Option A does not exist as a settings option. Native SQL pre-aggregation (C) would change the data, not fix profiling scope. Profiling in Power BI Desktop (D) is the same Power Query Editor — the setting is the same.

---

## Question 21

**The Column Distribution view in Power Query shows that a "CustomerID" column has 9,800 distinct values out of 10,000 total values. What does this most likely indicate?**

A) The column has 200 null values  
B) There are 200 duplicate CustomerID values  
C) The column has 200 error values  
D) The data type is incorrect for this column  

**✅ Correct Answer: B**

**Explanation:** Column Distribution shows **Distinct** (unique values ignoring duplicates) and **Unique** (values appearing exactly once). If there are 9,800 distinct values from 10,000 rows, approximately 200 values appear more than once — i.e., there are **200 duplicate CustomerID entries**. Null values would show in Column Quality (A). Error values also appear in Column Quality (C). Data type issues are separate and not indicated by the distinct/total count ratio (D).

---

## Question 22

**An analyst notices that a "Revenue" column in Power Query shows 15% "Error" in the Column Quality view. What is the most appropriate first action?**

A) Delete all rows with errors  
B) Replace errors with a default value such as 0  
C) Click on the "Error" indicator to filter and inspect the error rows to understand the cause  
D) Change the data type of the column from Text to Decimal  

**✅ Correct Answer: C**

**Explanation:** Before taking any remediation action, the analyst should **inspect the error rows** to understand what is causing the errors. Clicking the "Error" percentage in Column Quality filters the preview to show only error rows, revealing the error messages and affected values. Deleting all error rows (A) may remove legitimate data. Replacing with 0 (B) may be appropriate for some errors but not all (e.g., division errors vs. type conversion errors). Changing the data type (D) may be the fix, but only after diagnosing the root cause.

---

## Question 23

**In Power Query, a "SalesDate" column was imported as a Text data type and contains values like "2024-01-15". When the analyst changes the data type to Date, some rows show errors. Which of the following is the most likely cause?**

A) Power Query does not support the ISO 8601 date format  
B) Some text values in the column are not valid date strings (e.g., "N/A", blank cells entered as text)  
C) The column has too many rows for type conversion  
D) Date columns are not supported in DirectQuery mode  

**✅ Correct Answer: B**

**Explanation:** When converting from Text to Date, Power Query attempts to parse every value. If the column contains non-date strings such as "N/A", "TBD", or empty strings stored as text, those values cannot be converted and produce errors. Power Query fully supports ISO 8601 date format (A is wrong). Row count does not cause type conversion errors (C is wrong). DirectQuery mode is irrelevant here as this is a transformation step (D is wrong).

---

## Question 24

**A dataset contains a "Postcode" column that should never be null. An analyst wants Power Query to automatically flag any null postcode as a data quality issue during refresh — without removing those rows. What is the best approach?**

A) Use "Remove Rows with Errors" after setting the column to required  
B) Add a conditional column that marks rows with null postcodes as "Invalid" for downstream review  
C) Use the "Keep Errors" option on the column  
D) Set a column constraint in the source SQL database  

**✅ Correct Answer: B**

**Explanation:** Adding a **conditional column** (e.g., `= if [Postcode] = null then "Invalid" else "Valid"`) creates a quality flag column that preserves all rows while clearly identifying problematic records. This allows downstream processes or report consumers to filter and review invalid data without data loss. "Remove Rows with Errors" (A) deletes rows, which is not desired. "Keep Errors" (C) is for error values, not nulls specifically, and doesn't add a flag column. Setting SQL constraints (D) would reject the data at source and is beyond the Power Query layer.

---

## Question 25

**An analyst uses "Fill Down" on a "Region" column in Power Query. What does this operation do?**

A) Replaces all null values with the region name entered by the user  
B) Propagates each non-null value downward to fill subsequent null cells until the next non-null value  
C) Sorts the column alphabetically and fills missing positions  
D) Copies the first row's value to all rows in the column  

**✅ Correct Answer: B**

**Explanation:** **Fill Down** scans the column from top to bottom and replaces each null cell with the **last non-null value above it**. This is commonly used when a source report merges cells vertically — the category appears once at the top of a group and remaining rows in that group are blank. Fill Down restores the category to every row. It does not prompt the user (A), sort the data (C), or copy the first-row value to all rows (D).

---

## Data Transformations

---

## Question 26

**A Power Query table has a "Month" column with values (Jan, Feb, Mar…) and a "Category" column. The analyst needs to reshape the data so that each month becomes its own column, with values from a "Sales" column as the cell values. Which transformation achieves this?**

A) Transpose  
B) Unpivot Columns  
C) Pivot Column  
D) Group By  

**✅ Correct Answer: C**

**Explanation:** **Pivot Column** takes a column of row values and spreads them into new columns, using another column's values to populate the cells. In this case, selecting the "Month" column and pivoting it with "Sales" as the values column creates columns Jan, Feb, Mar, etc. **Transpose** (A) flips rows into columns and columns into rows for the entire table. **Unpivot** (B) is the reverse operation — collapsing columns into rows. **Group By** (D) aggregates rows by a grouping key.

---

## Question 27

**A flat table has separate columns for Jan_Sales, Feb_Sales, Mar_Sales, Apr_Sales, etc. A data model requires a long-format table with a single "Month" column and a single "Sales" column. Which Power Query transformation achieves this?**

A) Pivot Column  
B) Transpose  
C) Unpivot Columns (selecting the month columns)  
D) Merge Queries  

**✅ Correct Answer: C**

**Explanation:** **Unpivot Columns** converts attribute columns (Jan_Sales, Feb_Sales, etc.) into rows, creating an "Attribute" column (containing the original column headers) and a "Value" column (containing the sales amounts). You would then rename those columns to "Month" and "Sales". This is the classic normalisation pattern for wide-to-long reshaping. Pivot (A) is the opposite operation. Transpose (B) flips all rows/columns. Merge (D) joins two tables and does not reshape columns into rows.

---

## Question 28

**An analyst uses "Group By" in Power Query to summarise a sales table by "Region" and "Category", calculating the total Sales for each combination. After applying Group By, which of the following best describes what happens to the original rows?**

A) Original rows are deleted from the data source permanently  
B) The original rows are replaced by summary rows in the query result; the source data is unchanged  
C) Group By creates a new separate table in Power BI  
D) The original rows remain in the query output alongside the new summary rows  

**✅ Correct Answer: B**

**Explanation:** **Group By** is a Power Query transformation step that aggregates data. The query output contains only the grouped summary rows — the original detail rows are not present in the query result. However, the source data is completely unchanged. It is a query-level transformation only (B). The source data is never modified by Power Query (A). Group By produces rows in the same query, not a new separate table (C). The original and summary rows do not coexist in the same query output (D).

---

## Question 29

**A "FullName" column contains values like "Smith, John". A developer needs to split this into "LastName" and "FirstName" columns. Which Power Query feature is most appropriate?**

A) Split Column by Delimiter  
B) Extract — Text Before Delimiter  
C) Group By  
D) Add Column from Examples  

**✅ Correct Answer: A**

**Explanation:** **Split Column by Delimiter** is designed exactly for this scenario. Specifying the comma (`,`) as the delimiter will split "Smith, John" into two columns: "Smith" and " John" (the space can then be trimmed). "Extract — Text Before Delimiter" (B) would extract only the last name but not automatically create the first name column. "Group By" (C) is an aggregation, not a text split. "Add Column from Examples" (D) could work by inferring the pattern from examples you provide, but it is less direct than Split Column by Delimiter.

---

## Question 30

**A developer needs to remove all rows from a Power Query table where the "OrderDate" is before January 1, 2020. Which approach in Power Query achieves this?**

A) Replace Values — replace all dates before 2020 with null  
B) Filter Rows — filter the OrderDate column to be on or after 01/01/2020  
C) Group By with a minimum date filter  
D) Add a conditional column and then delete rows where the flag is "Old"  

**✅ Correct Answer: B**

**Explanation:** **Filter Rows** directly removes rows that do not meet the specified condition. You can filter the OrderDate column using "is after or equal to" 01/01/2020, which keeps only the desired rows. Replace Values (A) would set old dates to null but keep the rows. Group By (C) would aggregate, not filter individual rows. Adding a conditional column and then deleting flagged rows (D) works but is a two-step workaround when Filter Rows accomplishes this in one step.

---

## Question 31

**In Power Query, what is the difference between "Remove Duplicates" and "Keep Duplicates"?**

A) "Remove Duplicates" deletes all copies of a duplicated row; "Keep Duplicates" keeps only the rows that appear more than once  
B) "Remove Duplicates" keeps one instance of each unique row; "Keep Duplicates" removes all unique rows  
C) Both operations produce the same result but in different orders  
D) "Remove Duplicates" operates on a single column; "Keep Duplicates" operates on the entire table  

**✅ Correct Answer: A**

**Explanation:** **Remove Duplicates** retains one instance of each distinct row and removes all extra copies — the result is a deduplicated table. **Keep Duplicates** does the opposite: it retains only the rows that are **not unique** (i.e., rows that appear more than once), which is useful for finding duplicate records. Both operations can be applied to selected columns or the full table. C is wrong (they produce opposite results). D is wrong (both can operate on selected columns or the whole table).

---

## Question 32

**An analyst has a column with data type "Whole Number" that contains values representing US dollar amounts stored as integers (e.g., 1999 meaning $19.99). They need to divide all values by 100 to get the decimal amount. Which Power Query approach correctly transforms all values?**

A) Add a conditional column with the formula `[Amount] / 100`  
B) Right-click the column → Transform → Divide → enter 100  
C) Change the column data type to Decimal Number  
D) Use Replace Values to find "1999" and replace with "19.99"  

**✅ Correct Answer: B**

**Explanation:** **Transform → Divide** (or **Standard → Divide** in the Transform tab) applies a division operation to every value in the selected column, replacing each value with the divided result. This is an in-place column transformation. Adding a conditional column (A) creates a new column but doesn't replace the original; also, conditional columns aren't used for arithmetic on all rows. Changing the data type (C) would format the display but does not perform arithmetic division. Replace Values (D) would only work for exact string matches, which is impractical for numeric arithmetic across many distinct values.

---

## Question 33

**A data analyst receives a JSON file exported from a web service. After loading it into Power Query, all data appears in a single column as a list of Record objects. What is the correct sequence of steps to flatten this into a tabular format?**

A) Click "To Table" to convert the list to a table, then expand the Record column to individual columns  
B) Use the JSON connector instead of the Text/CSV connector  
C) Convert the JSON to CSV before loading into Power Query  
D) Use "Transpose" to convert the records into columns  

**✅ Correct Answer: A**

**Explanation:** When Power Query loads a JSON file with a top-level array, it presents it as a **List** in the query preview. The steps are: (1) click **"To Table"** to convert the List to a single-column table containing Records, (2) click the **expand icon** on the column header to expand each Record into its individual fields/columns. This produces a clean tabular result. The connector choice (B) doesn't change the expansion need. Pre-converting to CSV (C) is unnecessary and lossy. Transpose (D) flips rows and columns and would not expand nested records.

---

## Question 34

**An XML file contains nested elements. When loaded in Power Query, it shows a "Table" value in a single column. What step is needed to access the data within those nested tables?**

A) Use "Expand to New Rows" on the nested Table column  
B) Use the "Drill Down" feature  
C) Click the expand icon on the column to expand the nested Table into columns or rows  
D) Use M function `Xml.Document()` with a custom column  

**✅ Correct Answer: C**

**Explanation:** When Power Query displays a cell containing **[Table]**, clicking the **expand icon** (two-arrow icon in the column header) lets you expand each nested table's columns into the parent table or expand rows. This is the standard approach for both XML and JSON nested structures. "Expand to New Rows" (A) exists as an option within the expand dialog but it is not a separate button — it is part of the expand operation. "Drill Down" (B) navigates into a single cell value, not all rows. Using `Xml.Document()` via a custom column (D) would require writing M code for data that's already been parsed.

---

## Question 35

**A developer uses Power Query to connect to a SQL Server table that has 5 million rows. They want to keep only the top 1,000 rows based on the "OrderDate" column (most recent orders). Which approach is most efficient?**

A) Load all 5 million rows into Power Query and then use "Keep Top Rows" to keep 1,000  
B) Sort by OrderDate descending in Power Query, then use "Keep Top Rows" with 1,000 — ensuring query folding pushes this to SQL Server  
C) Write a DAX measure to filter the top 1,000 rows after import  
D) Use a report-level page filter to show only the top 1,000  

**✅ Correct Answer: B**

**Explanation:** Sorting by OrderDate descending followed by **Keep Top Rows** (1,000) in Power Query can **fold back to SQL Server** as `SELECT TOP 1000 ... ORDER BY OrderDate DESC`, meaning only 1,000 rows are transferred over the network. This is far more efficient than importing all 5 million rows and filtering client-side (A). DAX measures (C) operate after import, requiring all rows to be loaded first. Report-level page filters (D) operate on imported data and do not reduce what is imported.

---

## Question 36

**An analyst has a table where each row represents a transaction and includes a "Tags" column containing comma-separated values (e.g., "Red, Large, Sale"). They want each tag to be on its own row. What is the correct Power Query approach?**

A) Pivot the Tags column  
B) Split Column by Delimiter (comma), then Unpivot the resulting tag columns, keeping only the "Value" column  
C) Use Group By on the Tags column  
D) Use "Extract — Text Between Delimiters" repeatedly  

**✅ Correct Answer: B**

**Explanation:** The correct approach is to: (1) **Split Column by Delimiter** (comma) to create separate columns for each tag (Tag.1, Tag.2, Tag.3, etc.), (2) **Unpivot** those tag columns to create one row per tag per transaction, (3) keep or rename the value column. This correctly explodes the comma-separated list into individual rows. Pivot (A) would create columns from unique values, not rows. Group By (C) aggregates data. Extract Text Between Delimiters (D) would require knowing the exact positions and doesn't scale for variable numbers of tags.

---

## Merge & Append Queries

---

## Question 37

**A data analyst has two queries: "Orders" (containing OrderID and CustomerID) and "Customers" (containing CustomerID and CustomerName). They want to add the CustomerName to each order row. The Orders table must retain all rows even if there is no matching customer. Which merge type should they use?**

A) Inner Join  
B) Left Outer Join (Orders as left table)  
C) Right Outer Join  
D) Full Outer Join  

**✅ Correct Answer: B**

**Explanation:** A **Left Outer Join** with Orders as the left table returns all rows from Orders and matching rows from Customers. Orders with no matching CustomerID will have null values for CustomerName, but **all order rows are retained**. An Inner Join (A) would drop orders without matching customers. A Right Outer Join (C) would keep all customers, potentially losing orders. A Full Outer Join (D) keeps all rows from both tables, potentially introducing customer rows without orders, which is not required.

---

## Question 38

**A developer wants to find all rows in a "NewProducts" table that do NOT exist in an "ExistingProducts" table, based on a matching "ProductCode" column. Which merge join type achieves this?**

A) Inner Join  
B) Left Outer Join  
C) Left Anti Join  
D) Right Anti Join  

**✅ Correct Answer: C**

**Explanation:** A **Left Anti Join** returns only the rows from the left table (NewProducts) that have **no matching rows** in the right table (ExistingProducts). This is the correct operation for finding records in one table absent from another. Inner Join (A) returns only matching rows. Left Outer Join (B) returns all left rows plus matches from the right. Right Anti Join (D) would return rows in ExistingProducts not present in NewProducts — the opposite of what is needed.

---

## Question 39

**What is the key difference between "Merge Queries" and "Append Queries" in Power Query?**

A) Merge combines columns from two tables (horizontal join); Append stacks rows from two or more tables (vertical union)  
B) Merge adds rows from a second table; Append adds columns from a second table  
C) Merge only works with SQL sources; Append works with any source  
D) Merge creates a new table; Append modifies the first table in-place  

**✅ Correct Answer: A**

**Explanation:** **Merge Queries** performs a horizontal join — it matches rows between two tables on key columns and brings in columns from the second table. **Append Queries** performs a vertical union — it stacks the rows of two or more tables with the same (or compatible) column structure on top of each other. A is the precise description. B has the definitions reversed. C is incorrect (both work with any source). D is incorrect (both produce a new result set; the original queries are unchanged unless you choose "Merge/Append as New").

---

## Question 40

**A company has 12 CSV files — one per month — each with identical columns (Date, Product, Revenue). An analyst wants to combine them all into a single table. What is the fastest way to do this in Power Query?**

A) Merge all 12 queries using Inner Joins  
B) Use "Append Queries as New" and select all 12 queries  
C) Load them individually and create a DAX UNION calculated table  
D) Create a new query and use M function `Table.Combine()` referencing all 12 queries  

**✅ Correct Answer: B**

**Explanation:** **Append Queries as New** allows you to select multiple queries (including all 12 monthly queries) and stack their rows into a single combined table. This is the designed tool for combining tables with the same structure. Merging with Inner Joins (A) would require 11 sequential merge steps and would multiply columns, not stack rows. DAX UNION (C) works post-load but is less efficient and more complex. `Table.Combine()` in M (D) is technically correct and valid but requires writing M code, whereas the UI option (B) is faster and equivalent.

---

## Question 41

**When performing a Full Outer Join between Table A (100 rows) and Table B (80 rows), where 60 rows match on the key, how many rows will the result contain at minimum?**

A) 60  
B) 80  
C) 100  
D) 120  

**✅ Correct Answer: D**

**Explanation:** A **Full Outer Join** returns: all matching rows (60) + unmatched rows from Table A (100 − 60 = 40) + unmatched rows from Table B (80 − 60 = 20) = **60 + 40 + 20 = 120 rows**. A is wrong (that's Inner Join). B is wrong (that's Right Outer Join). C is wrong (that's Left Outer Join). The full outer join is the maximum possible row count since it retains all rows from both sides.

---

## Question 42

**A developer creates a query that merges a "Sales" table with a "Products" table and expands the merged column to include "ProductName" and "Category". They then reference this merged query in two other queries. When the Sales table data changes, how many queries need to be refreshed?**

A) All three queries — the merged query and both referencing queries  
B) Only the two referencing queries  
C) Only the merged query itself  
D) All queries refresh automatically only if using DirectQuery  

**✅ Correct Answer: A**

**Explanation:** In Power Query, a **referenced query** inherits the result of its source query. When the Sales data changes and the dataset is refreshed, Power Query re-executes **all queries in the dependency chain** — the merged base query AND both referencing queries — in a single refresh operation. There is no partial refresh of only downstream or only upstream queries during a standard dataset refresh. D is incorrect because Import mode also fully refreshes all queries on schedule.

---

## Question 43

**What is the difference between a query "Duplicate" and a query "Reference" in Power Query?**

A) A Duplicate creates an independent copy with all steps; a Reference creates a query that begins where the source query ends, sharing the same upstream steps  
B) A Duplicate creates a shortcut; a Reference copies all steps  
C) They are identical — the only difference is the name  
D) A Reference allows cross-dataset connections; a Duplicate does not  

**✅ Correct Answer: A**

**Explanation:** **Duplicate** creates a completely independent copy of a query with all its M code steps copied over. Changes to the original do not affect the duplicate and vice versa. **Reference** creates a new query that takes the output of the source query as its input. The referenced query shares the upstream steps and only adds its own new steps. If the source query changes, the referencing query automatically reflects the change. B has the definitions reversed. C is wrong — they behave very differently. D is wrong — both operate within the same Power BI file.

---

## Question 44

**A developer performs a Left Outer merge between "Employees" (left) and "Departments" (right) on "DepartmentID". After expanding the merged column, some rows show null in the "DepartmentName" column. What does this indicate?**

A) The merge failed because of incompatible data types  
B) Some employees have a DepartmentID that does not exist in the Departments table  
C) The Departments table has duplicate DepartmentIDs  
D) The merge should have been an Inner Join  

**✅ Correct Answer: B**

**Explanation:** In a Left Outer Join, all rows from the left table (Employees) are retained. When an employee's DepartmentID does not match any DepartmentID in the right table (Departments), the expanded columns from Departments will be **null** for those rows. This means those employees are assigned to departments that don't exist in the Departments table — a referential integrity issue. Incompatible data types (A) would cause an error, not nulls. Duplicate DepartmentIDs (C) would cause row multiplication. The nulls are expected Left Outer Join behaviour, not a reason to switch to Inner Join (D).

---

## M Code & Advanced Transformations

---

## Question 45

**A developer writes a Power Query custom column formula: `= Text.Upper([ProductName])`. Which M function category does `Text.Upper` belong to, and what does it do?**

A) Table function — converts the table column to uppercase  
B) Text function — converts the text value in [ProductName] to all uppercase letters  
C) List function — converts a list of product names to uppercase  
D) Record function — modifies the record field named ProductName  

**✅ Correct Answer: B**

**Explanation:** `Text.Upper` is a **Text function** in the M standard library that takes a single text value and returns it converted to uppercase. In a custom column formula, `[ProductName]` references the value in that column for each row, and `Text.Upper([ProductName])` returns the uppercase version of each row's product name. It operates row-by-row on scalar values, not on entire columns, tables, or lists.

---

## Question 46

**A developer wants to add a calculated column in Power Query using `Table.AddColumn`. Which M expression correctly adds a column named "TaxAmount" that is 10% of the "Price" column?**

A) `Table.AddColumn(Source, "TaxAmount", each [Price] * 0.10)`  
B) `Table.AddColumn(Source, "TaxAmount", [Price] * 0.10)`  
C) `Table.TransformColumns(Source, {"TaxAmount", each [Price] * 0.10})`  
D) `Table.AddColumn(Source, "TaxAmount", (row) => row[Price] * 0.10, type number)`  

**✅ Correct Answer: A**

**Explanation:** `Table.AddColumn(table, newColumnName, columnGenerator)` is the correct syntax. The `columnGenerator` must be a **function** — the `each` keyword is syntactic sugar for `(_) =>`, creating a function that receives each row as `_` and accesses fields via `[FieldName]`. Option A is correct and idiomatic M. Option B is missing the `each` keyword, so `[Price] * 0.10` is evaluated as an expression, not a row-level function. Option C uses `Table.TransformColumns`, which modifies existing columns, not adds new ones. Option D is also valid syntactically (and even includes the type hint) but A is the more idiomatic M code answer.

---

## Question 47

**A Power Query developer needs to accumulate a running total for a list of numbers. Which M function is designed for fold/accumulate operations over a list?**

A) `List.Sum`  
B) `List.Accumulate`  
C) `List.Generate`  
D) `Table.Group`  

**✅ Correct Answer: B**

**Explanation:** `List.Accumulate(list, seed, accumulator)` is specifically designed to **fold** a list by applying an accumulator function to a running state, producing a final accumulated value. It is the M equivalent of a left fold or reduce operation. `List.Sum` (A) only computes the total sum, not incremental/custom accumulation. `List.Generate` (C) generates a list based on a condition and next-value function (useful for iteration). `Table.Group` (D) groups table rows by a key column.

---

## Question 48

**Which M function is used to apply a transformation function to specified columns of a table, modifying their values in place?**

A) `Table.AddColumn`  
B) `Table.TransformColumns`  
C) `Table.ReplaceValue`  
D) `Table.SelectColumns`  

**✅ Correct Answer: B**

**Explanation:** `Table.TransformColumns(table, transformations)` takes a list of `{columnName, transformFunction}` pairs and applies each function to the values in the specified column, returning a new table with those columns transformed. `Table.AddColumn` (A) adds a new column without modifying existing ones. `Table.ReplaceValue` (C) finds and replaces specific values. `Table.SelectColumns` (D) selects a subset of columns to keep.

---

## Question 49

**A developer writes the following M code in a Power Query step:**
```
= Table.TransformColumns(PreviousStep, {{"Revenue", each _ * 1.15, type number}})
```
**What does this expression do?**

A) Adds 15% to every value in the Revenue column and stores the result as a number  
B) Multiplies Revenue by 1.15 only for rows where Revenue is not null  
C) Creates a new column called "Revenue" alongside the existing one  
D) Filters rows where Revenue is greater than 1.15  

**✅ Correct Answer: A**

**Explanation:** `Table.TransformColumns` with the transformation `{{"Revenue", each _ * 1.15, type number}}` applies the function `each _ * 1.15` to every value in the "Revenue" column and specifies the output type as `number`. The `_` represents each individual cell value. The result is the Revenue column with all values increased by 15%, stored as numbers. It operates on **all rows** without a null check (B). It modifies the existing column in-place, not creating a new one (C). It does not filter rows (D).

---

## Query Folding & Incremental Refresh

---

## Question 50

**What is "query folding" in Power Query, and why is it important?**

A) Collapsing multiple Power Query steps into a single step for readability  
B) The process by which Power Query translates transformation steps into a native query (e.g., SQL) executed at the data source, reducing data transfer  
C) A feature that merges multiple queries into one query file  
D) Compressing Power Query output before loading to the VertiPaq engine  

**✅ Correct Answer: B**

**Explanation:** **Query folding** is the mechanism by which the Power Query engine translates M transformation steps into an equivalent **native query** (SQL, OData, etc.) that is pushed down to and executed by the data source. This means filtering, sorting, grouping, and joining happen at the source — only the resulting, smaller dataset is transferred to Power BI, dramatically improving performance and reducing network load. It has nothing to do with collapsing M steps visually (A), merging query files (C), or VertiPaq compression (D).

---

## Question 51

**A developer applies the following Power Query steps to a SQL Server source: Filter Rows → Sort → Keep Top Rows → Add Custom Column (using `Text.Upper`). After which step does query folding likely break?**

A) Filter Rows  
B) Sort  
C) Keep Top Rows  
D) Add Custom Column using `Text.Upper`  

**✅ Correct Answer: D**

**Explanation:** **Filter Rows, Sort, and Keep Top Rows** all have native SQL equivalents (WHERE, ORDER BY, TOP) and can typically be folded to SQL Server. However, **`Text.Upper` in an Add Custom Column** is an M-specific function with no direct SQL equivalent. Once Power Query encounters a step that cannot be folded (like an M function call with no SQL translation), **all subsequent steps also lose folding**. Therefore, query folding breaks at the `Text.Upper` custom column step. The preceding three steps (A, B, C) can still fold if query folding is still active up to that point.

---

## Question 52

**A developer wants to configure incremental refresh for a large fact table in Power BI. Which two Power Query parameters are required as a prerequisite for incremental refresh configuration?**

A) `StartDate` and `EndDate` — both of type DateTime  
B) `RangeStart` and `RangeEnd` — both of type DateTime  
C) `IncrementalStart` and `IncrementalEnd` — both of type Date  
D) `RefreshStart` and `RefreshEnd` — both of type Text  

**✅ Correct Answer: B**

**Explanation:** Incremental refresh in Power BI requires exactly two Power Query parameters named **`RangeStart`** and **`RangeEnd`** (case-sensitive), both of **DateTime** data type. These parameters are used to filter the date/time column in the Power Query step — Power BI uses them to determine which historical partitions to archive and which recent partitions to refresh. Any other names (A, C, D) will not be recognised by the incremental refresh feature in Power BI.

---

## Question 53

**When incremental refresh is enabled on a dataset, what happens to historical data partitions that fall outside the refresh window?**

A) They are deleted from the dataset on the next refresh  
B) They are archived and retained without being re-queried during refresh, saving refresh time  
C) They are moved to cold storage in Azure  
D) They are exported to a separate Power BI file  

**✅ Correct Answer: B**

**Explanation:** Incremental refresh partitions the dataset's table into a **rolling window of partitions**. Historical partitions (older than the refresh range) are **frozen and retained** in the dataset — they are not re-queried against the source on each refresh. Only the recent "incremental" partitions are refreshed. This drastically reduces refresh time and source system load for large historical tables. Data is not deleted (A), moved to external storage (C), or exported (D).

---

## Privacy Levels & Parameters

---

## Question 54

**A Power Query developer has two data sources: a public-facing weather API (marked as Public) and a confidential internal HR database (marked as Private). They want to merge data from both. What will Power BI do?**

A) Allow the merge freely because one source is public  
B) Block the merge and show a formula firewall error, because Private data cannot be combined with other sources without explicit privacy-level configuration  
C) Automatically mark the merged result as Private  
D) Prompt the user to export the HR data to a public location first  

**✅ Correct Answer: B**

**Explanation:** Power Query's **formula firewall** prevents data from a **Private** source from being sent to or combined with less-trusted sources without the user's explicit awareness. Merging a Private HR database with a Public API would risk leaking sensitive data, so Power BI raises a **Formula Firewall / Privacy** error. To resolve this, the developer must review the privacy settings and either change the HR source's privacy level to "Organizational" with awareness of the implications, or adjust how the merge is structured. A is wrong — privacy levels apply regardless of the other source's level. C and D describe non-existent automatic behaviors.

---

## Question 55

**A Power Query parameter of type "List" is used to provide a dropdown of allowed values in Power BI Desktop. A developer wants report editors (not just the developer) to be able to change parameter values when editing the report in Power BI Desktop. Where can report editors modify Power Query parameter values without opening the Advanced Editor?**

A) Data tab → Refresh All  
B) Home tab → Edit Queries → Edit Parameters  
C) Report view → Format pane → Parameters  
D) File → Options and Settings → Data source settings  

**✅ Correct Answer: B**

**Explanation:** In Power BI Desktop, report editors can change Power Query parameter values by going to **Home tab → Edit Queries → Edit Parameters** (or **Transform Data → Edit Parameters** depending on the version). This opens a dialog showing all parameters with their current values, allowed values (if a list is defined), and input fields for changing them — no need to open the Advanced Editor or write M code. The Refresh All button (A) only refreshes data. The Format pane (C) controls visual formatting, not query parameters. Data source settings (D) manages credentials and source paths, not parameter values.

---

*End of Section 1 — Prepare the Data (55 Questions)*

---

> **Study Tip:** For the real PL-300 exam, focus on understanding **when** to use each connection mode, how query folding affects performance, and the practical differences between transformation operations. Hands-on practice in Power BI Desktop is the best preparation.
