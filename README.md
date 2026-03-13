# PL-300: Microsoft Power BI Data Analyst Associate – Complete Study Repository

> **Single-source preparation guide** for the Microsoft PL-300 certification exam. Everything you need to pass — study notes, 200+ practice questions, case studies, DAX reference, and sample data files.

---

## 📋 Exam Overview

| Detail | Information |
|---|---|
| **Exam Code** | PL-300 |
| **Full Name** | Microsoft Power BI Data Analyst Associate |
| **Duration** | 120 minutes |
| **Number of Questions** | 40–60 questions |
| **Passing Score** | 700 / 1000 |
| **Exam Fee** | $165 USD |
| **Languages** | English, Japanese, Chinese (Simplified), Korean, German, French, Spanish, Portuguese (Brazil), Italian, Chinese (Traditional), Dutch |
| **Skills Measured Version** | January 2026 |
| **Certification Validity** | Renewed annually |

---

## 📊 Skills Measured & Weightings

| Domain | Weight |
|---|---|
| **1. Prepare the Data** | 25–30% |
| **2. Model the Data** | 25–30% |
| **3. Visualize and Analyze the Data** | 25–30% |
| **4. Manage and Secure Power BI** | 15–20% |

### Domain 1 – Prepare the Data (25–30%)
- Get or connect to data sources
- Profile and clean the data
- Transform and load the data

### Domain 2 – Model the Data (25–30%)
- Design and implement a data model
- Create model calculations using DAX
- Optimize model performance

### Domain 3 – Visualize and Analyze the Data (25–30%)
- Create reports
- Enhance reports for usability and storytelling
- Identify patterns and trends

### Domain 4 – Manage and Secure Power BI (15–20%)
- Create and manage workspaces and assets
- Manage semantic models
- Secure and govern data

---

## 📁 Repository Structure

```
PL-300-prep/
│
├── README.md                          ← You are here
│
├── study-guides/
│   ├── 01-prepare-the-data.md         ← Domain 1 comprehensive notes
│   ├── 02-model-the-data.md           ← Domain 2 comprehensive notes
│   ├── 03-visualize-and-analyze-data.md ← Domain 3 comprehensive notes
│   └── 04-manage-and-secure-powerbi.md  ← Domain 4 comprehensive notes
│
├── practice-questions/
│   ├── section1-prepare-data-mcq.md   ← 50+ MCQ: Prepare Data
│   ├── section2-model-data-mcq.md     ← 50+ MCQ: Model Data
│   ├── section3-visualize-analyze-mcq.md ← 50+ MCQ: Visualize & Analyze
│   ├── section4-manage-secure-mcq.md  ← 50+ MCQ: Manage & Secure
│   └── mixed-exam-simulation.md       ← 60-question full mock exam
│
├── case-studies/
│   ├── case-study-1-retail-sales.md   ← Retail analytics scenario
│   ├── case-study-2-hr-analytics.md   ← HR performance scenario
│   ├── case-study-3-financial-reporting.md ← Finance budgeting scenario
│   └── case-study-4-supply-chain.md   ← Supply chain scenario
│
├── dax-reference/
│   ├── dax-cheat-sheet.md             ← Complete DAX function reference
│   └── dax-patterns.md                ← Common DAX patterns with code
│
├── power-query-reference/
│   └── m-code-reference.md            ← Power Query M code reference
│
├── practice-database/
│   ├── README.md                      ← How to use practice data
│   ├── sales-data.csv                 ← ~100 rows sales transactions
│   ├── products-data.csv              ← ~30 products
│   ├── stores-data.csv                ← ~10 stores
│   ├── customers-data.csv             ← ~50 customers
│   ├── dates-data.csv                 ← Date dimension 2023–2025
│   ├── budget-data.csv                ← Budget/forecast data
│   └── employees-data.csv             ← HR employee data
│
└── exam-tips/
    ├── exam-strategy.md               ← Time management & exam tips
    └── quick-revision.md              ← Last-night revision cheat sheet
```

---

## 🗓️ 8-Week Study Plan

| Week | Focus Domain | Topics | Recommended Files |
|---|---|---|---|
| **Week 1** | Prepare the Data | Power Query, data sources, DirectQuery vs Import | `study-guides/01-prepare-the-data.md`, `power-query-reference/m-code-reference.md` |
| **Week 2** | Prepare the Data – Practice | Data profiling, cleaning, transformations, parameters | `practice-questions/section1-prepare-data-mcq.md`, Practice in Power BI Desktop |
| **Week 3** | Model the Data | Star schema, relationships, calculated columns, measures | `study-guides/02-model-the-data.md`, `dax-reference/dax-cheat-sheet.md` |
| **Week 4** | Model the Data – DAX Deep Dive | CALCULATE, time intelligence, optimization | `dax-reference/dax-patterns.md`, `practice-questions/section2-model-data-mcq.md` |
| **Week 5** | Visualize and Analyze | Report design, visuals, Copilot, formatting | `study-guides/03-visualize-and-analyze-data.md` |
| **Week 6** | Visualize and Analyze – Practice | Bookmarks, drill-through, AI visuals, paginated reports | `practice-questions/section3-visualize-analyze-mcq.md` |
| **Week 7** | Manage and Secure Power BI | Workspaces, RLS, gateways, governance | `study-guides/04-manage-and-secure-powerbi.md`, `practice-questions/section4-manage-secure-mcq.md` |
| **Week 8** | Full Review & Mock Exam | All domains, case studies, timed practice exam | `practice-questions/mixed-exam-simulation.md`, all `case-studies/`, `exam-tips/` |

### Weekly Goals Detail

**Week 1 – Prepare the Data (Fundamentals)**
- Understand Power Query Editor interface
- Learn data source connection types (File, Database, Online Services, Other)
- Master DirectQuery vs Import vs Live Connection vs Composite models
- Practice creating and editing queries using M code
- Complete hands-on exercises with `practice-database/sales-data.csv`

**Week 2 – Prepare the Data (Practice)**
- Data profiling: column distribution, column quality, column profile
- Data cleaning: removing duplicates, handling nulls, fixing errors
- Transformations: pivot, unpivot, merge, append, group by
- Parameters and data source settings
- Attempt Section 1 MCQs (target: 80%+ correct)

**Week 3 – Model the Data (Fundamentals)**
- Build star schema with `practice-database/` files in Power BI Desktop
- Create all relationship types with correct cardinality
- Understand cross-filter direction (single vs bidirectional)
- Write basic DAX: SUM, AVERAGE, COUNT, DISTINCTCOUNT
- Role-playing dimensions with inactive relationships

**Week 4 – Model the Data (DAX Deep Dive)**
- Master CALCULATE and filter context
- Time intelligence: TOTALYTD, SAMEPERIODLASTYEAR, DATEADD
- Calculated columns vs measures vs calculated tables
- Performance optimization with Performance Analyzer
- Calculation groups and dynamic measures
- Attempt Section 2 MCQs (target: 80%+ correct)

**Week 5 – Visualize and Analyze (Fundamentals)**
- Choose appropriate visuals for different data types
- Apply conditional formatting
- Use Copilot for narrative visuals and report pages
- Configure report page settings, themes, bookmarks
- Design for accessibility and mobile layout

**Week 6 – Visualize and Analyze (Practice)**
- Create bookmarks and navigation buttons
- Configure drill-through and tooltip pages
- Sync slicers across pages
- Use AI visuals: Q&A, Key Influencers, Decomposition Tree
- Explore anomaly detection and forecasting
- Attempt Section 3 MCQs (target: 80%+ correct)

**Week 7 – Manage and Secure Power BI**
- Create and configure workspaces
- Set up row-level security (static and dynamic RLS)
- Configure data gateways (on-premises and VNET)
- Set up scheduled refresh and alerts
- Apply sensitivity labels and manage endorsements
- Attempt Section 4 MCQs (target: 80%+ correct)

**Week 8 – Full Review and Mock Exam**
- Complete all 4 case studies
- Take the 60-question mock exam under timed conditions (120 minutes)
- Review weak areas using study guides
- Read `exam-tips/quick-revision.md` the night before the exam
- Re-attempt questions you got wrong

---

## 🎯 How to Use This Repository

### Step 1: Set Up Your Environment
1. Download and install **Power BI Desktop** (free): https://powerbi.microsoft.com/desktop
2. Sign up for a **Power BI service** account (free tier available)
3. Clone or download this repository

### Step 2: Load the Practice Data
1. Open Power BI Desktop
2. Click **Get Data → Text/CSV**
3. Load all files from `practice-database/` folder
4. Follow the `practice-database/README.md` for relationship setup instructions

### Step 3: Follow the Study Plan
- Read each study guide in order
- Practice with Power BI Desktop as you learn
- Test yourself with MCQs after each domain
- Complete case studies to apply knowledge

### Step 4: Practice MCQs
- Complete section MCQs after studying each domain
- Aim for 80%+ before moving on
- Review explanations for all incorrect answers
- Take the `mixed-exam-simulation.md` as a final timed test

### Step 5: Final Preparation
- Complete all case studies
- Review `exam-tips/quick-revision.md`
- Read `exam-tips/exam-strategy.md` the week before
- Schedule your exam when consistently scoring 80%+ on mock exams

---

## 📚 Official Microsoft Resources

| Resource | Link |
|---|---|
| Official Exam Page | https://learn.microsoft.com/en-us/credentials/certifications/data-analyst-associate/ |
| Skills Measured (Jan 2026) | https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/pl-300 |
| Microsoft Learn Learning Path | https://learn.microsoft.com/en-us/training/paths/prepare-data-power-bi/ |
| Power BI Documentation | https://learn.microsoft.com/en-us/power-bi/ |
| DAX Guide | https://dax.guide/ |
| Power BI Community | https://community.powerbi.com/ |
| Sample Datasets | https://learn.microsoft.com/en-us/power-bi/create-reports/sample-datasets |
| Power BI YouTube Channel | https://www.youtube.com/c/MSPowerBI |
| Practice Assessment | https://learn.microsoft.com/en-us/credentials/certifications/practice-assessments-for-microsoft-certifications |

---

## ✅ Quick Pre-Exam Checklist

### Domain 1 – Prepare the Data
- [ ] Can explain difference between Import, DirectQuery, Live Connection, Composite
- [ ] Know when to use Merge vs Append
- [ ] Can write basic M code transformations
- [ ] Understand privacy levels (Public, Organizational, Private)
- [ ] Know data profiling tools (column quality, distribution, profile)
- [ ] Can create and use parameters

### Domain 2 – Model the Data
- [ ] Can build a star schema with proper relationships
- [ ] Understand all cardinality types (1:1, 1:M, M:M)
- [ ] Can write CALCULATE with filter arguments
- [ ] Know time intelligence functions (TOTALYTD, SAMEPERIODLASTYEAR, DATEADD)
- [ ] Understand difference between calculated columns and measures
- [ ] Know when to use bidirectional cross-filtering

### Domain 3 – Visualize and Analyze
- [ ] Can choose appropriate visual for each scenario
- [ ] Know how to create bookmarks and navigation
- [ ] Understand drill-through vs drill-down
- [ ] Can configure row-level tooltips
- [ ] Know AI visuals: Key Influencers, Decomposition Tree, Q&A
- [ ] Understand Copilot features in Power BI

### Domain 4 – Manage and Secure Power BI
- [ ] Know workspace roles (Admin, Member, Contributor, Viewer)
- [ ] Can implement static and dynamic RLS
- [ ] Know gateway types and when to use each
- [ ] Understand scheduled refresh configuration
- [ ] Know sensitivity label hierarchy
- [ ] Understand deployment pipelines

---

## 🏷️ Content Updates

This repository is aligned with the **PL-300 Skills Measured document (January 2026 update)**, which includes:
- ✅ Copilot features for narrative visuals
- ✅ Copilot for creating report pages
- ✅ Copilot for semantic model summary
- ✅ Visual calculations with DAX
- ✅ Calculation groups
- ✅ Updated workspace and governance features

---

## 📝 License

This study repository is for educational purposes. All content is original and created to help candidates prepare for the PL-300 exam. Microsoft, Power BI, and related trademarks are property of Microsoft Corporation.

---

*Good luck on your exam! 🎓 Remember: consistent practice with Power BI Desktop is the key to success.*
