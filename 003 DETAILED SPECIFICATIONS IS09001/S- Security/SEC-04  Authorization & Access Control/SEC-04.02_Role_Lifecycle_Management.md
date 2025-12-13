# Specification: SEC-04.02 Role Lifecycle Management

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |
|-----------------|-------------|------------|---------------------|------------------|
| **SEC-04.02** | **1.0.0** | **DRAFT** | Business Architect | Product Officer |

---

## 1. Purpose

This specification defines the **logical lifecycle management of roles** within the SUSTINA platform.

It governs how roles are:
- Created
- Modified
- Reviewed
- Deprecated
- Retired

The objective is to ensure roles remain **purpose-driven, auditable, least-privilege**, and aligned with organisational and regulatory requirements.

This document is **technology-neutral** and excludes transport protocols, storage technologies, and implementation details.

---

## 2. Relationship to Other Security Specifications

| Specification | Relationship |
|---------------|--------------|
| **SEC-04.01** Roles & Permissions Model | Defines the structure and meaning of roles |
| **SEC-04.03** Role Assignment & Entitlements | Applies roles to subjects |
| **SEC-05.01** Joiner–Leaver–Mover Policy | Triggers lifecycle changes |
| **SEC-03.01** Token & Session Handling | Enforces revocation after lifecycle changes |
| **SEC-10.00** GDPR Data Handling Strategy | Governs retention and lawful processing |

<div><sub><strong>Table 1 –</strong> Relationship to other security specifications</sub></div>

---

## 3. Scope

This specification governs:
- Role creation and approval
- Role modification and versioning
- Role review and recertification
- Role deprecation and retirement

Out of scope:
- Assignment of roles to subjects (SEC-04.03)
- Access decision enforcement (SEC-04.04)

---

## 4. Role Lifecycle States

| State | Description |
|------|-------------|
| Draft | Role proposed but not active |
| Active | Role available for assignment |
| Deprecated | Role no longer assignable |
| Retired | Role fully withdrawn from use |

<div><sub><strong>Table 2 –</strong> Role lifecycle states</sub></div>

---

## 5. Role Creation

### 5.1 Creation Requirements

To create a role, the following information must be provided:

| Attribute | Required | Description |
|----------|----------|-------------|
| Role Name | Yes | Canonical, unique role name |
| Description | Yes | Clear business purpose |
| Permissions | Yes | Permissions granted by role |
| Owner | Yes | Accountable role owner |
| Risk Tier | Yes | Low / Medium / High |
| Justification | Yes | Reason for role creation |

<div><sub><strong>Table 3 –</strong> Required attributes for role creation</sub></div>

### 5.2 Approval

- Role creation may require approval based on risk tier
- Approval decisions must be auditable
- Rejected roles must not progress to Active state

---

## 6. Role Modification

Role changes must:
- Preserve role identity
- Increment role version
- Be reviewed and approved where risk changes

Typical modifications include:
- Permission changes
- Description updates
- Ownership changes

---

## 7. Role Review & Recertification

| Review Trigger | Description |
|---------------|-------------|
| Scheduled review | Periodic review based on risk tier |
| Material change | Significant permission change |
| Incident-driven | Triggered by security event |
| Audit finding | Triggered by compliance review |

<div><sub><strong>Table 4 –</strong> Role review triggers</sub></div>

- Reviews must confirm ongoing business need
- Outcomes must be recorded and auditable

---

## 8. Role Deprecation & Retirement

### 8.1 Deprecation

- Deprecated roles must not be newly assigned
- Existing assignments must be identified
- Replacement roles should be defined where applicable

### 8.2 Retirement

- Retired roles must have zero active assignments
- All entitlements must be removed
- Historical records must be retained for audit purposes

---

## 9. Governance & Audit

Role lifecycle management must ensure:
- Deterministic outcomes for lifecycle operations
- Full audit trail of changes and approvals
- Alignment with separation of duties policies
- Evidence suitable for ISO-9001 audits

---

## 10. Change History

| Version | Date | Author | Notes |
|--------|------|--------|-------|
| 1.0.0 | 2025-12-13 | Business Architect | Initial authoritative role lifecycle specification |

<div><sub><strong>Table 5 –</strong> Change history</sub></div>
