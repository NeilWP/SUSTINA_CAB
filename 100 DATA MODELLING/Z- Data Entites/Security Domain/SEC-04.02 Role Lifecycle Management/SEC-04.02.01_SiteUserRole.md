# Data Entity Specification: SEC-04.02.01 SiteUserRole

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Reviewer** |
| :--- | :--- | :--- | :--- |:--- |
| **SEC-04.02.01** | 1.0.0 | **DRAFT** | Architect | Product Owner |
<div><sub><strong>Table - 1 SEC-04.02.01 –</strong> Document control header</sub></div></br>

---

## 1. Description & Scope

The **SEC-04.02.01 SiteUserRole** entity records **role assignments and revocations** for users.

This entity supports:
- Assigning a role to a user (grant)
- Revoking a role from a user (revoke) while retaining history
- Evidencing who performed the assignment/revocation (actor)
- DQ checks for role validity, timeline correctness, and duplicates

---

## 2. Referential Integrity Standard

> All relationships are **logical only**.  
> No physical FOREIGN KEY constraints exist.

Logical references:
- `SiteUserGuid` → **SEC-02.01 SiteUser** (identity anchor)
- `RoleId` → **SEC-04.01.01 Role**
- `AssignedBySiteUserGuid` / `RevokedBySiteUserGuid` → **SEC-02.01 SiteUser** (actor; nullable for system actions)

---

## 3. ERD (Context) — One Level Only

```mermaid
erDiagram
    Auth_SEC_04_01_Role {
        int RoleId PK
        nvarchar RoleCode
        bit IsActive
    }

    Auth_SEC_04_02_SiteUserRole {
        uniqueidentifier SiteUserGuid
        int RoleId
        datetime2 AssignedAtUtc
        uniqueidentifier AssignedBySiteUserGuid
        datetime2 RevokedAtUtc
        uniqueidentifier RevokedBySiteUserGuid
    }

    Auth_SEC_04_01_Role ||--o{ Auth_SEC_04_02_SiteUserRole : "RoleId (logical)"
```
<div><sub><strong>Figure - 1 SEC-04.02.01 –</strong> One-level ERD context for SiteUserRole</sub></div></br>

---

## 4. Table Definition

**Table:** `[Auth].[SEC_04_02_SiteUserRole]`

| Column | Type | Null | Notes |
|--------|------|------|-------|
| `SiteUserGuid` | UNIQUEIDENTIFIER | NOT NULL | Subject identifier (User GUID). |
| `RoleId` | INT | NOT NULL | Logical reference to Role. |
| `AssignedAtUtc` | DATETIME2(3) | NOT NULL | UTC timestamp when role granted. |
| `AssignedBySiteUserGuid` | UNIQUEIDENTIFIER | NULL | Actor granting role; NULL allowed for system actions. |
| `RevokedAtUtc` | DATETIME2(3) | NULL | UTC timestamp when role revoked (if revoked). |
| `RevokedBySiteUserGuid` | UNIQUEIDENTIFIER | NULL | Actor revoking role; NULL allowed for system actions. |
<div><sub><strong>Table - 2 SEC-04.02.01 –</strong> Physical table definition for `[Auth].[SEC_04_02_SiteUserRole]`</sub></div></br>

---

## 5. Data Management

> This section lists **only** the stored procedures, views, and triggers created **directly** to manage this entity, including DQ controls and audit signalling.

| Object Type | Name | Description |
|-------------|------|-------------|
| Stored Procedure | **usp_SEC_04_02_AssignRoleToUser** | Assigns a role to a user (writes assignment). |
| Stored Procedure | **usp_SEC_04_02_RevokeRoleFromUser** | Revokes a role from a user (sets revoke fields). |
| Stored Procedure | **usp_SEC_04_02_GetUserRoles** | Returns effective roles for a user (active assignments only). |
| View | **vw_SEC_04_02_SiteUserRole_Effective** | Active assignments: `RevokedAtUtc IS NULL` and role active. |
| View | **vw_SEC_04_02_SiteUserRole_DQ** | Exposes DQ flags (duplicates, invalid timelines, inactive role usage). |
| Stored Procedure | **usp_SEC_04_02_SiteUserRole_DQ_Validate** | Executes DQ validation rules and returns pass/fail results with rule codes. |
| Stored Procedure | **usp_SEC_04_02_SiteUserRole_DQ_Report** | Standardised DQ exception report for governance and security review. |
| Trigger | **trg_SEC_04_02_SiteUserRole_AuditSignal** | Emits role grant/revoke events to unified audit spine on INSERT/UPDATE (design intention). |
<div><sub><strong>Table - 3 SEC-04.02.01 –</strong> SiteUserRole management objects (including DQ + audit signalling)</sub></div></br>

---

## 6. Data Management Diagram (Direct Objects Only)

```mermaid
flowchart TB
    SUR[[Auth.SEC_04_02_SiteUserRole]]

    P1[usp_SEC_04_02_AssignRoleToUser]
    P2[usp_SEC_04_02_RevokeRoleFromUser]
    P3[usp_SEC_04_02_GetUserRoles]

    V1[vw_SEC_04_02_SiteUserRole_Effective]
    V2[vw_SEC_04_02_SiteUserRole_DQ]

    DQ1[usp_SEC_04_02_SiteUserRole_DQ_Validate]
    DQ2[usp_SEC_04_02_SiteUserRole_DQ_Report]

    T1{{trg_SEC_04_02_SiteUserRole_AuditSignal}}

    P1 --> SUR
    P2 --> SUR
    P3 --> SUR

    SUR --> V1

    SUR --> DQ1
    DQ1 --> V2
    V2 --> DQ2

    SUR --> T1
```
<div><sub><strong>Figure - 2 SEC-04.02.01 –</strong> Direct operational, DQ, and audit-signal objects created for SiteUserRole</sub></div></br>

---

## 7. Data Quality Measures (DQ)

| Rule Code | Rule | Rationale |
|----------|------|-----------|
| DQ-SEC-04-SUR-01 | `AssignedAtUtc` must not be NULL | Assignment must be time-bound evidence. |
| DQ-SEC-04-SUR-02 | `RevokedAtUtc` (if present) must be >= `AssignedAtUtc` | Prevents invalid timelines. |
| DQ-SEC-04-SUR-03 | Duplicate active assignments (same user + role with `RevokedAtUtc IS NULL`) must not exist | Prevents ambiguous entitlement. |
| DQ-SEC-04-SUR-04 | Active assignments must reference an active role (`Role.IsActive = 1`) | Prevents retired roles being effective. |

---

## 8. Business Rules

- A user may have multiple roles.
- Revocation does not delete history: revocation is timestamp-based.
- `AssignedBySiteUserGuid` and `RevokedBySiteUserGuid` may be NULL only for explicit system actions.
- Effective roles are defined as assignments where:
  - `RevokedAtUtc IS NULL`, and
  - the referenced role is active.

---

## 9. Change History

| Version | Date | Author | Notes |
|---------|------|--------|-------|
| 1.0.0 | 2025-12-14 | Architect | Initial SiteUserRole entity spec aligned to SEC templates (ERD, management objects, DQ). |
<div><sub><strong>Table - 4 SEC-04.02.01 –</strong> Change history</sub></div></br>
