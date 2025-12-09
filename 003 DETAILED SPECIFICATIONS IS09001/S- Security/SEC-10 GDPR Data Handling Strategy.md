# Specification: SEC-10.00 GDPR Data Handling Strategy

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |
|-----------------|-------------|------------|---------------------|------------------|
| SEC-10.00 | 1.0.0 | **DRAFT** | Business Architect | Product Officer |

---

# 1. Purpose
The purpose of this document is to define the **GDPR Data Handling Strategy** for the SUSTINA ecosystem.  
This strategy ensures that all personal data is processed, transmitted, stored, and deleted in accordance with:

- The General Data Protection Regulation (GDPR)  
- ISO-9001 Quality Management Standards  
- SUSTINA internal data governance rules  
- Privacy-by-design and security-by-design principles  

This document applies to all personal data processed across SUSTINA’s systems, including:

- Identity data  
- Authentication data  
- Contact data  
- User-generated content  
- System-level audit data  

---

# 2. Scope

This strategy applies to:

- React applications  
- App API / BFF (`SUSTINA.AppApi`)  
- Core API (`nrg_Core_API`)  
- SQL Server and all persistent data storage layers  
- Logging, monitoring, and auditing systems  
- Backup and archival processes  
- Third‑party integrations processing personal data  

Excluded from this document:

- Non-personal data security  
- Payment processing (covered under SEC-20.x)  
- Vendor-specific contractual compliance  

---

# 3. GDPR Principles Implemented in SUSTINA

SUSTINA implements all seven GDPR foundational principles:

### 3.1 Lawfulness, Fairness, Transparency
- Users are informed of data usage through clear privacy notices.  
- All processing activities are mapped to lawful bases (e.g., consent, legitimate interest).

### 3.2 Purpose Limitation
- Data is collected only for defined operational purposes.  
- Reuse or repurposing of user data requires explicit approval from the Data Protection Officer.

### 3.3 Data Minimisation
- Only strictly necessary data is collected.  
- Sensitive fields may be hashed or pseudonymised (see SEC-99.00 Hashing Strategy).

### 3.4 Accuracy
- Users may request corrections at any time.  
- Critical identifiers (email, username) are validated during registration.

### 3.5 Storage Limitation
- Expired accounts follow deletion or anonymisation rules.  
- Backup cycles are automatically purged after defined retention windows.

### 3.6 Integrity & Confidentiality
- Data is protected using encryption at rest, TLS in transit, and hashed sensitive elements.  
- Access to personal data follows RBAC and least-privilege principles.

### 3.7 Accountability
- All processing activities are logged.  
- Audit trails are maintained for ISO-9001 and GDPR compliance.

---

# 4. Personal Data Classification

SUSTINA categorises personal data into security tiers:

| Tier | Type of Data | Examples | Controls |
|------|--------------|----------|----------|
| **Tier 1: Identity-Critical** | Direct identifiers | Email, Username, User GUID | Hashing, encryption, strict retention |
| **Tier 2: Behavioural** | Usage patterns | Login timestamps, session history | Pseudonymisation, monitoring |
| **Tier 3: Operational** | Non-sensitive metadata | Preferences, UI settings | Standard controls |
| **Tier 4: Sensitive (GDPR)** | Government identifiers, NI numbers | None currently collected | Strict hashing, no plaintext storage |

---

# 5. Operational Requirements

## 5.1 Data Minimisation
All system components must collect **only required fields**.  
Any candidate for removal must undergo a minimisation review.

## 5.2 Secure Transmission
All personal data must be transmitted using **TLS 1.2+**.

## 5.3 Secure Storage
Sensitive values must use one of:

- Argon2id hashing (irreversible)  
- AES-256 encryption (when reversible access required)  
- GUID-based pseudonymisation  

Passwords must **always** be hashed (SEC-99.00).

## 5.4 Right to Access
Users can request all data stored about them.  
Core must support data extraction via internal administrative tools.

## 5.5 Right to Erasure ("Right to be Forgotten")
User deletion workflow must:

1. Remove or anonymise personal data  
2. Invalidate active sessions  
3. Delete password hashes  
4. Purge reset/verification tokens  
5. Remove analytics identifiers  

## 5.6 Data Portability
Data exports must use machine-readable formats (JSON, CSV).

---

# 6. Logging & Monitoring Requirements

- Logs must not contain passwords, tokens, or sensitive identifiers.  
- For GDPR compliance, logs must not contain full email addresses — use masked forms.  
- Authentication attempts are logged with pseudonymised user identifiers.  
- Audit logs must be immutable and access-controlled.

---

# 7. Third‑Party Integrations

All third‑party systems handling personal data must:

- Provide a GDPR-compliant DPA (Data Processing Agreement)  
- Support encryption in transit and at rest  
- Notify SUSTINA of breaches within 72 hours  
- Be subject to vendor risk assessment  

---

# 8. Data Breach Protocol

In the event of a breach:

1. Incident is escalated to the DPO and Security Officer.  
2. Containment actions are initiated immediately.  
3. A risk assessment determines exposure and impact.  
4. The ICO and affected users are notified within **72 hours** (if required by law).  
5. A full incident report is prepared under ISO-9001 corrective action procedure.

---

# 9. ISO-9001 Governance

To maintain certification:

- All GDPR processes must be documented and repeatable.  
- Data flows must have complete traceability.  
- This document must be reviewed annually or after material system changes.  
- Evidence of compliance must be produced during internal and external audits.

---

# 10. Change History

| Version | Date | Author | Notes |
|---------|------|--------|-------|
| 1.0.0 | 2025-12-09 | Business Architect | Initial GDPR strategy document for security accreditation |

---
