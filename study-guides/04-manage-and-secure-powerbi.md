# Domain 4: Manage and Secure Power BI (15–20%)

> **Exam Weight:** 15–20% | **Estimated Study Time:** 1 week

This domain covers workspace administration, distributing content, configuring security, and governing data assets. While smaller in weight, these topics are straightforward marks to earn with targeted study.

---

## Table of Contents

1. [Create and Manage Workspaces and Assets](#1-create-and-manage-workspaces-and-assets)
2. [Secure and Govern Data](#2-secure-and-govern-data)
3. [Common Exam Pitfalls](#4-common-exam-pitfalls)
4. [Key Terms Glossary](#5-key-terms-glossary)

---

## 1. Create and Manage Workspaces and Assets

### 1.1 Workspaces

Workspaces are collaboration containers in the Power BI service that hold reports, dashboards, datasets (semantic models), dataflows, and paginated reports.

**Workspace Types:**

| Type | Description |
|---|---|
| **My Workspace** | Personal workspace; cannot be shared; for personal use and testing only |
| **Workspace (v2)** | Collaborative workspace backed by a Microsoft 365 group or standalone; supports all features |

**Creating a Workspace:**
1. Power BI service → **Workspaces → Create a workspace**
2. Provide name, description, and optional Pro/Premium licence mode
3. Optionally associate with a Microsoft 365 group
4. Configure advanced settings (OneDrive storage, Capacity assignment)

**Workspace Settings:**
- **Premium capacity** — assign the workspace to a Premium or Fabric capacity for advanced features
- **OneDrive** — link to a SharePoint/OneDrive folder for file storage
- **Allowed file extensions** — control which file types can be uploaded

### 1.2 Workspace Roles

This is one of the most tested topics in Domain 4. Know all four roles and their permissions precisely.

| Permission | Admin | Member | Contributor | Viewer |
|---|---|---|---|---|
| View and interact with content | ✅ | ✅ | ✅ | ✅ |
| Create/edit reports in the workspace | ✅ | ✅ | ✅ | ❌ |
| Publish reports to workspace | ✅ | ✅ | ✅ | ❌ |
| Delete workspace items | ✅ | ✅ | ✅ | ❌ |
| Schedule data refresh | ✅ | ✅ | ✅ | ❌ |
| Create apps from workspace | ✅ | ✅ | ❌ | ❌ |
| Update and publish apps | ✅ | ✅ | ❌ | ❌ |
| Share items and allow re-sharing | ✅ | ✅ | ❌ | ❌ |
| Add/remove users (except Admins) | ✅ | ✅ | ❌ | ❌ |
| Add/remove Admins | ✅ | ❌ | ❌ | ❌ |
| Delete the workspace | ✅ | ❌ | ❌ | ❌ |

> **Exam Tip:** **Contributors** can publish and edit content but cannot create apps or manage users. **Members** can do everything except add/remove Admins and delete the workspace.

### 1.3 Power BI Apps

Apps are packaged, read-only views of workspace content distributed to end users.

**Key App Characteristics:**
- Users get a **stable, curated view** of reports and dashboards
- The workspace continues to be the development environment
- App audience = consumers; workspace = producers
- App can include a subset of workspace content

**Creating an App:**
1. Workspace → **Create app**
2. Configure: Name, Description, Logo, Support info, Contact
3. Add content (reports, dashboards, select pages to include or exclude)
4. Configure navigation (custom navigation pane)
5. Set audience permissions (individuals, groups, or whole organisation)
6. **Publish app**

**Updating an App:**
- After making changes in the workspace, go to **Workspace → Update app**
- Users automatically receive the updated version

**App Permissions:**
- **Build permission** — allows users to build new reports using the app's dataset
- **Reshare** — allows users to share the app with others

### 1.4 Publishing and Importing Items

**Publishing from Power BI Desktop:**
1. **Home → Publish**
2. Sign in with Power BI account
3. Select destination workspace
4. Publishes both the report AND the semantic model

**Importing .pbix via Power BI Service:**
- Workspace → **New → Upload a file** → Browse for .pbix
- Also supports Excel workbooks, paginated report (.rdl) files

**Exporting from the Service:**
- Reports can be exported as: PDF, PowerPoint, CSV (table visuals), Excel (table data)
- Requires appropriate permissions
- Export to PDF / PowerPoint available in the report viewer toolbar

### 1.5 Dashboards

Dashboards are single-page canvases that pin tiles from multiple reports and datasets.

**Key Dashboard Features:**

| Feature | Description |
|---|---|
| **Pin visual** | Pin any report visual to a dashboard |
| **Pin live page** | Pin an entire report page as a single tile |
| **Tile types** | Visuals, Images, Text boxes, Web content, Video |
| **Dashboard themes** | Apply a light/dark theme to the dashboard canvas |
| **Q&A on dashboard** | Ask natural language questions directly on the dashboard |
| **Featured dashboard** | Set as the default landing page in Power BI service |

**Dashboard vs Report:**

| Feature | Dashboard | Report |
|---|---|---|
| Pages | Single page | Multi-page |
| Interactivity | Limited (drill down to report) | Full (slicers, filters, cross-filter) |
| Alerts | ✅ Supported | ❌ Not supported |
| Subscriptions | ✅ Supported | ✅ Supported |
| Created in | Power BI service only | Power BI Desktop + service |

### 1.6 Subscriptions and Data Alerts

**Subscriptions:**
- Receive email snapshots of reports or dashboards on a schedule
- Set up via **Subscribe** button in report or dashboard
- Configure: recipients, frequency (Daily, Weekly, After data refresh), time of day
- Subscribers receive a static image + link to the live report
- **Workspace Admins/Members** can create subscriptions on behalf of others

**Data Alerts:**
- Notify when a **dashboard tile KPI or card** crosses a threshold
- Set up via the tile's **ellipsis menu → Manage alerts**
- Configure: condition (above/below), threshold value, notification frequency
- Only available for **numeric card tiles and gauge tiles** on dashboards (not on reports)
- Recipients must have access to the dashboard

> **Exam Tip:** Data alerts are only available on **dashboards**, not report pages. They only work for **Import** mode datasets and require the dashboard tile to show a numeric scalar value.

### 1.7 Endorsement

Endorsement signals the quality and trustworthiness of content to consumers:

| Endorsement Level | Who Can Apply | Meaning |
|---|---|---|
| **None** | — | Default; no endorsement |
| **Promoted** | Content owner or workspace Member/Admin | Owner recommends this content |
| **Certified** | Tenant admins (via certification reviewer designation) | Meets organisational standards |
| **Master data** (Fabric) | Designated data owners | Authoritative source of record |

**Applying Endorsement:**
- Semantic model/report settings → **Endorsement** → select level → Save

**Discovery:** Endorsed content appears higher in search results and is marked with badges.

### 1.8 Data Gateways

Gateways allow Power BI service to connect to **on-premises** data sources.

| Gateway Type | Use Case | How it Works |
|---|---|---|
| **On-premises data gateway (standard)** | Multiple users, production workloads | Installed on a server; acts as a bridge; supports multiple connectors |
| **On-premises data gateway (personal mode)** | Single user, personal use only | Installed on user's own PC; not suitable for production |
| **Virtual Network (VNet) data gateway** | Azure data sources in a private VNet | Managed service; no on-premises server needed; Premium/Fabric required |

**Gateway Architecture:**
```
Power BI Service (cloud) ←→ Gateway (on-premises server) ←→ Data Source (SQL, Oracle, etc.)
```

**Configuring a Gateway:**
1. Download and install the gateway from Power BI service
2. Register the gateway with Power BI (sign in with admin account)
3. In Power BI service: **Settings (gear) → Manage connections and gateways**
4. Add data source connections on the gateway
5. Assign users who can use the gateway data source
6. In dataset settings: assign the gateway connection

**Gateway Cluster:**
- Multiple gateway instances can form a **cluster** for high availability
- Load is distributed across cluster members
- If one gateway goes offline, others continue serving requests

### 1.9 Scheduled Refresh

Scheduled refresh keeps Import-mode datasets current:

**Configuring Scheduled Refresh:**
1. Workspace → Semantic model → **Settings → Scheduled refresh**
2. Configure:
   - **Refresh frequency**: Daily or Weekly
   - **Time zone**: specify the timezone for scheduled times
   - **Times**: add up to 8 times/day (Pro) or 48 times/day (Premium)
   - **Send refresh failure notifications**: email owner and/or contacts
3. Requires a valid gateway connection for on-premises sources
4. Data source credentials must be configured

**Refresh History:**
- View refresh logs in dataset settings → **Refresh history**
- Shows success/failure status and duration for each refresh run

> **Exam Tip:** Maximum scheduled refresh frequency is **8 times per day for Pro** licences and **48 times per day for Premium** licences. DirectQuery datasets do not require scheduled refresh (data is always live).

### 1.10 Deployment Pipelines

Deployment pipelines enable **ALM (Application Lifecycle Management)** for Power BI content.

**Pipeline Stages:**
- **Development** → **Test** → **Production**
- Each stage is a separate workspace
- Content is promoted (deployed) between stages

**Features:**
- Deploy reports, dashboards, datasets, and dataflows
- Apply **deployment rules** to change data source connections per stage (e.g., Dev DB → Prod DB)
- View **comparison** between stages before deploying
- Supports rollback via the pipeline history

**Licence Requirements:**
- Requires **Premium** capacity or **Premium Per User (PPU)**

---

## 2. Secure and Govern Data

### 2.1 Row-Level Security (RLS)

RLS restricts data access at the row level based on the user's identity.

**Two Types of RLS:**

#### Static RLS
- Hard-coded rules that filter to specific values
- Each role is manually maintained

```dax
// Role: "South Region" — filter Sales to South region only
[Region] = "South"

// Role: "Manager" — filter to specific manager
[ManagerID] = 42
```

**Setting Up Static RLS:**
1. Power BI Desktop → **Modeling → Manage roles**
2. Create a new role (e.g., "South Region")
3. Select the table and write a DAX filter expression
4. **Save**
5. **Test as role** to verify
6. Publish to Power BI service
7. In service: semantic model → **Security** → assign users/groups to roles

#### Dynamic RLS
- Uses the currently signed-in user's identity to filter data
- No separate role per user needed — scales to any number of users

```dax
// Dynamic RLS using USERNAME() or USERPRINCIPALNAME()
// In the Sales table:
[SalesRepEmail] = USERPRINCIPALNAME()

// Or using a separate security/mapping table:
// In the SecurityTable role filter:
[UserEmail] = USERPRINCIPALNAME()
```

**Dynamic RLS Architecture:**
1. Create a security mapping table: `UserEmail | Region` (or `UserEmail | EmployeeID`)
2. Relate the security table to the relevant dimension table
3. In the RLS role DAX expression, filter the security table by USERPRINCIPALNAME()
4. With bidirectional cross-filtering (or PATHCONTAINS for hierarchies), the filter propagates to fact tables

**Testing RLS:**
- Desktop: **Modeling → View as → select role**
- Service: Semantic model → Security → **Test as role**

> **Exam Tip:** `USERNAME()` returns `DOMAIN\username` format. `USERPRINCIPALNAME()` returns `user@domain.com` format. Use **USERPRINCIPALNAME()** for Power BI service (cloud) scenarios — it matches the Azure AD UPN.

### 2.2 Object-Level Security (OLS)

OLS restricts access to specific **tables or columns** (not just rows):

- Requires **Tabular Editor** or XMLA endpoint to configure (not configurable in Power BI Desktop UI)
- Hidden tables/columns become completely invisible to restricted users
- RLS and OLS can be combined

### 2.3 Sensitivity Labels

Sensitivity labels (from Microsoft Purview Information Protection) classify and protect Power BI content:

**Label Hierarchy (most to least restrictive):**
1. **Highly Confidential** — most restrictive; encrypts exports
2. **Confidential** — encryption on exports; access logs
3. **Internal** — internal use only; watermarks
4. **Public** — no restrictions

**Applying Sensitivity Labels:**
- On semantic models, reports, dashboards: **File → Info → Apply a sensitivity label** (in service)
- Labels inherit downward: if a dataset has "Confidential," reports built on it inherit that label
- Labels are retained when content is exported (PDF, Excel, PowerPoint)

**Label Enforcement:**
- Tenant admins configure mandatory labelling policies
- Users cannot lower a label without justification (label demotion policy)
- Admins can view all labelled content in Microsoft Purview compliance portal

> **Exam Tip:** Sensitivity labels are applied at the **content level** and carry through to exports. They are managed through the **Microsoft Purview** admin portal, not the Power BI admin portal.

### 2.4 Semantic Model (Dataset) Access

Control who can use the semantic model independently of reports:

**Permission Levels:**
| Permission | Description |
|---|---|
| **Read** | Can query the model via reports they have access to |
| **Build** | Can create new reports using this dataset (in service or Desktop via live connection) |
| **Write** | Can modify the dataset (effectively workspace-level access) |
| **Reshare** | Can share the dataset permissions with others |

**Granting Build Permission:**
- Semantic model → **More options → Manage permissions**
- Or via **Share** dialog → tick "Allow recipients to build content with the data"

**Why Build Permission Matters:**
- Build permission enables the **Live Connection** scenario: data analysts connect Power BI Desktop to a published dataset and build their own reports without data duplication
- Supports the **centralised model / distributed reporting** pattern

### 2.5 Sharing and Distribution Methods

| Method | Best For | Licence Required |
|---|---|---|
| **Direct share** | Ad-hoc sharing with specific users | Pro (sender and receiver) |
| **Workspace access** | Team collaboration on content creation | Pro or Premium |
| **App** | Broad distribution to many consumers | Pro (creator); Free (consumer with Premium capacity) |
| **Embed in SharePoint** | Intranet portal integration | Pro (publisher) |
| **Embed in Teams** | Teams collaboration | Pro |
| **Publish to web** | Public internet (no authentication) | Admin must enable; not for confidential data |
| **Export** | One-time sharing via PDF/Excel/PowerPoint | Standard export rights |

> **Exam Tip:** **Publish to web** creates a publicly accessible link — it is not secure and should never be used for internal or confidential content. Tenant admins can disable this feature.

### 2.6 Information Protection and Governance

**Power BI Admin Portal:**
- Accessible via **Settings (gear) → Admin portal** (for tenant admins only)
- Key settings:
  - Export and sharing settings (control export formats)
  - Sensitivity label policies
  - Workspace creation settings
  - Certified content settings
  - Copilot settings
  - Developer settings (API access, embed codes)

**Audit Logs:**
- All Power BI activity is logged in the **Microsoft 365 Audit Log** (Purview)
- Track: who viewed a report, who exported data, who shared content, refresh failures
- Accessible via PowerShell (`Get-PowerBIActivityEvent`) or Microsoft Purview portal

**Usage Metrics:**
- Per-report and per-dashboard metrics available in the service
- Track: views, unique viewers, shares, performance
- Workspace admins can see aggregated workspace metrics

---

## 3. Common Exam Pitfalls

| Pitfall | Correct Understanding |
|---|---|
| Giving users Contributor when they only need to view | Viewers can only consume; Contributors can edit and publish |
| Confusing data alerts (dashboards) with subscriptions | Alerts = threshold notification on dashboard tile; Subscriptions = scheduled email snapshot |
| Thinking scheduled refresh works for DirectQuery | DirectQuery is always live; no refresh schedule needed or supported |
| Confusing USERNAME() and USERPRINCIPALNAME() | Use USERPRINCIPALNAME() for cloud/Power BI service; returns user@domain.com |
| Thinking RLS is set in the Power BI service | RLS roles are created in **Power BI Desktop**; users are assigned in the **service** |
| Forgetting to assign users to RLS roles in service | Creating roles in Desktop is not enough; must assign users in service security settings |
| Using personal gateway for production workloads | Personal gateway is single-user only; use standard gateway for shared/production use |
| Not distinguishing Promoted from Certified endorsement | Promoted = owner sets; Certified = org-designated reviewer sets |
| Thinking Build permission allows editing a dataset | Build = create reports on top of it; Write/Admin = actual model editing |
| Confusing Apps with Workspaces | Workspace = dev/collab environment; App = packaged read-only distribution |

---

## 4. Key Terms Glossary

| Term | Definition |
|---|---|
| **Workspace** | Collaborative container for Power BI content (reports, datasets, dashboards) |
| **App** | Packaged, curated view of workspace content for distribution to consumers |
| **Row-Level Security (RLS)** | DAX-based rules that filter rows based on the viewing user's identity |
| **Object-Level Security (OLS)** | Security that hides entire tables or columns from specific roles |
| **Sensitivity Label** | Microsoft Purview classification applied to content; retained on export |
| **On-premises Gateway** | Software bridge enabling cloud Power BI to connect to on-premises data |
| **VNet Gateway** | Microsoft-managed gateway for Azure private network data sources |
| **Scheduled Refresh** | Automatic data refresh for Import-mode datasets on a configured schedule |
| **Deployment Pipeline** | ALM tool for promoting content through Dev → Test → Production stages |
| **Endorsement** | Quality signal: Promoted (owner) or Certified (designated reviewer) |
| **Build Permission** | Allows users to create new reports from a published semantic model |
| **USERPRINCIPALNAME()** | DAX function returning the current user's Azure AD UPN (email format) |
| **Data Alert** | Notification triggered when a dashboard tile value crosses a threshold |
| **Subscription** | Scheduled email containing a snapshot of a report or dashboard |
| **Audit Log** | Microsoft 365 log of all Power BI activity for governance and compliance |

---

## Quick Revision Summary

**Workspace Roles (most → least privilege):**
Admin > Member > Contributor > Viewer

- **Admin** = full control including deleting workspace and adding/removing Admins
- **Member** = can create apps and manage users (except Admins)
- **Contributor** = can publish and edit content; cannot create apps
- **Viewer** = read-only access

**RLS Setup Flow:**
1. Create roles in **Power BI Desktop** (Modeling → Manage roles)
2. Write DAX filter expressions per table
3. Publish to Power BI service
4. Assign users/groups to roles in **Service → Semantic model → Security**

**Dynamic RLS:** Use `USERPRINCIPALNAME()` to match signed-in user's email to a column in the model.

**Gateway Types:**
- **Standard** = production, multi-user, on-premises server
- **Personal** = single user, personal use, user's PC
- **VNet** = Azure private network, Premium/Fabric required

**Endorsement:** Promoted (owner) < Certified (org-designated reviewer)

**Sensitivity Labels** propagate to exports (PDF, Excel, PowerPoint); managed in Microsoft Purview.

**Scheduled Refresh Limits:**
- Pro: 8 times/day
- Premium: 48 times/day
- DirectQuery: Not needed (always live)

**App vs Direct Share vs Workspace:**
- **App** = stable, curated experience for consumers; update via "Update app"
- **Direct Share** = share specific item with specific people
- **Workspace** = gives direct access to all workspace content; for collaborators

**Build Permission** = create reports on top of a published dataset (Live Connection pattern).
