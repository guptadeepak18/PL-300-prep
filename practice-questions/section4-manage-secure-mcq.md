# Section 4: Manage and Secure Power BI — Practice Questions

**Domain Weight:** ~25% of the PL-300 exam  
**Question Count:** 55 Multiple Choice Questions  
**Topics:** Workspaces, Roles, Deployment Pipelines, Gateways, Refresh, RLS, Sensitivity Labels, Sharing, Governance

---

## Question 1

**Your organization wants to create a collaborative workspace where multiple developers can publish reports and semantic models, configure scheduled refresh, and manage app settings. Which Power BI workspace role allows ALL of these actions?**

A) Viewer  
B) Contributor  
C) Member  
D) Admin  

**✅ Correct Answer: C**

**Explanation:** The **Member** role in a Power BI workspace can publish content, configure scheduled refresh, create and publish workspace apps, and manage most content settings. **Admin** has all Member permissions plus the ability to add/remove members, delete the workspace, and update workspace settings. **Contributor** can publish and edit content but cannot manage app settings or publish the workspace app. **Viewer** can only view published content. Both Member and Admin fit the scenario, but Member is the minimum sufficient role.

---

## Question 2

**Which workspace role is the MINIMUM required to add new members to a Power BI workspace?**

A) Contributor  
B) Member  
C) Admin  
D) Viewer  

**✅ Correct Answer: C**

**Explanation:** Only the **Admin** role can add or remove workspace members, change member roles, and delete the workspace. **Members** can perform most content operations but cannot manage workspace membership. **Contributors** and **Viewers** have even more restricted permissions. This is an important exam distinction — always remember that workspace membership management is an Admin-exclusive action.

---

## Question 3

**A team member needs to view reports and dashboards in a Power BI workspace but should NOT be able to edit, publish, or share content. Which role should they be assigned?**

A) Contributor  
B) Member  
C) Viewer  
D) Reader  

**✅ Correct Answer: C**

**Explanation:** The **Viewer** role provides read-only access to the workspace — users can view reports, dashboards, and dataflows, but cannot edit content, publish reports, or share items further. There is no "Reader" role in Power BI workspaces (that is an Azure RBAC term). Contributor allows publishing and editing. Member allows those plus app management. Viewer is precisely the read-only role required.

---

## Question 4

**A Contributor in a Power BI workspace wants to publish a new workspace app so that consumers can access curated content. Can a Contributor do this?**

A) Yes, Contributors can create and publish workspace apps  
B) No, only Members and Admins can publish workspace apps  
C) No, only Admins can publish workspace apps  
D) Yes, but only if an Admin has first published a version of the app  

**✅ Correct Answer: B**

**Explanation:** Publishing a **workspace app** requires at minimum a **Member** role. **Contributors** can create and edit reports and semantic models within the workspace but cannot publish the workspace app. **Admins** can also publish apps. The distinction between Contributor and Member is a frequently tested exam point — Contributor = can edit content, Member = can edit content AND manage the app.

---

## Question 5

**Your organization has created a Power BI workspace app and granted access to 500 business users. A user with app access opens the app but cannot see the underlying workspace in the workspace list. Is this expected behavior?**

A) No — app access should automatically grant workspace Viewer access  
B) Yes — app access and workspace access are independent; users can access the app without seeing the workspace  
C) No — users must be workspace Viewers to access the app  
D) Yes — but only if the workspace is on Premium capacity  

**✅ Correct Answer: B**

**Explanation:** **App access and workspace access are completely separate in Power BI.** Users granted access to a workspace app do not need (and do not automatically receive) workspace membership. They access the curated app experience through the app URL or Apps section. This is the intended design — workspace access is for content creators/administrators, while app access is for consumers. This separation is fundamental to Power BI's content distribution model.

---

## Question 6

**You are setting up a deployment pipeline in Power BI. What are the three stages in a standard deployment pipeline?**

A) Author → Review → Publish  
B) Development → Test → Production  
C) Bronze → Silver → Gold  
D) Source → Staging → Live  

**✅ Correct Answer: B**

**Explanation:** Power BI deployment pipelines have three standard stages: **Development** (where reports and models are built and tested by developers), **Test** (where content is validated by QA or business users), and **Production** (where finalized, approved content is available to end consumers). Each stage maps to a separate workspace. The other options describe DevOps or data engineering patterns not specific to Power BI deployment pipelines.

---

## Question 7

**Which of the following is TRUE about Power BI deployment pipelines?**

A) Deployment pipelines can only be used with Premium capacity workspaces  
B) When you deploy content to the next stage, all workspace members are also copied to the next stage's workspace  
C) Deployment pipelines support selective deployment — you can choose which items to deploy rather than deploying everything  
D) Deployment pipelines automatically handle data source credentials for each stage  

**✅ Correct Answer: C**

**Explanation:** Deployment pipelines support **selective deployment**, allowing you to choose specific reports, semantic models, or other items to deploy to the next stage rather than deploying all workspace content. Workspace members and permissions are NOT copied between stages — each stage's workspace has independently managed access. Data source credentials must be configured separately for each stage. Deployment pipelines do require a Fabric/Premium capacity workspace.

---

## Question 8

**What is the key difference between a Power BI Dashboard and a Power BI Report?**

A) Dashboards support drill-through; reports do not  
B) Dashboards are single-page canvases with pinned tiles from multiple reports/datasets; reports can be multi-page with interactive visuals  
C) Reports can only have 10 visuals; dashboards have no limit  
D) Dashboards require Premium; reports do not  

**✅ Correct Answer: B**

**Explanation:** **Dashboards** are single-page canvases in Power BI Service where you pin tiles from multiple reports, datasets, or external sources. They provide a consolidated monitoring view. **Reports** are multi-page, interactive documents with rich visuals, cross-filtering, and drill capabilities built in Desktop or the Service. Reports support drill-through natively; dashboards support drill-through by linking tiles to underlying reports. Neither requires Premium by itself.

---

## Question 9

**A dashboard tile shows daily units sold. A manager wants to receive an email notification when units sold exceed 10,000 in a day. How should this be configured?**

A) Set a scheduled refresh alert on the semantic model  
B) Create a Power Automate flow triggered by a Power BI dataset  
C) Set a data alert on the dashboard tile  
D) Use the Anomaly Detection feature on the underlying report visual  

**✅ Correct Answer: C**

**Explanation:** **Data alerts** on dashboard tiles allow users to set threshold conditions (greater than, less than) on KPI, card, and gauge tiles. When the threshold is crossed during a data refresh, the user receives an email notification and/or Power BI notification. Data alerts are configured directly on the dashboard tile by clicking the ellipsis (...) and selecting "Manage alerts." Power Automate can also be used but is not the native built-in feature for this scenario.

---

## Question 10

**Which types of dashboard tiles support data alerts in Power BI?**

A) Any tile type including bar charts, line charts, and tables  
B) Only tiles showing a single numeric value: Card, KPI, and Gauge tiles  
C) Only tiles pinned from Premium capacity reports  
D) Only tiles created from streaming datasets  

**✅ Correct Answer: B**

**Explanation:** **Data alerts** in Power BI are only supported on **Card**, **KPI**, and **Gauge** dashboard tiles — tiles that display a single numeric value. Complex visuals like bar charts, line charts, tables, or maps do not support data alerts because they show multiple data points and there is no single threshold concept to trigger on. This is a commonly tested exam detail.

---

## Question 11

**A business user wants to receive a weekly PDF email of a Power BI report every Monday at 8 AM. Which Power BI feature enables this?**

A) Data alerts  
B) Report subscriptions  
C) Scheduled refresh  
D) Export to PDF via Power Automate  

**✅ Correct Answer: B**

**Explanation:** **Report subscriptions** (Subscribe to report) allow users to schedule recurring email deliveries of report pages or dashboards as PNG images or PDF attachments. You can set the day, time, frequency, and recipient list. Data alerts are threshold-based, not schedule-based. Scheduled refresh updates the data in the semantic model but doesn't send reports via email. Power Automate can do this but is not the built-in native feature asked about.

---

## Question 12

**Which of the following endorsement types requires a designated administrator to certify content in Power BI?**

A) Promoted  
B) Certified  
C) Verified  
D) Featured  

**✅ Correct Answer: B**

**Explanation:** **Certification** is the higher endorsement tier that requires a designated **certification authority** (configured by Power BI admins in tenant settings). Only users with the certification permission can certify semantic models, reports, dataflows, or apps. **Promoted** endorsement can be applied by any workspace admin or member without a central authority — it's self-service. "Verified" and "Featured" are not Power BI endorsement types.

---

## Question 13

**What is the PRIMARY purpose of promoting content in Power BI (using the "Promoted" endorsement)?**

A) To prevent users from modifying certified content  
B) To signal to other users that the content is high-quality and trustworthy, making it discoverable  
C) To automatically grant all workspace users access to the promoted content  
D) To publish the content to the Microsoft AppSource marketplace  

**✅ Correct Answer: B**

**Explanation:** **Endorsements** (Promoted and Certified) are discoverability signals — they appear as badges on content items and help users identify which semantic models, reports, or dataflows are reliable and recommended for use. Promoted means the workspace owner/member has flagged it as a good source. Certified adds organizational authority. Endorsements do not change permissions, prevent modifications, or publish to AppSource.

---

## Question 14

**A report in Power BI Service uses an on-premises SQL Server database as its data source and uses Import mode. The semantic model needs to refresh twice daily. Which component is REQUIRED?**

A) A virtual network (VNet) gateway  
B) An on-premises data gateway in standard mode  
C) An on-premises data gateway in personal mode  
D) No gateway is required; Power BI can connect directly to on-premises sources  

**✅ Correct Answer: B**

**Explanation:** An **on-premises data gateway in standard mode** is required to enable Power BI Service to reach on-premises data sources (like SQL Server) for scheduled refresh. Standard mode supports multiple users, multiple data sources, and enterprise scenarios. **Personal mode** is for individual use and cannot be shared across reports or users — it also cannot be used when the semantic model is shared with others. Power BI Service cannot connect to on-premises sources without a gateway. VNet gateways are for cloud-only scenarios with private network services.

---

## Question 15

**What is the key difference between an on-premises data gateway in "standard mode" and "personal mode"?**

A) Standard mode supports DirectQuery; personal mode only supports Import  
B) Standard mode is shared across multiple users and reports; personal mode is single-user only and cannot be shared  
C) Personal mode supports cloud data sources; standard mode only supports on-premises sources  
D) Standard mode requires Premium capacity; personal mode does not  

**✅ Correct Answer: B**

**Explanation:** The core difference is **sharing and multi-user support**: **Standard mode** (enterprise gateway) is installed on a server, can be managed by multiple admins, and can serve many users, reports, and semantic models simultaneously. **Personal mode** is installed on a single user's machine, only accessible to that user, and cannot be used when the report/model is shared with others. Both modes support on-premises sources. Neither mode requires Premium by itself.

---

## Question 16

**For which scenario is a Virtual Network (VNet) data gateway MOST appropriate?**

A) Connecting to an on-premises SQL Server in a corporate data center  
B) Connecting to an Azure SQL Database that is protected by a private endpoint and not publicly accessible  
C) Refreshing a personal semantic model on a user's local machine  
D) Publishing paginated reports to Power BI Premium  

**✅ Correct Answer: B**

**Explanation:** A **VNet data gateway** enables Power BI Service to securely connect to data sources within an **Azure Virtual Network** — such as Azure SQL Database, Azure Synapse, or other Azure PaaS services that are configured with private endpoints and no public internet access. It runs entirely in Azure (no on-premises machine needed). For true on-premises sources (in a corporate data center), a standard on-premises data gateway is required.

---

## Question 17

**A Power BI Pro user has a semantic model (Import mode) connected to an on-premises SQL Server. How many times per day can they schedule a refresh with a standard gateway?**

A) 8 times per day  
B) 24 times per day  
C) 48 times per day  
D) Unlimited  

**✅ Correct Answer: A**

**Explanation:** With a **Power BI Pro** license, the scheduled refresh limit is **8 times per day (every 3 hours)**. With a **Power BI Premium** capacity (P1 and above) or Fabric capacity, the limit increases to **48 times per day (every 30 minutes)**. This is a frequently tested exam detail. The difference in refresh frequency between Pro and Premium is a common decision factor for capacity planning.

---

## Question 18

**A Power BI Premium capacity workspace has a semantic model with scheduled refresh configured. What is the maximum number of scheduled refreshes per day in Premium?**

A) 8  
B) 24  
C) 48  
D) 96  

**✅ Correct Answer: C**

**Explanation:** **Power BI Premium** capacity (and Fabric Capacities F64+) supports up to **48 scheduled refreshes per day**, compared to 8 for Pro. This allows refreshes as frequently as every 30 minutes. This Premium refresh limit increase is one of the key Premium benefits relevant to the PL-300 exam. Some sources note that with API-driven refresh, frequency can be higher, but 48 is the standard scheduled refresh limit for Premium.

---

## Question 19

**What is the purpose of incremental refresh in Power BI?**

A) To refresh only the visuals that changed on a report page  
B) To load only new or modified rows from the data source rather than reloading the entire dataset on every refresh  
C) To automatically detect changes in the data source and trigger a refresh  
D) To refresh semantic models at shorter intervals than the standard scheduled refresh  

**✅ Correct Answer: B**

**Explanation:** **Incremental refresh** is a data loading optimization that partitions data by date/time and only refreshes the most recent partitions (new/changed rows) during each refresh cycle, leaving historical partitions unchanged. This dramatically reduces refresh time and resource usage for large tables. It requires a date/time column for partitioning and must be configured in Power BI Desktop before publishing. It is not about visual refresh, change detection, or refresh intervals.

---

## Question 20

**What are the TWO date/time parameters required when configuring incremental refresh in Power BI Desktop?**

A) StartDate and EndDate  
B) RangeStart and RangeEnd  
C) DateFrom and DateTo  
D) MinDate and MaxDate  

**✅ Correct Answer: B**

**Explanation:** Incremental refresh in Power BI Desktop requires creating two **Power Query parameters** named exactly **RangeStart** (type: Date/Time) and **RangeEnd** (type: Date/Time). These parameter names are case-sensitive and must match exactly. They are then used to filter the date column in the data source query, and Power BI automatically manages the partition refresh logic based on these parameters.

---

## Question 21

**You are implementing Row-Level Security (RLS) to ensure that each regional sales manager can only see data for their own region. The Region column values are: North, South, East, West. You create four roles, each with a DAX filter like [Region] = "North". What type of RLS is this?**

A) Dynamic RLS  
B) Object-level security  
C) Static RLS  
D) Column-level security  

**✅ Correct Answer: C**

**Explanation:** **Static RLS** uses hardcoded DAX filter expressions in each role to restrict data — e.g., [Region] = "North" for the North role. Each user must be manually added to the appropriate role. **Dynamic RLS** uses the DAX `USERNAME()` or `USERPRINCIPALNAME()` function to dynamically filter data based on the logged-in user's identity, typically by joining to a security mapping table. Static RLS is simple but requires manual role maintenance as org changes occur.

---

## Question 22

**You want to implement dynamic RLS where each employee can only see data matching their email address in a security mapping table. Which DAX function returns the current user's email address in Power BI Service?**

A) USERNAME()  
B) USERPRINCIPALNAME()  
C) CURRENTUSER()  
D) USEREMAILADDRESS()  

**✅ Correct Answer: B**

**Explanation:** **USERPRINCIPALNAME()** returns the UPN (User Principal Name) of the currently authenticated user in Power BI Service, which is typically their email address (e.g., john.doe@company.com). **USERNAME()** returns "DOMAIN\username" format in some environments, and in Power BI Service it may return the email — but USERPRINCIPALNAME() is the reliable and recommended function for email-based dynamic RLS. CURRENTUSER() and USEREMAILADDRESS() are not valid Power BI DAX functions.

---

## Question 23

**After publishing a semantic model with RLS roles to Power BI Service, which role grants workspace members the ability to manage RLS group assignments (adding/removing users to roles) without giving them full Admin access?**

A) There is no such specific role; only Admins can manage RLS  
B) Members and Admins can manage RLS role memberships in the Service  
C) Contributors can manage RLS role memberships  
D) Any workspace role can manage RLS  

**✅ Correct Answer: B**

**Explanation:** In Power BI Service, **Admins and Members** of the workspace can manage RLS role memberships (Security > Manage roles) for semantic models in that workspace. **Contributors** can build and edit content but cannot manage security role memberships. **Viewers** have no admin access. This is an important exam detail — knowing which roles can manage RLS in the service vs. which roles can define RLS in Desktop is key.

---

## Question 24

**A report author wants to test that their RLS configuration correctly restricts data for the "Sales_East" role before publishing. Which feature in Power BI Desktop allows this?**

A) Preview data in Power Query Editor  
B) "View as" / "Test as role" in the Modeling tab  
C) Create a test user in Azure Active Directory and sign in  
D) Use the Security dialog to enable role testing mode  

**✅ Correct Answer: B**

**Explanation:** In Power BI Desktop, the **Modeling tab > Manage roles > "View as"** (also called "Test as role") feature allows you to preview the report as if you were assigned to a specific RLS role, or as a specific user. This lets you verify that data is correctly filtered before publishing. In Power BI Service, a similar "Test as role" feature exists in the semantic model's Security settings. Creating a test AAD user is unnecessary and impractical for this purpose.

---

## Question 25

**Which of the following statements about RLS and workspace roles is CORRECT?**

A) Workspace Admins are subject to RLS filters when viewing reports  
B) Workspace Members, Contributors, and Admins bypass RLS by default — RLS only applies to Viewers and users with Build permission  
C) RLS is applied to all users regardless of their workspace role  
D) RLS only applies to users outside the workspace (external users)  

**✅ Correct Answer: B**

**Explanation:** **Workspace members with edit permissions (Admin, Member, Contributor) bypass RLS** — they can see all data in the semantic model. **RLS applies to users who access the semantic model via the Viewer role or via shared/published report access (Build permission)**. This is a critical exam concept: if you want to test RLS, you must use the "Test as role" feature or view the report as a user who is subject to RLS (e.g., a Viewer or an external user with only Build permission).

---

## Question 26

**Your organization is required by compliance policy to classify all Power BI content with sensitivity labels. A report is created in Power BI Desktop from a certified semantic model that already has a "Confidential" sensitivity label. What happens to the report's label?**

A) The report inherits the "Confidential" label automatically from the semantic model  
B) The report has no label until one is manually applied  
C) The report is automatically labeled "Internal" by default  
D) Power BI blocks the report creation because the source has a Confidential label  

**✅ Correct Answer: A**

**Explanation:** Power BI supports **sensitivity label inheritance** — when a report or artifact is created from a labeled semantic model, it inherits the semantic model's sensitivity label automatically. This inheritance flows downstream through the data lineage (semantic model → report → export). This is powered by Microsoft Purview Information Protection integration. Label inheritance ensures compliance without requiring manual labeling at each step.

---

## Question 27

**Sensitivity labels in Power BI are configured and managed through which Microsoft service?**

A) Microsoft Defender for Cloud  
B) Microsoft Purview (formerly Azure Information Protection / Microsoft Information Protection)  
C) Microsoft Endpoint Manager  
D) Azure Active Directory  

**✅ Correct Answer: B**

**Explanation:** Sensitivity labels in Power BI come from **Microsoft Purview** (which encompasses the former Azure Information Protection and Microsoft Information Protection). Labels are created and managed in the Microsoft Purview compliance portal, and when applied in Power BI, they use the same label policies defined organization-wide. Admins enable the integration in Power BI tenant settings. This is not part of Defender for Cloud, Endpoint Manager, or AAD directly.

---

## Question 28

**What happens when a Power BI report with a sensitivity label is exported to Excel or PDF?**

A) The sensitivity label is stripped from the exported file  
B) The exported file inherits the same sensitivity label, and label-based protections (e.g., encryption) are applied  
C) The export is blocked if the label is higher than "Internal"  
D) A new label is applied based on the export format  

**✅ Correct Answer: B**

**Explanation:** When a labeled Power BI artifact is exported (to Excel, PDF, PowerPoint, or CSV), the **sensitivity label carries over to the exported file** and label-based protections such as encryption, watermarking, and access restrictions are applied to the export. For example, a "Highly Confidential" label that enforces encryption in Office files will cause the exported Excel file to be encrypted and require authorized access. This is a key compliance feature of the Purview integration.

---

## Question 29

**A user is given "Build" permission on a Power BI semantic model. Which of the following actions can they perform?**

A) Schedule refresh of the semantic model  
B) Share the semantic model with other users  
C) Connect to the semantic model from Excel or Power BI Desktop and create new reports  
D) Modify the semantic model's data model and DAX measures  

**✅ Correct Answer: C**

**Explanation:** The **Build** permission on a semantic model allows users to: connect from external tools (Excel, Power BI Desktop, Analyze in Excel), create new reports on top of the semantic model, and use it as a shared semantic model. It does NOT allow: modifying the model (Write permission needed), scheduling refresh (workspace membership or admin needed), or resharing (Reshare permission needed). Build is the minimum permission to consume a semantic model as a data source.

---

## Question 30

**Which of the following semantic model permission levels allows a user to grant other users permission to access the semantic model?**

A) Read  
B) Build  
C) Write  
D) Reshare  

**✅ Correct Answer: D**

**Explanation:** The **Reshare** permission specifically allows a user to grant others access to the semantic model. Without Reshare, even a user with Build permission cannot share the semantic model further. The four semantic model permissions are: **Read** (view the data), **Build** (create reports from it), **Reshare** (share with others), and **Write** (edit the model). These can be combined — e.g., Build + Reshare allows creating and sharing.

---

## Question 31

**A report is shared directly with a user (not via a workspace app). The user has "Read" access to the report. Which of the following is TRUE?**

A) The user can also access all other reports in the same workspace  
B) The user can re-share the report with their colleagues by default  
C) The user gets access only to that specific report, not the workspace  
D) The user automatically gets Build permission on the underlying semantic model  

**✅ Correct Answer: C**

**Explanation:** When sharing a **specific report** directly, the recipient gets access **only to that report**, not to the workspace or other content. They can view the report but cannot access the underlying workspace, other reports, or the semantic model unless separately granted. By default, resharing is not permitted unless the sharer checked "Allow recipients to share this report." Build permission on the semantic model must be granted separately if needed.

---

## Question 32

**What is the MINIMUM license required for a recipient to view a Power BI report shared directly with them from a Power BI Pro user's workspace?**

A) Power BI Free  
B) Power BI Pro  
C) Power BI Premium Per User (PPU)  
D) Microsoft 365 Business Basic  

**✅ Correct Answer: B**

**Explanation:** To view a report shared from a **Pro workspace** (non-Premium), the recipient must also have a **Power BI Pro** license. If the workspace were assigned to a **Premium capacity**, Viewers could access it with only a **Free license**. This is a foundational licensing concept: Pro-to-Pro sharing requires both parties to have Pro. The Premium capacity is what enables free consumers (Viewers) to access content.

---

## Question 33

**An organization wants to share Power BI reports with external partner companies (outside their Azure AD tenant). Which feature enables this external sharing?**

A) External workspace access through gateway  
B) Azure AD B2B (Business-to-Business) collaboration with Power BI external sharing enabled  
C) Setting the report to "Public" access in the Power BI tenant settings  
D) Publishing the report to Power BI Embedded  

**✅ Correct Answer: B**

**Explanation:** Power BI uses **Azure AD B2B collaboration** to enable external sharing. When allowed by admin settings (tenant settings > Allow invitations to external users), internal users can share reports with external email addresses. The external user receives an invitation and accesses the content via their external identity. The admin can control which domains are allowed. Publishing to "Public" shares with anyone without authentication. Power BI Embedded is for custom application embedding, not B2B sharing.

---

## Question 34

**What is the difference between "Embed for your customers" and "Embed for your organization" in Power BI embedded analytics?**

A) "Embed for your customers" uses Azure AD authentication; "Embed for your organization" uses app tokens  
B) "Embed for your customers" (App Owns Data) uses a service principal/master account and does not require end users to have Power BI licenses; "Embed for your organization" (User Owns Data) requires users to authenticate with their own Power BI licenses  
C) "Embed for your customers" is free; "Embed for your organization" requires Premium  
D) There is no difference; both approaches work identically  

**✅ Correct Answer: B**

**Explanation:** The two embedding scenarios differ fundamentally: **"Embed for your customers" (App Owns Data)** is for ISVs embedding Power BI in apps for external customers who don't have Power BI accounts. The app authenticates using a service principal or master account, and end users don't need Power BI licenses. **"Embed for your organization" (User Owns Data)** embeds Power BI in internal apps where users authenticate with their own Azure AD credentials and Power BI licenses. Option A has the authentication methods reversed.

---

## Question 35

**A company uses a service principal to authenticate a Power BI embedded application. Which Power BI tenant setting must be enabled for service principals to use the Power BI REST APIs?**

A) "Allow service principals to use Power BI APIs" in tenant settings  
B) "Enable Developer Mode" in Power BI Service  
C) "Allow external access" in workspace settings  
D) Service principals can always use Power BI APIs without any tenant setting changes  

**✅ Correct Answer: A**

**Explanation:** For service principals to access Power BI REST APIs (required for embedded scenarios), the Power BI admin must enable **"Allow service principals to use Power BI APIs"** in the Power BI admin portal > Tenant settings. Without this, service principal authentication to Power BI APIs is blocked. The service principal must also be added to a security group that is allowed in this setting (or the setting can be applied to the whole organization).

---

## Question 36

**You are configuring a deployment pipeline and notice that the data source credentials for the Test stage are different from Development (different test database credentials). Where must these credentials be updated after deployment?**

A) In Power BI Desktop before deploying  
B) In the Power Query Editor of the deployed report  
C) In the semantic model's data source settings in Power BI Service (workspace settings > dataset settings > data source credentials)  
D) In the gateway configuration for the Test stage gateway  

**✅ Correct Answer: C**

**Explanation:** After deploying content to a new pipeline stage, **data source credentials must be configured in the Power BI Service** for the semantic model in the destination workspace — under the semantic model's settings > Data source credentials. Deployment pipelines can use **deployment rules** to automatically swap data source connection strings, but credentials themselves must be entered in the Service. They cannot be pre-configured in Desktop or inherited automatically for security reasons.

---

## Question 37

**In a deployment pipeline, what are "deployment rules" used for?**

A) To enforce governance rules that prevent deployment without manager approval  
B) To automatically replace data source connection details (server names, databases) when deploying between stages  
C) To enforce sensitivity labels on deployed content  
D) To restrict which workspace roles can trigger a deployment  

**✅ Correct Answer: B**

**Explanation:** **Deployment rules** in Power BI deployment pipelines allow you to define stage-specific values for **data source connection strings** and **parameters** that are automatically applied when content is deployed to that stage. For example, you can configure the Test stage to point to the test SQL database and Production to point to the production database, so deployments automatically update the connection without manual edits. They are not approval workflows, label enforcement tools, or role restrictions.

---

## Question 38

**Which of the following workspace settings can be configured at the workspace level in Power BI Service?**

A) Assigning the workspace to a Premium/Fabric capacity  
B) Enabling RLS for all reports in the workspace  
C) Setting a default sensitivity label for all content created in the workspace  
D) Both A and C  

**✅ Correct Answer: A**

**Explanation:** At the **workspace settings level**, you can: (1) assign the workspace to a **Premium or Fabric capacity**, (2) control OneDrive integration, (3) set the workspace's license mode (Pro, Premium, Fabric), (4) manage the workspace description and contact info. RLS is configured per semantic model, not at the workspace level. Default sensitivity labels are a Purview/admin policy setting, not a per-workspace setting. Option A is the clear correct answer.

---

## Question 39

**A sensitivity label policy in Microsoft Purview requires users to always apply a label before saving a Power BI report. What type of label policy is this?**

A) Automatic labeling  
B) Recommended labeling  
C) Mandatory labeling  
D) Default labeling  

**✅ Correct Answer: C**

**Explanation:** **Mandatory labeling** policies require users to select and apply a sensitivity label before they can save or publish a file/report. Users cannot proceed without choosing a label. **Default labeling** automatically applies a pre-configured label but users can change it. **Recommended labeling** suggests a label but doesn't require it. **Automatic labeling** applies labels without user involvement based on content inspection. Mandatory is the enforcement type that requires user action.

---

## Question 40

**When configuring scheduled refresh in Power BI Service, where are the data source credentials for a cloud data source (e.g., Azure SQL Database) stored?**

A) In the Power BI Desktop .pbix file  
B) In the on-premises data gateway configuration  
C) In the semantic model's data source credentials settings in Power BI Service  
D) In the Azure Key Vault connected to the workspace  

**✅ Correct Answer: C**

**Explanation:** Data source credentials for scheduled refresh (both cloud and on-premises sources) are stored and managed in **Power BI Service**, under the semantic model's settings > **Data source credentials**. For cloud sources, no gateway is needed, but the credentials (username/password or service principal) must still be entered in this Service setting. They are not stored in the .pbix file (which only contains the connection definition, not credentials) or in Azure Key Vault natively.

---

## Question 41

**A workspace Admin wants to see a log of who published reports, changed security settings, or modified dataset permissions over the past 30 days. Where can this audit information be found?**

A) Power BI Service workspace activity log  
B) Microsoft Purview activity explorer  
C) Microsoft 365 compliance center / Microsoft Purview audit logs  
D) Power BI admin portal > Usage metrics  

**✅ Correct Answer: C**

**Explanation:** **Microsoft 365 / Microsoft Purview audit logs** record Power BI activities (publishing, access, security changes, etc.) and are accessible from the Microsoft Purview compliance portal or via the Office 365 Management Activity API. The **Power BI admin portal** also provides audit logs (it redirects to the M365 audit log). Workspace activity logs show workspace-level activity but may not have the full audit depth. Usage metrics show consumption stats, not security audit events.

---

## Question 42

**Which role in Power BI is responsible for managing tenant-level settings, such as enabling/disabling features for all users in the organization?**

A) Workspace Admin  
B) Power BI Service Administrator  
C) Global Administrator  
D) Both B and C  

**✅ Correct Answer: D**

**Explanation:** **Power BI Service Administrators** (assigned in Azure AD) can manage Power BI tenant settings, the admin portal, audit logs, capacity management, and governance features. **Global Administrators** in Azure AD also have Power BI admin privileges by default. Both roles can manage tenant-level Power BI settings. Workspace Admins only have control over their specific workspace. This is an important distinction — tenant administration vs. workspace administration.

---

## Question 43

**You need to prevent Power BI users in your organization from exporting data to Excel across all reports. Where should this be configured?**

A) In each report's settings in Power BI Service  
B) In each workspace's settings  
C) In the Power BI admin portal > Tenant settings > Export and sharing settings  
D) In Microsoft Endpoint Manager (Intune)  

**✅ Correct Answer: C**

**Explanation:** Organization-wide controls for data export, sharing, and other features are managed in the **Power BI admin portal > Tenant settings**. The "Export data" setting under "Export and sharing settings" can be disabled for all users or restricted to specific security groups. Report-level settings are per-report. Workspace settings don't control export. Intune manages devices and apps, not Power BI feature availability.

---

## Question 44

**A developer publishes a semantic model to Power BI Service and wants to allow report developers in another team to build new reports on top of it from their own workspaces. What must the developer grant?**

A) Viewer access to the semantic model's workspace  
B) Build permission on the semantic model  
C) Contributor access to the semantic model's workspace  
D) Download permission on the semantic model  

**✅ Correct Answer: B**

**Explanation:** **Build permission** on a semantic model is specifically designed to allow other users to create new reports using that semantic model from any workspace, without giving them access to the entire workspace. The report developers can connect to the semantic model via "Get data > Power BI datasets" in Desktop and build their own reports. Workspace Viewer or Contributor access is broader and gives workspace-level access, which is not needed. Download permission is for downloading the .pbix.

---

## Question 45

**What happens to an existing workspace app when a workspace is deleted in Power BI Service?**

A) The app continues to work and remains accessible to users  
B) The app is automatically moved to the Personal workspace of the workspace Admin  
C) The app is also deleted, and users lose access to the app content  
D) The app enters a "suspended" state for 30 days before deletion  

**✅ Correct Answer: C**

**Explanation:** When a **workspace is deleted**, all content within it — reports, semantic models, dataflows, dashboards, and the associated **workspace app** — is deleted as well. Users who had access to the app will immediately lose access. There is no automatic transfer of content to another workspace or a grace period. This underscores the importance of workspace governance and backup strategies. Power BI does have a workspace recovery period (soft delete), but the app still becomes inaccessible.

---

## Question 46

**A user with only Viewer access in a workspace tries to create a new report in that workspace using the workspace's semantic model. What happens?**

A) They can create reports but only in their personal workspace  
B) They can create reports directly in the workspace  
C) They receive an error because Viewers cannot create reports in the workspace  
D) They are prompted to upgrade to a Pro license  

**✅ Correct Answer: C**

**Explanation:** **Viewers** in a workspace have **read-only access** — they can view existing reports, dashboards, and dataflows, but cannot create, edit, or publish content to the workspace. Creating a new report requires at minimum **Contributor** access. The user could potentially create a report in their own personal workspace if they have Build permission on the semantic model, but they cannot create content in the shared workspace itself.

---

## Question 47

**You configure dynamic RLS using a security mapping table that stores [EmployeeEmail] and [Region]. The DAX filter on the Sales table is: FILTER(SecurityMapping, SecurityMapping[EmployeeEmail] = USERPRINCIPALNAME()). A user with email sarah@company.com has "East" in the mapping table. What data will Sarah see?**

A) All data from the Sales table  
B) Only rows from the Sales table where Region = "East"  
C) No data (the filter returns an empty set)  
D) Data for all regions because she has a company email  

**✅ Correct Answer: B**

**Explanation:** The dynamic RLS DAX filter finds the row in SecurityMapping where EmployeeEmail matches Sarah's UPN (sarah@company.com), which maps her to Region = "East". This filter context then propagates through the model relationships, restricting the Sales table to only rows where Region = "East". This is the standard dynamic RLS pattern. Sarah sees only East data because the USERPRINCIPALNAME() function returns her specific email, which is matched to her region assignment.

---

## Question 48

**Object-Level Security (OLS) in Power BI allows you to do which of the following?**

A) Restrict access to entire tables or specific columns in the semantic model based on user roles  
B) Encrypt individual database columns at rest  
C) Prevent specific users from logging into Power BI  
D) Hide specific report pages from certain users  

**✅ Correct Answer: A**

**Explanation:** **Object-Level Security (OLS)** is a feature in Power BI (configured in Tabular Editor or SSAS) that allows you to restrict access to entire **tables** or specific **columns** within a semantic model. Users in restricted roles will see the object as if it doesn't exist (similar to it being hidden and inaccessible). RLS filters rows; OLS hides/restricts entire objects. OLS does not handle page-level visibility (that's done with bookmarks/RLS patterns), encryption, or login restrictions.

---

## Question 49

**Which of the following is NOT a valid reason to use Power BI Premium capacity instead of Pro?**

A) Need to share reports with consumers who only have a Free license  
B) Need to publish paginated reports  
C) Need to use AI features like AutoML and cognitive services in dataflows  
D) Need to create more than 10 workspaces  

**✅ Correct Answer: D**

**Explanation:** The number of workspaces you can create is NOT limited by Pro vs. Premium in a way that requires Premium — Pro users can create multiple workspaces. The valid Premium-specific capabilities include: **sharing with Free users (A)**, **publishing paginated reports (B)**, **AI features in dataflows like AutoML (C)**, incremental refresh with large datasets, larger dataset sizes, higher refresh frequency (48/day), deployment pipelines, and more. Option D is not a Premium-specific limitation.

---

## Question 50

**What is the purpose of a "gateway cluster" in the on-premises data gateway setup?**

A) To load-balance queries and provide high availability across multiple gateway machines  
B) To organize gateways by data source type  
C) To limit which workspaces can use the gateway  
D) To enable the gateway to connect to multiple cloud regions  

**✅ Correct Answer: A**

**Explanation:** A **gateway cluster** is created when multiple gateway installations are added to the same logical gateway group. This provides **high availability** (if one gateway machine goes down, others in the cluster handle requests) and **load balancing** (queries are distributed across cluster members). This is essential for enterprise scenarios where gateway availability and performance are critical. It does not organize by data source type, restrict workspace access, or change cloud regions.

---

## Question 51

**An on-premises data gateway is installed and running. A workspace Admin tries to add a new data source connection to the gateway but doesn't see the gateway in the available list. What is the MOST likely reason?**

A) The gateway is running in personal mode  
B) The workspace Admin has not been added as a gateway admin  
C) The gateway needs to be restarted  
D) The gateway can only be used with Premium workspaces  

**✅ Correct Answer: B**

**Explanation:** In Power BI Service, only users who have been added as **gateway administrators** can see and use the gateway when configuring data source connections. If the workspace Admin is not a gateway admin, the gateway won't appear in their available gateways list. This is separate from workspace roles — gateway administration is managed in Power BI Service > Manage gateways. Personal mode gateways also wouldn't appear for others, but the question specifies a standard gateway scenario.

---

## Question 52

**You want to implement column-level security to prevent certain users from seeing the "Salary" column in the Employee table. Which approach should you use?**

A) Row-Level Security with a DAX filter on the Salary column  
B) Object-Level Security (OLS) to hide the Salary column for restricted roles  
C) Conditional formatting to blank out the Salary column for certain users  
D) Remove the Salary column from the report visual  

**✅ Correct Answer: B**

**Explanation:** **Object-Level Security (OLS)** is the correct mechanism for column-level (and table-level) security in Power BI. You can configure OLS to hide the "Salary" column from users in restricted roles — it will appear as if the column doesn't exist in the model. RLS filters rows, not columns. Conditional formatting and removing columns from visuals are visual-layer approaches that don't secure the underlying data (users could still access the data via Analyze in Excel or API).

---

## Question 53

**A Power BI report is connected to a streaming dataset. Which type of dashboard tile is BEST suited for showing real-time streaming data?**

A) Pinned report visual tile  
B) Streaming tile (from streaming dataset)  
C) Web content tile  
D) Text box tile  

**✅ Correct Answer: B**

**Explanation:** **Streaming tiles** on dashboards are designed specifically for real-time streaming datasets in Power BI. They update continuously as new data streams in without requiring a full page refresh, displaying live data with low latency. Pinned report tiles refresh based on the report's data refresh schedule. Web content tiles display external web pages. Text box tiles show static text. For true real-time streaming (IoT, live feeds), streaming tiles are the correct choice.

---

## Question 54

**Your organization requires that all external users accessing Power BI content authenticate using their own organization's credentials rather than receiving separate credentials. Which Azure feature supports this?**

A) Azure AD B2C (Business to Customer)  
B) Azure AD B2B (Business to Business)  
C) Azure AD Application Proxy  
D) Azure Multi-Factor Authentication (MFA)  

**✅ Correct Answer: B**

**Explanation:** **Azure AD B2B** (Business-to-Business) collaboration is the mechanism that allows external users to authenticate with their own organization's Azure AD credentials to access Power BI content shared with them. They receive an invitation, accept it, and then access content using their home organization's identity — no separate credentials needed. **B2C** is for consumer (non-organizational) identity scenarios. Application Proxy is for publishing internal apps. MFA adds authentication factors but doesn't address the external identity federation requirement.

---

## Question 55

**Which of the following BEST describes the "Contributor" role in a Power BI workspace?**

A) Can view all content, configure scheduled refresh, and manage workspace app settings  
B) Can publish reports and edit content in the workspace, but cannot publish the workspace app or manage workspace members  
C) Can only view published reports and dashboards  
D) Has full control including adding members, changing settings, and deleting the workspace  

**✅ Correct Answer: B**

**Explanation:** The **Contributor** role in Power BI workspaces can: publish content (reports, semantic models), edit existing content, configure scheduled refresh for datasets they publish, delete content they own, and create dataflows. However, Contributors **cannot**: publish the workspace app, update existing app settings, add/remove workspace members, or delete the workspace. That's the key Contributor vs. Member distinction frequently tested on the PL-300 exam.

---

*End of Section 4 Practice Questions — 55 Questions Total*

**Workspace Role Quick Reference:**

| Action | Admin | Member | Contributor | Viewer |
|--------|-------|--------|-------------|--------|
| View content | ✅ | ✅ | ✅ | ✅ |
| Edit/publish content | ✅ | ✅ | ✅ | ❌ |
| Publish workspace app | ✅ | ✅ | ❌ | ❌ |
| Add/remove members | ✅ | ❌ | ❌ | ❌ |
| Delete workspace | ✅ | ❌ | ❌ | ❌ |
| Manage RLS in Service | ✅ | ✅ | ❌ | ❌ |

**Score Tracker:**  
- 50–55 correct: Excellent — well prepared for this domain  
- 40–49 correct: Good — review flagged topics  
- Below 40: Review the Manage and Secure section of the Microsoft PL-300 study guide
