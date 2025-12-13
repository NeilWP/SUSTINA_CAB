# Specification: SEC-05.01 Joiner–Leaver–Mover Policy

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |
|-----------------|-------------|------------|---------------------|------------------|
| **SEC-05.01** | **1.0.0** | **DRAFT** | Business Architect | Product Officer |

---

## 1. Purpose

This specification defines the **authoritative Joiner–Leaver–Mover (JLM) policy** for managing the lifecycle of identities within the SUSTINA platform.

It ensures that:
- Access is provisioned promptly and correctly when a subject joins
- Access is modified deterministically when a subject’s role or context changes
- Access is fully revoked when a subject leaves
- Sessions and tokens are invalidated where required
- All actions are auditable and compliant with ISO-9001 and GDPR

This document is **technology-neutral** and excludes implementation details.

---

## 2. Relationship to Other Security Specifications

| Specification | Relationship |
|---------------|--------------|
| **SEC-02.xx** Identity & Authentication | Establishes identity states |
| **SEC-03.01** Token & Session Handling | Governs session revocation |
| **SEC-04.01–04** Authorisation & RBAC | Governs role and entitlement changes |
| **SEC-10.00** GDPR Data Handling Strategy | Governs retention and lawful basis |

<div><sub><strong>Table 1 –</strong> Relationship to other security specifications</sub></div>

---

## 3. Scope

This policy governs:
- Identity onboarding (Joiner)
- Identity changes (Mover)
- Identity offboarding (Leaver)
- Associated entitlement, session, and audit actions

Out of scope:
- Authentication mechanics
- UI or workflow tooling

---

## 4. Joiner Policy

| Requirement | Description |
|------------|-------------|
| Identity created | Subject identity established |
| Baseline roles assigned | Minimum required access granted |
| Verification required | Identity verification enforced |
| Sessions limited | Initial access subject to policy |

<div><sub><strong>Table 2 –</strong> Joiner requirements</sub></div>

---

## 5. Mover Policy

| Requirement | Description |
|------------|-------------|
| Entitlements recalculated | Role set updated |
| SoD re-evaluated | Conflicts prevented |
| Excess access removed | Least privilege enforced |
| Sessions reviewed | Renewal or revocation applied |

<div><sub><strong>Table 3 –</strong> Mover requirements</sub></div>

---

## 6. Leaver Policy

| Requirement | Description |
|------------|-------------|
| All roles removed | Entitlements revoked |
| Sessions terminated | Tokens invalidated |
| Identity locked | Prevent reactivation |
| Data retention applied | GDPR-aligned handling |

<div><sub><strong>Table 4 –</strong> Leaver requirements</sub></div>

---

## 7. Timing Guarantees

| Event | Required Timing |
|------|-----------------|
| Joiner provisioning | Before first access |
| Mover changes | Immediately upon approval |
| Leaver revocation | Immediate or same business day |

<div><sub><strong>Table 5 –</strong> Timing guarantees</sub></div>

---

## 8. Evidence & Audit

All JLM events must produce:
- Deterministic outcome indicators
- Timestamps
- Responsible authority references
- Links to affected entitlements and sessions

---

## 9. Change History

| Version | Date | Author | Notes |
|--------|------|--------|-------|
| 1.0.0 | 2025-12-13 | Business Architect | Initial authoritative JLM policy |

<div><sub><strong>Table 6 –</strong> Change history</sub></div>
