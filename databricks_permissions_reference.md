# Databricks Unity Catalog — Permissions Reference

> **Ready-to-use reference** for platform administrators setting up role-based access in Azure Databricks with Unity Catalog. 
> Designed to be strictly unambiguous, enforcing environment segregation and least privilege.

### Assumptions used in this permission model

This report intentionally makes assumptions to provide an enterprise-ready “default” design; adjust based on your reality:

*   **You use a single Unity Catalog metastore per account/region** with multiple workspaces, and you can apply workspace bindings for isolation. 
*   **You organize data governance primarily through catalogs and schemas**, using inheritance for scalable grants. 
*   **You have (or can implement) account-level group provisioning** from your IdP and avoid workspace-local groups for UC governance. 
*   **CI/CD for pipelines and deployments uses service principals**, not human users. 
*   **You prefer volumes for file governance** rather than direct external-location file privileges. 
*   **Materialized views, if used, are treated as governed assets** with explicit SELECT and REFRESH controls.

---

## Detailed Privilege Breakdown

### SANDBOX Environment

| Privilege | Platform Admin | Data Eng | Data Sci | Analyst | MLOps Eng | MLOps SP |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| USE CATALOG / BROWSE | yes | yes | yes | yes | — | — |
| CREATE SCHEMA | yes | yes | yes | yes | — | — |
| CREATE TABLE | yes | yes | yes | yes | — | — |
| SELECT | yes | yes | yes | yes | — | — |
| MODIFY | yes | yes | yes | yes | — | — |
| EXECUTE | yes | yes | yes | yes | — | — |
| READ/WRITE VOLUME | yes | yes | yes | yes | — | — |
| CREATE MODEL / VERSION | yes | yes | yes | — | — | — |

### DEVELOPMENT Environment

| Privilege | Platform Admin | Data Eng | Data Sci | Analyst | MLOps Eng | MLOps SP |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| USE CATALOG / BROWSE | yes | yes | yes | — | yes | yes |
| CREATE SCHEMA | yes | yes | — | — | — | — |
| CREATE TABLE | yes | yes | — | — | — | — |
| SELECT | yes | yes | yes | — | yes | yes |
| MODIFY | yes | yes | — | — | — | — |
| EXECUTE | yes | yes | yes | — | yes | yes |
| READ VOLUME | yes | yes | yes | — | yes | yes |
| WRITE VOLUME | yes | yes | — | — | — | yes |
| CREATE MODEL VERSION | yes | yes | — | — | — | yes |

### STAGE Environment

| Privilege | Platform Admin | Data Eng | Data Sci | Analyst | MLOps Eng | MLOps SP |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| USE CATALOG / BROWSE | yes | yes | yes | yes | yes | yes |
| CREATE SCHEMA | yes | — | — | — | — | yes |
| CREATE TABLE | yes | — | — | — | — | yes |
| SELECT | yes | yes | yes | yes | yes | yes |
| MODIFY | yes | — | — | — | — | yes |
| EXECUTE | yes | yes | yes | yes | yes | yes |
| READ VOLUME | yes | yes | yes | yes | yes | yes |
| WRITE VOLUME | yes | — | — | — | — | yes |
| CREATE MODEL VERSION | yes | — | — | — | yes | yes |
| MANAGE (models) | yes | — | — | — | yes | yes |
| APPLY TAG (models) | yes | — | — | — | yes | yes |

### PRODUCTION Environment

| Privilege | Platform Admin | Data Eng | Data Sci | Analyst | MLOps Eng | MLOps SP |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| USE CATALOG / BROWSE | yes | yes | yes | yes | yes | yes |
| CREATE SCHEMA | yes | — | — | — | — | yes |
| CREATE TABLE | yes | — | — | — | — | yes |
| SELECT | yes | yes | yes | yes | yes | yes |
| MODIFY | yes | — | — | — | — | yes |
| EXECUTE | yes | yes | yes | yes | yes | yes |
| READ VOLUME | yes | yes | yes | yes | yes | yes |
| WRITE VOLUME | yes | — | — | — | — | yes |
| CREATE MODEL VERSION | yes | — | — | — | — | yes |
| MANAGE (models) | yes | — | — | — | — | yes |
| APPLY TAG (models) | yes | — | — | — | yes | yes |

---

## Quick Reference Privilege Matrix (By Environment)

Instead of one massive matrix, here is the access broken down by catalog environment.

### 1. Sandbox Catalog
*Free experimentation, personal exploration, prototypes.*

| Persona         | Privileges Allowed |
|-----------------|--------------------|
| **Platform Admin** | `MANAGE`, `CREATE SCHEMA`, `USE CATALOG`, `BROWSE` |
| **Data Eng**    | **Full Access**: `CREATE SCHEMA`, `CREATE TABLE`, `SELECT`, `MODIFY`, `CREATE VOLUME`, `EXECUTE`, etc. |
| **Data Sci**    | **Full Access**: `CREATE SCHEMA`, `CREATE TABLE`, `SELECT`, `MODIFY`, `CREATE VOLUME`, `EXECUTE`, etc. |
| **Analyst**     | **Full Access**: `CREATE SCHEMA`, `CREATE TABLE`, `SELECT`, `MODIFY`, `CREATE VOLUME`, `EXECUTE`, etc. |
| **MLOps Engineer (Interactive)** | *No Access* |
| **MLOps Service Principal**    | *No Access* |

### 2. Development Catalog
*Active pipeline development and feature engineering.*

| Persona         | Privileges Allowed |
|-----------------|--------------------|
| **Platform Admin** | `MANAGE`, `CREATE SCHEMA`, `USE CATALOG`, `BROWSE` |
| **Data Eng**    | **Full Access**: `CREATE SCHEMA`, `CREATE TABLE`, `SELECT`, `MODIFY`, `CREATE VOLUME`, `EXECUTE`, etc. |
| **Data Sci**    | **Read Only**: `SELECT`, `EXECUTE`, `READ VOLUME`. |
| **Analyst**     | *No Access* (In-progress pipelines are unstable) |
| **MLOps Engineer (Interactive)** | **Read Only**: `SELECT`, `EXECUTE`, `READ VOLUME`. |
| **MLOps Service Principal**    | **Testing Only**: `CREATE MODEL VERSION`, `EXECUTE`, `READ/WRITE VOLUME`. |

### 3. Stage Catalog
*Pre-production validation and testing.*

| Persona         | Privileges Allowed |
|-----------------|--------------------|
| **Platform Admin** | `MANAGE`, `CREATE SCHEMA`, `USE CATALOG`, `BROWSE` |
| **Data Eng**    | **Read Only**: `SELECT`, `EXECUTE`, `READ VOLUME` |
| **Data Sci**    | **Read Only**: `SELECT`, `EXECUTE`, `READ VOLUME` |
| **Analyst**     | **Read Only**: `SELECT`, `EXECUTE`, `READ VOLUME` |
| **MLOps Engineer (Interactive)** | **Model Approver**: Read-only data access + `CREATE MODEL VERSION`, `MANAGE` (models), `APPLY TAG`. |
| **MLOps Service Principal**    | **Pipeline Writer**: `CREATE SCHEMA`, `CREATE TABLE`, `SELECT`, `MODIFY`, `EXECUTE`, etc. |

### 4. Production Catalog
*Governed production data and certified models.*

| Persona         | Privileges Allowed |
|-----------------|--------------------|
| **Platform Admin** | `MANAGE`, `CREATE SCHEMA`, `USE CATALOG`, `BROWSE` |
| **Data Eng**    | **Read Only**: `SELECT`, `EXECUTE`, `READ VOLUME` |
| **Data Sci**    | **Read Only**: `SELECT`, `EXECUTE`, `READ VOLUME` |
| **Analyst**     | **Read Only**: `SELECT`, `EXECUTE`, `READ VOLUME` |
| **MLOps Engineer (Interactive)** | **Model Tagger**: Read-only data access + `APPLY TAG` on models only. |
| **MLOps Service Principal**    | **Pipeline Writer**: `CREATE SCHEMA`, `CREATE TABLE`, `SELECT`, `MODIFY`, `EXECUTE`, etc. |

---


## Table of Contents

1. [Core Principles](#1-core-principles)
2. [Privilege Inheritance Hierarchy](#2-privilege-inheritance-hierarchy)
3. [Group & Service Principal Naming](#3-group--service-principal-naming)
4. [Catalog Topology](#4-catalog-topology)
5. [Persona Definitions & Privileges](#5-persona-definitions--privileges)
6. [Per-Catalog Access Matrix](#6-per-catalog-access-matrix)
7. [SQL GRANT Statements](#7-sql-grant-statements)
8. [Best Practices and Governance Controls](#8-best-practices-and-governance-controls)
9. [Anti-Patterns to Avoid](#9-anti-patterns-to-avoid)
10. [Open Questions to Validate](#10-open-questions-to-validate)
11. [Workspace Object Permission Matrices](#11-workspace-object-permission-matrices)
12. [Limitations and Operational Edge Cases](#12-limitations-and-operational-edge-cases)
13. [Terraform Permission Examples](#13-terraform-permission-examples)

---

## 1. Core Principles

1. **Grant to groups, never to individuals.** Use Databricks account-level groups. Individual grants are hard to audit and maintain.
2. **Strict environment boundaries.** Stage and Production are governed environments. Automation service principals execute changes; interactive users only maintain read/approve access.
3. **Privilege inheritance flows downward.** A grant on a catalog applies to all current and future schemas and objects within it. Use this to simplify management — but with care.
4. **Separate platform admins from data consumers.** Workspace admin is a platform role, not a data access role. Data must be self-granted if needed.
5. **Use service principals for automation.** CI/CD pipelines, model deployments, and scheduled jobs should run as service principals.
6. **`MANAGE` is powerful.** A user with `MANAGE` can grant privileges to themselves. Only grant it dynamically through object ownership or tightly scoped pipelines.

---

## 2. Privilege Inheritance Hierarchy

```text
Metastore
 └── Catalog         ← BROWSE, USE CATALOG, CREATE SCHEMA, MANAGE
      └── Schema      ← USE SCHEMA, CREATE TABLE, CREATE MODEL, CREATE FUNCTION,
           │              CREATE MATERIALIZED VIEW, CREATE VOLUME, MANAGE
           ├── Table / View / Materialized View  ← SELECT, MODIFY, APPLY TAG, REFRESH
           ├── Volume                             ← READ VOLUME, WRITE VOLUME
           ├── Function / UDF                     ← EXECUTE
           └── Registered Model                  ← EXECUTE, CREATE MODEL VERSION, MANAGE, APPLY TAG
```

**Inheritance rule:** Granting a privilege at a higher level (e.g., catalog) automatically applies it to all existing and future child objects within that scope.

---

## 3. Group & Service Principal Naming

Use these standardized names consistently across Unity Catalog GRANT statements.

| Group / Principal         | Type               | Purpose                                      |
|---------------------------|--------------------|----------------------------------------------|
| `grp-dbx-platform-admins` | Databricks Group   | Platform administration and infrastructure   |
| `grp-uc-data-engineers`   | Databricks Group   | Pipeline development and data engineering    |
| `grp-uc-data-scientists`  | Databricks Group   | ML experimentation and model development     |
| `grp-uc-data-analysts`    | Databricks Group   | Data exploration and business reporting      |
| `grp-uc-mlops-devops`     | Databricks Group   | Interactive MLOps/DevOps — validation and approval |
| `sp-mlops-cicd`           | Service Principal  | Automated CI/CD execution and deployment     |

> **Key distinction:** `grp-uc-mlops-devops` (interactive users) **validate and approve**; `sp-mlops-cicd` (automation) **executes** the actual writes. `sp-mlops-cicd` is the sole automation principal for `stage` and `production`.

---

## 4. Catalog Topology

| Catalog       | Purpose                                              | Write Owners                   | Read Consumers                 |
|---------------|------------------------------------------------------|--------------------------------|--------------------------------|
| `sandbox`     | Free experimentation and isolated prototyping        | Engineers, Scientists, Analysts| Engineers, Scientists, Analysts|
| `development` | Active pipeline development and feature engineering  | Engineers                      | Scientists, MLOps Interactive/SP |
| `stage`       | Pre-production validation and MLOps testing          | MLOps SP                       | All Interactive Users          |
| `production`  | Governed production data and certified models        | MLOps SP                       | All Interactive Users          |

---

## 5. Persona Definitions & Privileges

### 5.1 Platform Admin

**Role:** Platform control plane. Manages users, groups, compute, workspaces, and metastore infrastructure. Does **not** perform regular data work.

**Metastore-level grants:** `CREATE CATALOG`, `CREATE EXTERNAL LOCATION`, `CREATE STORAGE CREDENTIAL`, `CREATE SERVICE CREDENTIAL`, `CREATE CONNECTION`, `CREATE SHARE`, `CREATE RECIPIENT`, `MANAGE ALLOWLIST`
**Catalog-level grants (all catalogs):** `USE CATALOG`, `BROWSE`, `CREATE SCHEMA`, `MANAGE`

> **Important:** Workspace admins do **not** have `SELECT` or `MODIFY` by default. They must explicitly self-grant (which creates an audit trail) using their `MANAGE` privilege. 

---

### 5.2 Data Engineer

**Role:** Build and maintain ETL/ELT pipelines, create and own domain schemas in `development`. Full write access in `sandbox` and `development`; strictly read-only in `stage` and `production`.

**Privileges across environments:**
*   **Sandbox / Development:** Full capabilities (`CREATE SCHEMA`, `CREATE TABLE`, `CREATE VOLUME`, `SELECT`, `MODIFY`, `EXECUTE`, etc.).
*   **Stage / Production:** Read-only (`USE CATALOG`, `BROWSE`, `USE SCHEMA`, `SELECT`, `READ VOLUME`, `EXECUTE`). No `MODIFY` or schema creation power (CI/CD handles this).

---

### 5.3 Data Scientist

**Role:** Explore and read governed data; build features, experiments, and ML models in isolated sandbox schemas; register models for MLOps handoff.

**Privileges across environments:**
*   **Sandbox:** Full capabilities across the entire Sandbox catalog (managed via workspace binding).
*   **Development / Stage / Production:** Read-only (`USE CATALOG`, `BROWSE`, `USE SCHEMA`, `SELECT`, `READ VOLUME`, `EXECUTE`). No `MODIFY` power.

---

### 5.4 Data Analyst

**Role:** Query curated data for reporting, dashboarding, and business insights.

**Privileges across environments:**
*   **Sandbox:** Full capabilities across the entire Sandbox catalog (managed via workspace binding).
*   **Stage / Production:** Read-only (`USE CATALOG`, `BROWSE`, `USE SCHEMA`, `SELECT`, `READ VOLUME`, `EXECUTE` functions).
*   **Development:** No access. In-progress pipelines are unstable and should not be used for business intelligence.

---

### 5.5 MLOps / DevOps Engineer (Interactive)

**Role:** Validate, approve, and oversee model deployments and pipeline operations. MLOps Engineers interactively review models in `stage` before production promotion. **Does not write to production data** — that is the CI/CD service principal's job.

**Privileges across environments:**
*   **Sandbox:** No access.
*   **Development:** Read-only + model inspection.
*   **Stage:** Read-only data + active model approval capabilities (`CREATE MODEL VERSION`, `MANAGE`, `APPLY TAG` on models). 
*   **Production:** Read-only data + model governance tracking (`APPLY TAG` on models directly). No data writes or model modifications.

---

### 5.6 MLOps CI/CD Service Principal

**Role:** Single pipeline executor for all governed deployment operations. Fully automates CI/CD across higher environments. 

**Privileges across environments:**
*   **Sandbox:** No access.
*   **Development:** Testing integration (`CREATE MODEL VERSION`, `READ VOLUME`, `WRITE VOLUME`).
*   **Stage / Production:** Full deployment automation (`CREATE SCHEMA`, `CREATE TABLE`, `CREATE MODEL`, `MANAGE` models, `SELECT`, `MODIFY`, `EXECUTE`, etc.).

---

## 6. Per-Catalog Access Matrix

This confirms write boundaries unambiguously.

| Persona                     | `sandbox`                 | `development`                 | `stage`                               | `production`                          |
|-----------------------------|---------------------------|-------------------------------|---------------------------------------|---------------------------------------|
| **Platform Admin**          | MANAGE                    | MANAGE                        | MANAGE                                | MANAGE                                |
| **Data Engineer**           | **Writer** (All schemas)  | **Writer** (All schemas)      | *Reader*                              | *Reader*                              |
| **Data Scientist**          | **Writer** (All schemas)  | *Reader*                    | *Reader*                              | *Reader*                              |
| **Data Analyst**            | **Writer** (All schemas)  | No access                | *Reader*                              | *Reader*                              |
| **grp-uc-mlops-devops**     | No access                 | *Reader*                      | *Reader* + **Model Approver**         | *Reader* + **Model Tagger**           |
| **sp-mlops-cicd**           | No access                 | Integration Tester            | **Governed Writer**                   | **Governed Writer**                   |

---

## 7. SQL GRANT Statements

### 7.1 Platform Admin Setup (run as metastore admin)

```sql
GRANT MANAGE, USE CATALOG, BROWSE, CREATE SCHEMA ON CATALOG sandbox TO `grp-dbx-platform-admins`;
GRANT MANAGE, USE CATALOG, BROWSE, CREATE SCHEMA ON CATALOG development TO `grp-dbx-platform-admins`;
GRANT MANAGE, USE CATALOG, BROWSE, CREATE SCHEMA ON CATALOG stage TO `grp-dbx-platform-admins`;
GRANT MANAGE, USE CATALOG, BROWSE, CREATE SCHEMA ON CATALOG production TO `grp-dbx-platform-admins`;
```

### 7.2 Data Engineer

```sql
-- sandbox: full access
GRANT USE CATALOG, BROWSE, CREATE SCHEMA ON CATALOG sandbox TO `grp-uc-data-engineers`;
GRANT USE SCHEMA, CREATE TABLE, CREATE MATERIALIZED VIEW, CREATE VOLUME, CREATE FUNCTION, CREATE MODEL ON ALL SCHEMAS IN CATALOG sandbox TO `grp-uc-data-engineers`;
GRANT SELECT, MODIFY ON ALL TABLES IN CATALOG sandbox TO `grp-uc-data-engineers`;
GRANT EXECUTE ON ALL FUNCTIONS IN CATALOG sandbox TO `grp-uc-data-engineers`;
GRANT EXECUTE, CREATE MODEL VERSION, APPLY TAG ON ALL MODELS IN CATALOG sandbox TO `grp-uc-data-engineers`;
GRANT READ VOLUME, WRITE VOLUME ON ALL VOLUMES IN CATALOG sandbox TO `grp-uc-data-engineers`;

-- development: full access
GRANT USE CATALOG, BROWSE, CREATE SCHEMA ON CATALOG development TO `grp-uc-data-engineers`;
GRANT USE SCHEMA, CREATE TABLE, CREATE MATERIALIZED VIEW, CREATE VOLUME, CREATE FUNCTION, CREATE MODEL ON ALL SCHEMAS IN CATALOG development TO `grp-uc-data-engineers`;
GRANT SELECT, MODIFY ON ALL TABLES IN CATALOG development TO `grp-uc-data-engineers`;
GRANT EXECUTE ON ALL FUNCTIONS IN CATALOG development TO `grp-uc-data-engineers`;
GRANT EXECUTE, CREATE MODEL VERSION, APPLY TAG ON ALL MODELS IN CATALOG development TO `grp-uc-data-engineers`;
GRANT READ VOLUME, WRITE VOLUME ON ALL VOLUMES IN CATALOG development TO `grp-uc-data-engineers`;

-- stage: strict read-only
GRANT USE CATALOG, BROWSE ON CATALOG stage TO `grp-uc-data-engineers`;
GRANT USE SCHEMA ON ALL SCHEMAS IN CATALOG stage TO `grp-uc-data-engineers`;
GRANT SELECT ON ALL TABLES IN CATALOG stage TO `grp-uc-data-engineers`;
GRANT EXECUTE ON ALL FUNCTIONS IN CATALOG stage TO `grp-uc-data-engineers`;
GRANT EXECUTE ON ALL MODELS IN CATALOG stage TO `grp-uc-data-engineers`;
GRANT READ VOLUME ON ALL VOLUMES IN CATALOG stage TO `grp-uc-data-engineers`;

-- production: strict read-only
GRANT USE CATALOG, BROWSE ON CATALOG production TO `grp-uc-data-engineers`;
GRANT USE SCHEMA ON ALL SCHEMAS IN CATALOG production TO `grp-uc-data-engineers`;
GRANT SELECT ON ALL TABLES IN CATALOG production TO `grp-uc-data-engineers`;
GRANT EXECUTE ON ALL FUNCTIONS IN CATALOG production TO `grp-uc-data-engineers`;
GRANT EXECUTE ON ALL MODELS IN CATALOG production TO `grp-uc-data-engineers`;
GRANT READ VOLUME ON ALL VOLUMES IN CATALOG production TO `grp-uc-data-engineers`;
```

### 7.3 Data Scientist

```sql
-- sandbox: full access
GRANT USE CATALOG, BROWSE, CREATE SCHEMA ON CATALOG sandbox TO `grp-uc-data-scientists`;
GRANT USE SCHEMA, CREATE TABLE, CREATE FUNCTION, CREATE MODEL, CREATE VOLUME ON ALL SCHEMAS IN CATALOG sandbox TO `grp-uc-data-scientists`;
GRANT SELECT, MODIFY ON ALL TABLES IN CATALOG sandbox TO `grp-uc-data-scientists`;
GRANT EXECUTE ON ALL FUNCTIONS IN CATALOG sandbox TO `grp-uc-data-scientists`;
GRANT EXECUTE, CREATE MODEL VERSION, APPLY TAG ON ALL MODELS IN CATALOG sandbox TO `grp-uc-data-scientists`;
GRANT READ VOLUME, WRITE VOLUME ON ALL VOLUMES IN CATALOG sandbox TO `grp-uc-data-scientists`;

-- development: read access
GRANT USE CATALOG, BROWSE ON CATALOG development TO `grp-uc-data-scientists`;
GRANT USE SCHEMA ON ALL SCHEMAS IN CATALOG development TO `grp-uc-data-scientists`;
GRANT SELECT ON ALL TABLES IN CATALOG development TO `grp-uc-data-scientists`;
GRANT EXECUTE ON ALL FUNCTIONS IN CATALOG development TO `grp-uc-data-scientists`;
GRANT EXECUTE ON ALL MODELS IN CATALOG development TO `grp-uc-data-scientists`;
GRANT READ VOLUME ON ALL VOLUMES IN CATALOG development TO `grp-uc-data-scientists`;

-- stage: read access
GRANT USE CATALOG, BROWSE ON CATALOG stage TO `grp-uc-data-scientists`;
GRANT USE SCHEMA ON ALL SCHEMAS IN CATALOG stage TO `grp-uc-data-scientists`;
GRANT SELECT ON ALL TABLES IN CATALOG stage TO `grp-uc-data-scientists`;
GRANT EXECUTE ON ALL FUNCTIONS IN CATALOG stage TO `grp-uc-data-scientists`;
GRANT EXECUTE ON ALL MODELS IN CATALOG stage TO `grp-uc-data-scientists`;
GRANT READ VOLUME ON ALL VOLUMES IN CATALOG stage TO `grp-uc-data-scientists`;

-- production: read access
GRANT USE CATALOG, BROWSE ON CATALOG production TO `grp-uc-data-scientists`;
GRANT USE SCHEMA ON ALL SCHEMAS IN CATALOG production TO `grp-uc-data-scientists`;
GRANT SELECT ON ALL TABLES IN CATALOG production TO `grp-uc-data-scientists`;
GRANT EXECUTE ON ALL FUNCTIONS IN CATALOG production TO `grp-uc-data-scientists`;
GRANT EXECUTE ON ALL MODELS IN CATALOG production TO `grp-uc-data-scientists`;
GRANT READ VOLUME ON ALL VOLUMES IN CATALOG production TO `grp-uc-data-scientists`;
```

### 7.4 Data Analyst

```sql
-- sandbox: full access
GRANT USE CATALOG, BROWSE, CREATE SCHEMA ON CATALOG sandbox TO `grp-uc-data-analysts`;
GRANT USE SCHEMA, CREATE TABLE, CREATE FUNCTION, CREATE VOLUME ON ALL SCHEMAS IN CATALOG sandbox TO `grp-uc-data-analysts`;
GRANT SELECT, MODIFY ON ALL TABLES IN CATALOG sandbox TO `grp-uc-data-analysts`;
GRANT EXECUTE ON ALL FUNCTIONS IN CATALOG sandbox TO `grp-uc-data-analysts`;
GRANT READ VOLUME, WRITE VOLUME ON ALL VOLUMES IN CATALOG sandbox TO `grp-uc-data-analysts`;

-- stage: read only
GRANT USE CATALOG, BROWSE ON CATALOG stage TO `grp-uc-data-analysts`;
GRANT USE SCHEMA ON ALL SCHEMAS IN CATALOG stage TO `grp-uc-data-analysts`;
GRANT SELECT ON ALL TABLES IN CATALOG stage TO `grp-uc-data-analysts`;
GRANT EXECUTE ON ALL FUNCTIONS IN CATALOG stage TO `grp-uc-data-analysts`;
GRANT READ VOLUME ON ALL VOLUMES IN CATALOG stage TO `grp-uc-data-analysts`;

-- production: read only
GRANT USE CATALOG, BROWSE ON CATALOG production TO `grp-uc-data-analysts`;
GRANT USE SCHEMA ON ALL SCHEMAS IN CATALOG production TO `grp-uc-data-analysts`;
GRANT SELECT ON ALL TABLES IN CATALOG production TO `grp-uc-data-analysts`;
GRANT EXECUTE ON ALL FUNCTIONS IN CATALOG production TO `grp-uc-data-analysts`;
GRANT READ VOLUME ON ALL VOLUMES IN CATALOG production TO `grp-uc-data-analysts`;
```

### 7.5 MLOps / DevOps Engineer (Human)

```sql
-- development: read only setup
GRANT USE CATALOG, BROWSE ON CATALOG development TO `grp-uc-mlops-devops`;
GRANT USE SCHEMA ON ALL SCHEMAS IN CATALOG development TO `grp-uc-mlops-devops`;
GRANT SELECT ON ALL TABLES IN CATALOG development TO `grp-uc-mlops-devops`;
GRANT EXECUTE ON ALL FUNCTIONS IN CATALOG development TO `grp-uc-mlops-devops`;
GRANT EXECUTE ON ALL MODELS IN CATALOG development TO `grp-uc-mlops-devops`;
GRANT READ VOLUME ON ALL VOLUMES IN CATALOG development TO `grp-uc-mlops-devops`;

-- stage: read + explicit model approval/validation authority
GRANT USE CATALOG, BROWSE ON CATALOG stage TO `grp-uc-mlops-devops`;
GRANT USE SCHEMA ON ALL SCHEMAS IN CATALOG stage TO `grp-uc-mlops-devops`;
GRANT SELECT ON ALL TABLES IN CATALOG stage TO `grp-uc-mlops-devops`;
GRANT EXECUTE ON ALL FUNCTIONS IN CATALOG stage TO `grp-uc-mlops-devops`;
GRANT EXECUTE, CREATE MODEL VERSION, MANAGE, APPLY TAG ON ALL MODELS IN CATALOG stage TO `grp-uc-mlops-devops`;
GRANT READ VOLUME ON ALL VOLUMES IN CATALOG stage TO `grp-uc-mlops-devops`;

-- production: read + diagnostic tagging
GRANT USE CATALOG, BROWSE ON CATALOG production TO `grp-uc-mlops-devops`;
GRANT USE SCHEMA ON ALL SCHEMAS IN CATALOG production TO `grp-uc-mlops-devops`;
GRANT SELECT ON ALL TABLES IN CATALOG production TO `grp-uc-mlops-devops`;
GRANT EXECUTE ON ALL FUNCTIONS IN CATALOG production TO `grp-uc-mlops-devops`;
GRANT EXECUTE, APPLY TAG ON ALL MODELS IN CATALOG production TO `grp-uc-mlops-devops`;
GRANT READ VOLUME ON ALL VOLUMES IN CATALOG production TO `grp-uc-mlops-devops`;
```

### 7.6 MLOps CI/CD Service Principal

```sql
-- development: CI/CD testing integration
GRANT USE CATALOG, BROWSE ON CATALOG development TO `sp-mlops-cicd`;
GRANT USE SCHEMA ON ALL SCHEMAS IN CATALOG development TO `sp-mlops-cicd`;
GRANT SELECT ON ALL TABLES IN CATALOG development TO `sp-mlops-cicd`;
GRANT EXECUTE, CREATE MODEL VERSION ON ALL MODELS IN CATALOG development TO `sp-mlops-cicd`;
GRANT READ VOLUME, WRITE VOLUME ON ALL VOLUMES IN CATALOG development TO `sp-mlops-cicd`;

-- stage: CI/CD full pipeline environment execution
GRANT USE CATALOG, BROWSE, CREATE SCHEMA ON CATALOG stage TO `sp-mlops-cicd`;
GRANT USE SCHEMA, CREATE TABLE, CREATE MATERIALIZED VIEW, CREATE FUNCTION, CREATE MODEL, CREATE VOLUME ON ALL SCHEMAS IN CATALOG stage TO `sp-mlops-cicd`;
GRANT SELECT, MODIFY ON ALL TABLES IN CATALOG stage TO `sp-mlops-cicd`;
GRANT EXECUTE ON ALL FUNCTIONS IN CATALOG stage TO `sp-mlops-cicd`;
GRANT EXECUTE, CREATE MODEL VERSION, MANAGE, APPLY TAG ON ALL MODELS IN CATALOG stage TO `sp-mlops-cicd`;
GRANT READ VOLUME, WRITE VOLUME ON ALL VOLUMES IN CATALOG stage TO `sp-mlops-cicd`;

-- production: CI/CD sole pipeline environment execution
GRANT USE CATALOG, BROWSE, CREATE SCHEMA ON CATALOG production TO `sp-mlops-cicd`;
GRANT USE SCHEMA, CREATE TABLE, CREATE MATERIALIZED VIEW, CREATE FUNCTION, CREATE MODEL, CREATE VOLUME ON ALL SCHEMAS IN CATALOG production TO `sp-mlops-cicd`;
GRANT SELECT, MODIFY ON ALL TABLES IN CATALOG production TO `sp-mlops-cicd`;
GRANT EXECUTE ON ALL FUNCTIONS IN CATALOG production TO `sp-mlops-cicd`;
GRANT EXECUTE, CREATE MODEL VERSION, MANAGE, APPLY TAG ON ALL MODELS IN CATALOG production TO `sp-mlops-cicd`;
GRANT READ VOLUME, WRITE VOLUME ON ALL VOLUMES IN CATALOG production TO `sp-mlops-cicd`;
```

---

## 8. Best Practices and Governance Controls

*   **Grant to groups, never to individuals.** Use Databricks account-level groups synced from your IdP. Individual grants are hard to audit and create management overhead.
*   **Use account-level identities and group-based grants:** Principals must be account-level to receive Unity Catalog privileges; avoid direct grants to users when possible and manage groups in your IdP.
*   **Use folder-based permission inheritance.** Organize notebooks, queries, and dashboards into team/project folders. Set permissions at the folder level to simplify management at scale.
*   **Make automation the writer in Stage and Production.** Jobs and deployments should run as a service principal so success doesn't depend on humans. Humans remain read-only viewers/approvers.
*   **Use workspace bindings for environment isolation:** By default, catalogs/external locations are accessible from any workspace attached to the metastore; bindings can isolate prod catalogs to prod workspaces and even enforce read-only workspace access levels. Critically, bindings override user-level permissions.
*   **Keep workspace admins rare and apply `RestrictWorkspaceAdmins`.** Prevent silent admin escalation workflows like reassigning owners or changing job `Run as` to a powerful principal.
*   **Assign `IS_OWNER` on jobs to service principals.** Production jobs should be owned by the CI/CD service principal, not individual users. Prevents breakage when employees leave.
*   **Enable visibility controls.** Turn on Workspace Visibility Control, Cluster Visibility Control, and Jobs Visibility Control to prevent users from seeing objects they have no permissions on.
*   **Constrain compute creation using policies.** Non-admin users must be granted policy permissions to use specific compute templates. Limits cost and enforces security configurations.
*   **Treat external locations as high-risk infrastructure:** External locations can cover broad storage paths; best practice is to prevent bypassing Unity Catalog controls (for example, avoid DBFS mounts for the same storage). Limit who can create external locations and who has direct storage access.
*   **Prefer managed tables/volumes when possible:** Managed tables/volumes provide stronger governance integration and are recommended for most new workloads; external tables/volumes are often transitional for migration or specific requirements.
*   **Treat personal access tokens as a privileged capability.** Apply the `CAN_USE` token capability strictly to automation identities and short-lived break-glass scopes.
*   **Automate permission management.** Use Terraform (`databricks_permissions` resource), Databricks Asset Bundles, or the Permissions API 2.0 to enforce permissions as code.
*   **Use governed tags and ABAC strategically:** Governed tags enforce consistent metadata and can support ABAC policies for scalable masking/filtering. Tag data is plain text, so avoid sensitive values in tags.
*   **Audit secret scope access carefully.** Restrict `CAN_MANAGE` on clusters that process secrets, and set `spark.databricks.acl.needAdminPermissionToViewLogs` to `true`.
*   **SQL Warehouse sizing — separate `CAN_USE` from `CAN_MANAGE`.** Give query consumers `CAN_USE`. Only platform admins should have `CAN_MANAGE` (which allows stopping, deleting, and resizing warehouses).
*   **Constrain workspace-admin power:** Keep workspace admins few, and consider enabling `RestrictWorkspaceAdmins` to limit job/run-as and service principal token capabilities.

---

## 9. Anti-Patterns to Avoid

| Anti-Pattern | Why It's Dangerous | Better Approach |
|---|---|---|
| `GRANT ALL PRIVILEGES ON CATALOG ... TO group` | `ALL PRIVILEGES` does not include `MANAGE` — false completeness. Too broad. | Grant per-securable-level specifically. |
| Admin self-granting `MODIFY` implicitly | Unaudited backdoor mix of platform and data role. | Admin explicitly uses `MANAGE` to grant themselves rights (creating an audit trail). |
| Using user accounts for CI/CD operations | Sessions expire, credentials drop, no pipeline isolation. | Always authenticate using `sp-mlops-cicd`. |
| Human data engineers modifying `stage` | Prevents achieving proper CI/CD immutability. | Stage must mimic production. Humans read, CI/CD automated pipeline handles all writes. |
| Engineer creation of schemas in `stage`/`production` | Uncontrolled resource sprawl outside version history. | Only the `sp-mlops-cicd` automation can deploy objects natively in governed environments. |
| Granting `CAN_MANAGE` on clusters to all users | Allows users to resize, edit, and modify cluster configs; runaway costs and security exposure. | Use `CAN_ATTACH_TO` for consumers; restrict `CAN_MANAGE` to admins and infra teams. |
| Using `CAN_MANAGE_RUN` instead of `IS_OWNER` for SP on Jobs | `CAN_MANAGE_RUN` cannot edit job settings. SP needs `IS_OWNER` to update schedules and configurations via CI/CD. | Assign `IS_OWNER` to the deploying service principal. |
| Giving `CAN_EDIT` on notebooks in Production | Humans editing production notebooks breaks CI/CD immutability and audit trails. | `CAN_READ` only in Production; all changes deployed via CI/CD. |
| Using human accounts for Secrets `WRITE` in Production | Credential rotation and access become tied to human lifecycle events. | Only service principals should `WRITE` secrets in governed environments. |
| Granting `CAN_MANAGE` on SQL Warehouses broadly | Allows stopping, deleting, and resizing warehouses; can break shared analytics. | Use `CAN_USE` for query consumers; `CAN_MANAGE` only for admins. |
| No permissions on DLT in Production for Data Eng | Data engineers cannot view pipeline status or troubleshoot failures. | Grant `CAN_VIEW` so engineers can monitor without modifying. |
| Using `CAN_VIEW` for Alerts (invalid permission) | `CAN_VIEW` is not a valid Alerts permission; API calls will fail silently or error. | Use `CAN_RUN` as the minimum valid level for Alerts. |
| Using `CAN_EDIT` for Jobs or DLT Pipelines (invalid permission) | `CAN_EDIT` is not a valid permission for Jobs or DLT Pipelines; API calls will fail. | Use `CAN_MANAGE_RUN` for Jobs or `CAN_RUN` for DLT as the intermediate level. |

---

*Last updated: 2026-03-05*
*Based on Azure Databricks Unity Catalog Privileges guidelines and least privilege architecture schemas.*

---

## 10. Open Questions to Validate

This model provides an enterprise-ready baseline. To ensure it accurately reflects your specific constraints, please validate the following questions:

*   **How many workspaces do you run** (dev/test/prod per team, or shared), and do you want hard environment isolation via workspace bindings?
*   **Workspace isolation strategy:** Do you use separate workspaces per environment (sandbox/dev/stage/prod), or shared workspaces with folder-based segregation?
*   **What is your catalog strategy:** catalog-per-environment, catalog-per-domain, or both (domain catalogs inside env workspaces)?
*   **Do data engineers need direct management delegation** (schema ownership / `MANAGE`) or do you want a central data governance team to own grants?
*   **Cluster policy alignment:** Which cluster policies are in place, and do they need different ACL levels for different teams?
*   **`CAN_RESTART` usage:** Should any personas have `CAN_RESTART` (terminate + start clusters) instead of `CAN_ATTACH_TO`? This model uses `CAN_ATTACH_TO` conservatively.
*   **Will analysts and scientists need governed file access** (volumes), and what are the main file-driven use cases (ingestion landing zones, feature artifacts, reporting exports)?
*   **SQL Warehouse tiers:** Do analysts need `CAN_MONITOR` (monitoring tab + run queries) or is `CAN_USE` (run queries only) sufficient?
*   **Dashboard type:** Are you using AI/BI Dashboards (`CAN_READ` API string) or Legacy Dashboards (`CAN_VIEW` API string)? This model assumes AI/BI Dashboards.
*   **Git workflow for Repos:** Who needs `CAN_MANAGE` on repos (branch switching, pull/push)? This model restricts that to admins and the service principal.
*   **What is your model lifecycle:** do data scientists publish model versions directly to prod, or is there a strict MLOps approval + CI/CD promotion gate?
*   **Model serving endpoints:** Do you use Model Serving Endpoints? If so, their ACLs (`CAN_VIEW`, `CAN_QUERY`, `CAN_MANAGE`) should be added to this model.
*   **Analyst access in Development:** This model blocks analysts from the Development workspace entirely. Should they have read-only access for early data exploration?
*   **DLT pipeline ownership:** Will ownership always be the MLOps service principal in governed environments? This limits human reading of log streams during operations.
*   **Are you planning ABAC** (governed tags + policies), and which classifications matter (PII/PHI/PCI, internal-only, certified/deprecated)?

---

## 11. Workspace Object Permission Matrices

These tables cover workspace-level object permissions (separate from Unity Catalog data privileges). All values use exact API-level permission names. `—` indicates no access is granted.

> **Permission level hierarchy (highest → lowest):**
> - Clusters / Instance Pools: `CAN_MANAGE` > `CAN_RESTART` > `CAN_ATTACH_TO`
> - Jobs / DLT Pipelines: `IS_OWNER` > `CAN_MANAGE` > `CAN_MANAGE_RUN` > `CAN_VIEW`
> - Notebooks / Directories / Repos / ML Experiments: `CAN_MANAGE` > `CAN_EDIT` > `CAN_READ` > `CAN_RUN`
> - SQL (Warehouses / Queries / Dashboards / Alerts): `CAN_MANAGE` > `CAN_EDIT` > `CAN_RUN` > `CAN_VIEW` / `CAN_USE`
> - Secrets: `MANAGE` > `WRITE` > `READ`

### 11.1 SANDBOX

*Purpose: Free experimentation, personal exploration, prototypes.*

| Object | Platform Admin | Data Eng | Data Sci | Analyst | MLOps Eng | MLOps SP |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| Clusters | CAN_MANAGE | CAN_MANAGE | CAN_ATTACH_TO | CAN_ATTACH_TO | CAN_MANAGE | — |
| Jobs | CAN_MANAGE | CAN_MANAGE | CAN_MANAGE_RUN | CAN_MANAGE_RUN | CAN_MANAGE | — |
| DLT Pipelines | CAN_MANAGE | CAN_MANAGE | CAN_RUN | CAN_VIEW | CAN_MANAGE | — |
| Directories | CAN_MANAGE | CAN_EDIT | CAN_EDIT | CAN_READ | CAN_EDIT | — |
| Notebooks | CAN_MANAGE | CAN_EDIT | CAN_EDIT | CAN_READ | CAN_EDIT | — |
| ML Experiments | CAN_MANAGE | CAN_EDIT | CAN_EDIT | CAN_READ | CAN_EDIT | — |
| Dashboards | CAN_MANAGE | CAN_EDIT | CAN_EDIT | CAN_EDIT | CAN_EDIT | — |
| Queries | CAN_MANAGE | CAN_EDIT | CAN_EDIT | CAN_RUN | CAN_EDIT | — |
| Alerts | CAN_MANAGE | CAN_MANAGE | CAN_RUN | CAN_RUN | CAN_MANAGE | — |
| SQL Warehouses | CAN_MANAGE | CAN_USE | CAN_USE | CAN_USE | CAN_USE | — |
| Repos | CAN_MANAGE | CAN_EDIT | CAN_EDIT | CAN_READ | CAN_EDIT | — |
| Instance Pools | CAN_MANAGE | CAN_ATTACH_TO | CAN_ATTACH_TO | CAN_ATTACH_TO | CAN_ATTACH_TO | — |

### 11.2 DEVELOPMENT

*Purpose: Controlled engineering development, active pipeline building.*

| Object | Platform Admin | Data Eng | Data Sci | Analyst | MLOps Eng | MLOps SP |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| Clusters | CAN_MANAGE | CAN_ATTACH_TO | CAN_ATTACH_TO | — | CAN_ATTACH_TO | CAN_ATTACH_TO |
| Jobs | CAN_MANAGE | CAN_MANAGE | CAN_MANAGE_RUN | — | CAN_MANAGE_RUN | IS_OWNER |
| DLT Pipelines | CAN_MANAGE | CAN_MANAGE | CAN_VIEW | — | CAN_RUN | IS_OWNER |
| Directories | CAN_MANAGE | CAN_EDIT | CAN_EDIT | — | CAN_EDIT | CAN_EDIT |
| Notebooks | CAN_MANAGE | CAN_EDIT | CAN_EDIT | — | CAN_EDIT | CAN_EDIT |
| ML Experiments | CAN_MANAGE | CAN_EDIT | CAN_EDIT | — | CAN_EDIT | CAN_EDIT |
| Dashboards | CAN_MANAGE | CAN_EDIT | CAN_EDIT | — | CAN_EDIT | CAN_EDIT |
| Queries | CAN_MANAGE | CAN_EDIT | CAN_EDIT | — | CAN_EDIT | CAN_EDIT |
| Alerts | CAN_MANAGE | CAN_MANAGE | CAN_RUN | — | CAN_MANAGE | — |
| Secrets | MANAGE | — | — | — | WRITE | WRITE |
| SQL Warehouses | CAN_MANAGE | CAN_USE | CAN_USE | — | CAN_USE | CAN_USE |
| Repos | CAN_MANAGE | CAN_EDIT | CAN_EDIT | — | CAN_EDIT | CAN_EDIT |
| Instance Pools | CAN_MANAGE | CAN_ATTACH_TO | CAN_ATTACH_TO | — | CAN_ATTACH_TO | CAN_ATTACH_TO |

### 11.3 STAGE

*Purpose: Pre-production validation and testing. Governed environment strictly driven by automation. Interactive users validate/read.*

| Object | Platform Admin | Data Eng | Data Sci | Analyst | MLOps Eng | MLOps SP |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| Clusters | CAN_MANAGE | CAN_ATTACH_TO | CAN_ATTACH_TO | CAN_ATTACH_TO | CAN_ATTACH_TO | CAN_ATTACH_TO |
| Jobs | CAN_MANAGE | CAN_VIEW | CAN_VIEW | CAN_VIEW | CAN_MANAGE_RUN | IS_OWNER |
| DLT Pipelines | CAN_MANAGE | CAN_VIEW | CAN_VIEW | CAN_VIEW | CAN_RUN | IS_OWNER |
| Directories | CAN_MANAGE | CAN_READ | CAN_READ | CAN_READ | CAN_READ | CAN_EDIT |
| Notebooks | CAN_MANAGE | CAN_READ | CAN_READ | CAN_READ | CAN_READ | CAN_EDIT |
| ML Experiments | CAN_MANAGE | CAN_READ | CAN_READ | CAN_READ | CAN_READ | CAN_EDIT |
| Dashboards | CAN_MANAGE | CAN_READ | CAN_READ | CAN_RUN | CAN_READ | CAN_EDIT |
| Queries | CAN_MANAGE | CAN_VIEW | CAN_VIEW | CAN_RUN | CAN_VIEW | CAN_EDIT |
| Alerts | CAN_MANAGE | CAN_RUN | CAN_RUN | CAN_RUN | CAN_MANAGE | — |
| Secrets | MANAGE | — | — | — | READ | READ |
| SQL Warehouses | CAN_MANAGE | CAN_USE | CAN_USE | CAN_USE | CAN_USE | CAN_USE |
| Repos | CAN_MANAGE | CAN_READ | CAN_READ | CAN_READ | CAN_READ | CAN_EDIT |
| Instance Pools | CAN_MANAGE | CAN_ATTACH_TO | CAN_ATTACH_TO | CAN_ATTACH_TO | CAN_ATTACH_TO | CAN_ATTACH_TO |

### 11.4 PRODUCTION

*Purpose: Governed production data and workloads. Driven entirely by automation.*

| Object | Platform Admin | Data Eng | Data Sci | Analyst | MLOps Eng | MLOps SP |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| Clusters | CAN_MANAGE | CAN_ATTACH_TO | CAN_ATTACH_TO | CAN_ATTACH_TO | CAN_ATTACH_TO | CAN_ATTACH_TO |
| Jobs | CAN_MANAGE | CAN_VIEW | CAN_VIEW | CAN_VIEW | CAN_VIEW | IS_OWNER |
| DLT Pipelines | CAN_MANAGE | CAN_VIEW | CAN_VIEW | CAN_VIEW | CAN_VIEW | IS_OWNER |
| Directories | CAN_MANAGE | CAN_READ | CAN_READ | CAN_READ | CAN_READ | CAN_EDIT |
| Notebooks | CAN_MANAGE | CAN_READ | CAN_READ | CAN_READ | CAN_READ | CAN_EDIT |
| ML Experiments | CAN_MANAGE | CAN_READ | CAN_READ | CAN_READ | CAN_READ | CAN_EDIT |
| Dashboards | CAN_MANAGE | CAN_READ | CAN_READ | CAN_RUN | CAN_READ | CAN_EDIT |
| Queries | CAN_MANAGE | CAN_VIEW | CAN_VIEW | CAN_RUN | CAN_VIEW | CAN_EDIT |
| Alerts | CAN_MANAGE | CAN_RUN | CAN_RUN | CAN_RUN | CAN_RUN | CAN_MANAGE |
| Secrets | MANAGE | — | — | — | READ | READ |
| SQL Warehouses | CAN_MANAGE | CAN_USE | CAN_USE | CAN_USE | CAN_USE | CAN_USE |
| Repos | CAN_MANAGE | CAN_READ | CAN_READ | CAN_READ | CAN_READ | CAN_EDIT |
| Instance Pools | CAN_MANAGE | CAN_ATTACH_TO | CAN_ATTACH_TO | CAN_ATTACH_TO | CAN_ATTACH_TO | CAN_ATTACH_TO |

---

## 12. Limitations and Operational Edge Cases

*   **ACL feature gating.** Access control features may depend on plan tier (Premium or above). For upgraded workspaces, ACL settings can start disabled. Verify the "Clusters, Pools, Jobs Access Control" toggle is enabled before relying on permission enforcement.
*   **Job execution identity is not the clicker.** Jobs run as the configured *Run as* principal. Giving someone `CAN_MANAGE_RUN` rights can indirectly expose data that only the *Run as* identity can read. Production job *Run as* should always be `sp-mlops-cicd`.
*   **Admin overreach risk.** By default, workspace admins can change owners and *Run as* settings broadly. Enable `RestrictWorkspaceAdmins` to limit their ability to manipulate job *Run as* or service principal tokens.
*   **Cluster start friction.** Users with only `CAN_ATTACH_TO` cannot auto-start a stopped cluster. Starting/restarting requires `CAN_RESTART` or `CAN_MANAGE`. Plan cluster auto-start policies accordingly.
*   **Logs and secrets exposure.** Secrets are not redacted in driver log streams. In legacy access modes, `CAN_ATTACH_TO` can expose sensitive keys. Treat `CAN_ATTACH_TO` as a permission with security consequences, and set `spark.databricks.acl.needAdminPermissionToViewLogs` to `true` for sensitive clusters.
*   **DLT driver logs behavior.** For Unity Catalog-enabled pipelines, only pipeline owners and workspace admins can view some driver logs by default. This confirms the service principal should be the exclusive owner in Stage and Production environments.

---

## 13. Terraform Permission Examples

### 13.1 Job Permissions (Production)

```hcl
resource "databricks_permissions" "prod_job" {
  job_id = databricks_job.etl_pipeline.id

  access_control {
    group_name       = "grp-ws-data-engineers"
    permission_level = "CAN_VIEW"
  }
  access_control {
    service_principal_name = "sp-mlops-cicd"
    permission_level       = "IS_OWNER"
  }
}
```

### 13.2 Cluster Permissions (Development)

```hcl
resource "databricks_permissions" "dev_cluster" {
  cluster_id = databricks_cluster.dev_shared.id

  access_control {
    group_name       = "grp-ws-data-engineers"
    permission_level = "CAN_ATTACH_TO"
  }
  access_control {
    group_name       = "grp-ws-data-scientists"
    permission_level = "CAN_ATTACH_TO"
  }
}
```

### 13.3 SQL Warehouse Permissions

```hcl
resource "databricks_permissions" "sql_warehouse" {
  sql_endpoint_id = databricks_sql_endpoint.shared.id

  access_control {
    group_name       = "grp-ws-data-analysts"
    permission_level = "CAN_USE"
  }
  access_control {
    group_name       = "grp-dbx-platform-admins"
    permission_level = "CAN_MANAGE"
  }
}
```

### 13.4 Secret Scope Permissions

```hcl
resource "databricks_secret_acl" "prod_secrets" {
  scope      = "production-credentials"
  principal  = "sp-mlops-cicd"
  permission = "WRITE"
}
```

### 13.5 Notebook Permissions (Production)

```hcl
resource "databricks_permissions" "prod_notebooks" {
  directory_path = "/Workspace/prod/pipelines"

  access_control {
    group_name       = "grp-ws-data-engineers"
    permission_level = "CAN_READ"
  }
  access_control {
    service_principal_name = "sp-mlops-cicd"
    permission_level       = "CAN_EDIT"
  }
}
```
