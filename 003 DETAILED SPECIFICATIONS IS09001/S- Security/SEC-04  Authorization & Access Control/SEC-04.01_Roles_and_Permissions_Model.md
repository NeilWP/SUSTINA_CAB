# Specification: SEC-04.01 Roles & Permissions Model

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |
|-----------------|-------------|------------|---------------------|------------------|
| **SEC-04.01** | **1.0.0** | **DRAFT** | Business Architect | Product Officer |

---

## 1. Purpose

This specification defines the **authoritative, logical model for roles and permissions** used to control access to resources within the SUSTINA platform.

It establishes:
- What a *Role* is and is not
- What a *Permission* (or capability) represents
- How roles relate to permissions
- The minimum information required to define and govern roles
- The constraints required to ensure auditability, separation of duties, and least privilege

This document is **technology-neutral** and deliberately excludes transport protocols, storage technologies, and interface definitions.

---

## 2. Relationship to Other Security Specifications


| Specification | Relationship |
|---------------|--------------|
| **SEC-02.04** Identity Identifiers & Canonicalisation | Provides subject identifiers referenced by role assignments |
| **SEC-03.01** Token & Session Handling | Consumes authorisation context derived from roles |
| **SEC-04.02** Role Lifecycle Management | Governs creation, change, and deprecation of roles |
| **SEC-04.03** Role Assignment & Entitlements | Governs assignment of roles to subjects |
| **SEC-05.01** Joiner–Leaver–Mover Policy | Drives role assignment changes |
| **SEC-10.00** GDPR Data Handling Strategy | Governs lawful processing and minimisation |
<div><sub><strong>Table 1 –</strong> Relationship to other security specifications</sub></div>

---

## 3. Scope

This specification governs:
- Logical definition of roles and permissions
- Role composition and inheritance (where permitted)
- Constraints on role design
- Authorisation context derivation

Out of scope:
- Role creation workflows (SEC-04.02)
- Role assignment workflows (SEC-04.03)
- Access decision enforcement mechanisms (SEC-04.04)
- Token encoding or claim formats (SEC-03.01)

---

## 4. Core Concepts

### 4.1 Subject
A **Subject** is a uniquely identified actor within the platform, referenced by a stable User GUID (SEC-02.04).

### 4.2 Permission
A **Permission** represents an abstract capability to perform an action on a class of resources.

Examples:
- View reports
- Submit data
- Approve changes
- Administer roles

Permissions are:
- Atomic
- Non-hierarchical
- Independently governable

### 4.3 Role
A **Role** is a named collection of permissions representing a job function or responsibility.

Roles:
- Aggregate permissions
- Are assigned to subjects
- May be time-bound or conditional via assignment metadata
- Must have a clear business purpose

---

## 5. Role Design Principles

### 5.1 Least Privilege
Roles must include **only the minimum permissions** required to fulfil their stated purpose.

### 5.2 Separation of Duties (SoD)
Roles must be designed to prevent conflicting permissions from being granted to a single subject.

### 5.3 Explicit Purpose
Each role must have a documented:
- Name
- Description
- Business owner
- Risk classification

### 5.4 Stability
Roles should be **stable over time**. Frequent changes indicate improper role granularity.

---

## 6. Role Composition & Inheritance


| Model | Permitted | Notes |
|------|-----------|-------|
| Flat roles (no inheritance) | Yes | Preferred default |
| Hierarchical roles | Conditional | Requires explicit governance |
| Permission-only assignment (no roles) | No | Violates auditability |
| Nested role inheritance | Restricted | Must avoid privilege amplification |
<div><sub><strong>Table 2 –</strong> Role composition models</sub></div>
</br>
Inheritance, where allowed, must be:
- Explicit
- Acyclic
- Auditable

---

## 7. Minimum Role Definition (Logical)


| Attribute | Required | Description |
|----------|----------|-------------|
| Role ID | Yes | Stable, system-generated identifier |
| Role Name | Yes | Canonical, human-readable name |
| Description | Yes | Clear statement of purpose |
| Permissions | Yes | Set of permissions granted |
| Owner | Yes | Accountable business owner |
| Risk Tier | Yes | Low / Medium / High |
| Status | Yes | Active / Deprecated |
| Created Date | Yes | Audit timestamp |
| Last Reviewed Date | Yes | Governance checkpoint |
<div><sub><strong>Table 3 –</strong> Required role attributes</sub></div>
</br>

---

## 8. Authorisation Context (Logical)

Roles assigned to a subject produce an **authorisation context** used by access decisions.

The authorisation context may include:
- Role identifiers
- Derived permission sets
- Risk indicators

The representation of this context (e.g. token claims) is governed by **SEC-03.01** and is out of scope here.

---

## 9. Governance

- All roles must be version-controlled
- Role definitions must be reviewed periodically based on risk tier
- Deprecated roles must not be assigned to new subjects
- Changes to role definitions require documented approval

---

## 10. ISO-9001 Alignment

The roles and permissions model must:
- Be documented and repeatable
- Support deterministic access decisions
- Provide auditable evidence of design intent and change

---

## 11. Change History

<div><sub><strong>Table 4 –</strong> Change history</sub></div>

| Version | Date | Author | Notes |
|--------|------|--------|-------|
| 1.0.0 | 2025-12-13 | Business Architect | Initial authoritative roles and permissions model |
