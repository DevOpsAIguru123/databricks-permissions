# Databricks Workspace — Permissions Reference

> **Detailed workspace permission matrices by environment**
> These matrices follow the defined personas and environments from the Unity Catalog permissions reference. They represent a recommended enterprise baseline aligned with the rule: humans create and iterate in SANDBOX/DEVELOPMENT, but higher environments are strictly owned by automation.

---

## 1. SANDBOX Environment

*Free experimentation, personal exploration, prototypes. All interactive users have full access to workspace objects.*

| Object | Platform Admin | Data Eng | Data Sci | Analyst | MLOps Eng | MLOps SP |
|---|---|---|---|---|---|---|
| **Clusters** | `CAN_MANAGE` | `CAN_MANAGE` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` | `—` | `—` |
| **Jobs** | `CAN_MANAGE` | `CAN_MANAGE` | `CAN_MANAGE` | `CAN_MANAGE` | `—` | `—` |
| **DLT Pipelines** | `CAN_MANAGE` | `CAN_MANAGE` | `CAN_MANAGE` | `CAN_MANAGE` | `—` | `—` |
| **Directories** | `CAN_MANAGE` | `CAN_EDIT` | `CAN_EDIT` | `CAN_EDIT` | `—` | `—` |
| **Notebooks** | `CAN_MANAGE` | `CAN_EDIT` | `CAN_EDIT` | `CAN_EDIT` | `—` | `—` |
| **ML Experiments** | `CAN_MANAGE` | `CAN_EDIT` | `CAN_EDIT` | `CAN_EDIT` | `—` | `—` |
| **Dashboards** | `CAN_MANAGE` | `CAN_EDIT` | `CAN_EDIT` | `CAN_EDIT` | `—` | `—` |
| **Queries** | `CAN_MANAGE` | `CAN_EDIT` | `CAN_EDIT` | `CAN_EDIT` | `—` | `—` |
| **Alerts** | `CAN_MANAGE` | `CAN_MANAGE` | `CAN_MANAGE` | `CAN_MANAGE` | `—` | `—` |
| **SQL Warehouse** | `CAN_MANAGE` | `CAN_USE` | `CAN_USE` | `CAN_USE` | `—` | `—` |
| **Repos** | `CAN_MANAGE` | `CAN_EDIT` | `CAN_EDIT` | `CAN_EDIT` | `—` | `—` |
| **Pools** | `CAN_MANAGE` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` | `—` | `—` |

---

## 2. DEVELOPMENT Environment

*Active pipeline development and feature engineering. Data Engineers build. Data Sci and MLOps Eng validate/read. Analysts have no access.*

| Object | Platform Admin | Data Eng | Data Sci | Analyst | MLOps Eng | MLOps SP |
|---|---|---|---|---|---|---|
| **Clusters** | `CAN_MANAGE` | `CAN_MANAGE` | `CAN_ATTACH_TO` | `—` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` |
| **Jobs** | `CAN_MANAGE` | `CAN_MANAGE` | `CAN_VIEW` | `—` | `CAN_VIEW` | `IS_OWNER` |
| **DLT Pipelines** | `CAN_MANAGE` | `CAN_MANAGE` | `CAN_VIEW` | `—` | `CAN_VIEW` | `IS_OWNER` |
| **Directories** | `CAN_MANAGE` | `CAN_EDIT` | `CAN_VIEW` | `—` | `CAN_VIEW` | `CAN_READ` |
| **Notebooks** | `CAN_MANAGE` | `CAN_EDIT` | `CAN_VIEW` | `—` | `CAN_VIEW` | `CAN_READ` |
| **ML Experiments** | `CAN_MANAGE` | `CAN_EDIT` | `CAN_VIEW` | `—` | `CAN_VIEW` | `CAN_READ` |
| **Dashboards** | `CAN_MANAGE` | `CAN_EDIT` | `CAN_VIEW` | `—` | `CAN_VIEW` | `CAN_VIEW` |
| **Queries** | `CAN_MANAGE` | `CAN_MANAGE` | `CAN_VIEW` | `—` | `CAN_VIEW` | `CAN_VIEW` |
| **Alerts** | `CAN_MANAGE` | `CAN_MANAGE` | `CAN_VIEW` | `—` | `CAN_VIEW` | `—` |
| **Secrets** | `CAN_MANAGE` | `READ` | `—` | `—` | `READ` | `READ` |
| **SQL Warehouse** | `CAN_MANAGE` | `CAN_USE` | `CAN_USE` | `—` | `CAN_USE` | `CAN_USE` |
| **Repos** | `CAN_MANAGE` | `CAN_EDIT` | `CAN_VIEW` | `—` | `CAN_VIEW` | `CAN_READ` |
| **Pools** | `CAN_MANAGE` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` | `—` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` |

---

## 3. STAGE Environment

*Pre-production validation and testing. Governed environment strictly driven by automation. All interactive users read.*

| Object | Platform Admin | Data Eng | Data Sci | Analyst | MLOps Eng | MLOps SP |
|---|---|---|---|---|---|---|
| **Clusters** | `CAN_MANAGE` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` |
| **Jobs** | `CAN_MANAGE` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `IS_OWNER` |
| **DLT Pipelines** | `CAN_MANAGE` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `IS_OWNER` |
| **Directories** | `CAN_MANAGE` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_READ` |
| **Notebooks** | `CAN_MANAGE` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_READ` |
| **ML Experiments** | `CAN_MANAGE` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_READ` |
| **Dashboards** | `CAN_MANAGE` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` |
| **Queries** | `CAN_MANAGE` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` |
| **Alerts** | `CAN_MANAGE` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `—` |
| **Secrets** | `CAN_MANAGE` | `—` | `—` | `—` | `READ` | `READ` |
| **SQL Warehouse** | `CAN_MANAGE` | `CAN_USE` | `CAN_USE` | `CAN_USE` | `CAN_USE` | `CAN_USE` |
| **Repos** | `CAN_MANAGE` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_READ` |
| **Pools** | `CAN_MANAGE` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` |

---

## 4. PRODUCTION Environment

*Governed production data and workloads. Driven entirely by automation.*

| Object | Platform Admin | Data Eng | Data Sci | Analyst | MLOps Eng | MLOps SP |
|---|---|---|---|---|---|---|
| **Clusters** | `CAN_MANAGE` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` |
| **Jobs** | `CAN_MANAGE` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `IS_OWNER` |
| **DLT Pipelines** | `CAN_MANAGE` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `IS_OWNER` |
| **Directories** | `CAN_MANAGE` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_READ` |
| **Notebooks** | `CAN_MANAGE` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_READ` |
| **ML Experiments** | `CAN_MANAGE` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_READ` |
| **Dashboards** | `CAN_MANAGE` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` |
| **Queries** | `CAN_MANAGE` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` |
| **Alerts** | `CAN_MANAGE` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `—` |
| **Secrets** | `CAN_MANAGE` | `—` | `—` | `—` | `READ` | `READ` |
| **SQL Warehouse** | `CAN_MANAGE` | `CAN_USE` | `CAN_USE` | `CAN_USE` | `CAN_USE` | `CAN_USE` |
| **Repos** | `CAN_MANAGE` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_VIEW` | `CAN_READ` |
| **Pools** | `CAN_MANAGE` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` | `CAN_ATTACH_TO` |

---

## Object-by-object permission mapping 
*(What each level actually enables based on official ACL definitions)*

*   **Jobs**: Your `CAN_RUN` should be treated as `CAN_MANAGE_RUN` (run/cancel runs). Databricks defines job permissions as `CAN_VIEW`, `CAN_MANAGE_RUN`, `CAN_MANAGE`, `IS_OWNER`, and also documents that jobs run using the *Run as* identity, not the identity of the user who clicks “Run now.” 
*   **Pipelines**: Lakeflow Declarative Pipelines use ACLs with `CAN_VIEW`, `CAN_RUN`, `CAN_MANAGE`, `IS_OWNER`. Pipeline updates can be run by principals with `CAN_RUN`, `CAN_MANAGE`, or `IS_OWNER`, and managing pipeline permissions requires `CAN_MANAGE` or `IS_OWNER`. 
*   **Compute**: Cluster permissions are `CAN_ATTACH_TO`, `CAN_RESTART`, `CAN_MANAGE`. Notably, `CAN_ATTACH_TO` allows attaching notebooks and viewing Spark UI, while starting/restarting requires `CAN_RESTART` or `CAN_MANAGE`. There is also a documented risk surface: in legacy compute, users with `CAN_ATTACH_TO` can see sensitive keys in logs. 
*   **Folders and notebooks**: Folder ACLs (`CAN_VIEW`, `CAN_RUN`, `CAN_EDIT`, `CAN_MANAGE`) are designed for inheritance and scale. UI “CAN VIEW” corresponds to API “CAN READ”. 
*   **Dashboards**: AI/BI dashboards use a combined `CAN_VIEW`/`CAN_RUN` permission, with higher levels `CAN_EDIT` and `CAN_MANAGE`. Legacy dashboards retain separate `CAN_VIEW` and `CAN_RUN`.
*   **Queries**: Query ACLs explicitly define `CAN_VIEW`, `CAN_RUN`, `CAN_EDIT`, `CAN_MANAGE`.
*   **Alerts**: Alerts generally use only two effective levels, `CAN_RUN` and `CAN_MANAGE`.
*   **Secrets**: Secret scopes are governed by `READ`, `WRITE`, `MANAGE`. This is intentionally distinct from the view/run/edit pattern. 
*   **SQL warehouses**: The warehouse ACL model includes `CAN_VIEW`, `CAN_MONITOR`, `CAN_USE`, `IS_OWNER`, `CAN_MANAGE`. Running queries is tied to `CAN_USE`.
*   **Repos**: Git folder permissions use `CAN_READ`, `CAN_RUN`, `CAN_EDIT`, `CAN_MANAGE`.
*   **Pools**: Pools support `CAN_ATTACH_TO` and `CAN_MANAGE`. 
*   **MLflow experiments**: Permissions differ for notebook experiments vs workspace experiments; notebook experiments follow notebook-like permissions, while workspace experiments allow `CAN_READ`, `CAN_EDIT`, `CAN_MANAGE`. 

---

## Limitations and operational edge cases

*   **Access control feature gating**: For upgraded workspaces, access control settings can start disabled. This matters during migrations where teams assume ACLs already apply. 
*   **ACL enablement gotcha**: If a workspace was created with certain ACL toggles off, the UI may not show “Permissions” controls for jobs, clusters, or pipelines. 
*   **Job execution identity is not the clicker**: Jobs run as the configured *Run as* principal. Giving someone “run job” rights can indirectly expose data that only the *Run as* identity can read. Production job *Run as* should be a tightly governed principal (`sp-mlops-cicd`).
*   **Admin overreach risk**: By default, workspace admins can change owners and *Run as* settings broadly. Enable `RestrictWorkspaceAdmins` to limit their ability to manipulate job *Run as* or service principal tokens.
*   **Cluster start friction**: If users only have `CAN_ATTACH_TO`, they may not be able to autostart a stopped cluster. Databricks explicitly ties start/restart ability to `CAN_RESTART` (or `CAN_MANAGE`). 
*   **Logs and secrets exposure**: Secrets are not redacted in driver log streams. In legacy access modes, `CAN_ATTACH_TO` can expose sensitive keys. Treat `CAN_ATTACH_TO` as a permission with security consequences. 
*   **DLT driver logs behavior**: For Unity Catalog-enabled pipelines, only pipeline owners and workspace admins can view some driver logs by default. This confirms the SP should be the exclusive owner in higher environments.

---

## Best practices and governance controls

1.  **Use workspace bindings to enforce isolation.** Binding catalogs to specific workspaces separates the "where" and "what".
2.  **Keep workspace admins rare and apply `RestrictWorkspaceAdmins`.** Prevent silent admin escalation workflows.
3.  **Make automation the writer in STAGE and PRODUCTION.** Jobs and deployments should run as a service principal so success doesn't depend on humans. Humans remain read-only viewers/approvers.
4.  **Prefer folder-first ACL management.** Inheritance is first-class. Grant access on top-level folders rather than individual objects to remain maintainable at scale.
5.  **Treat personal access tokens as a privileged capability.** Apply the `CAN_USE` token capability strictly to automation identities and short-lived break-glass scopes.
6.  **Constrain compute creation using policies.** Non-admin users must be granted policy permissions to use specific compute templates. Limits cost and enforces security configurations.
7.  **Avoid the “ALL PRIVILEGES means everything” trap.** Explicitly document roles rather than relying on `ALL PRIVILEGES`, which masks specific object boundaries.

---

## Open questions to validate

*   **Which cloud and editions are you standardizing on?** Do all workspaces have ACL features enabled natively?
*   **Are STAGE and PRODUCTION separate workspaces**, or a shared workspace with environment folders? (Environment folders complicate isolation vs bound catalogs).
*   **Will DLT pipeline ownership always be the MLOps service principal in governed environments?** This limits human reading of log streams during operations.
*   **Do analysts truly need interactive workspace access in DEVELOPMENT**, or can they be limited to STAGE/PRODUCTION read dashboards via SQL warehouses? (The matrix assumes No Access in Development to prevent dependencies on unstable schemas).