# Specification: SEC-06.01 Security Events & Evidence

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |
|-----------------|-------------|------------|---------------------|------------------|
| **SEC-06.01** | **1.0.0** | **DRAFT** | Business Architect | Product Officer |

---

## 1. Purpose

This specification defines the **logical requirements for security event generation, evidence capture, and audit support** across the SUSTINA platform.

It ensures that security-relevant actions:
- Are consistently recorded
- Produce evidentiary artefacts
- Support forensic analysis
- Meet ISO-9001 and regulatory expectations

This document is **technology-neutral**.

---

## 2. Relationship to Other Security Specifications

| Specification | Relationship |
|---------------|--------------|
| **SEC-02.xx** Identity & Authentication | Generates authentication events |
| **SEC-03.01** Token & Session Handling | Generates session events |
| **SEC-04.xx** Authorisation | Generates access decision events |
| **SEC-05.01** Joiner–Leaver–Mover Policy | Generates lifecycle events |
| **SEC-10.00** GDPR Data Handling Strategy | Governs data minimisation and retention |

<div><sub><strong>Table 1 –</strong> Relationship to other security specifications</sub></div>

---

## 3. Scope

This specification governs:
- Security-relevant events
- Evidence attributes
- Retention and access principles

Out of scope:
- Log storage technology
- SIEM tooling
- Alerting systems

---

## 4. Security Event Categories

| Category | Examples |
|--------|----------|
| Authentication | Sign-in success/failure |
| Session | Session creation, revocation |
| Authorisation | Access allow/deny |
| Entitlement | Role assignment/revocation |
| Lifecycle | Joiner/Mover/Leaver events |
| Administrative | Policy and role changes |

<div><sub><strong>Table 2 –</strong> Security event categories</sub></div>

---

## 5. Required Evidence Attributes

| Attribute | Description |
|----------|-------------|
| Event ID | Unique identifier |
| Timestamp | Event time |
| Subject ID | User GUID (where applicable) |
| Event Type | Categorisation |
| Outcome | Success / Failure |
| Source | Generating component |
| Correlation ID | Cross-event tracing |

<div><sub><strong>Table 3 –</strong> Required evidence attributes</sub></div>

---

## 6. Evidence Principles

- Evidence must be immutable
- Evidence must be minimised
- Evidence must be attributable
- Evidence must be retrievable for audit

---

## 7. Retention & Access

Retention periods must:
- Align with GDPR lawful basis
- Support audit requirements
- Be documented and approved

Access to evidence must be:
- Role-restricted
- Audited

---

## 8. Change History

| Version | Date | Author | Notes |
|--------|------|--------|-------|
| 1.0.0 | 2025-12-13 | Business Architect | Initial authoritative security events and evidence specification |

<div><sub><strong>Table 4 –</strong> Change history</sub></div>
