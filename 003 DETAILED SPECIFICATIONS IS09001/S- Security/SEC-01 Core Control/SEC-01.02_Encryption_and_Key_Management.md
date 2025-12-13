# Specification: SEC-01.02 Encryption & Key Management

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |
|-----------------|-------------|------------|---------------------|------------------|
| **SEC-01.02** | 1.0.0 | **DRAFT** | Business Architect | Product Officer |

---

## 1. Purpose

SEC-01.02 defines the **authoritative encryption and key management standards** used within SUSTINA to protect data that must remain **readable for operational or IAM purposes**.

This specification governs:
- Reversible encryption
- Approved algorithms
- Key separation and rotation
- Layer‑level authority
- GDPR‑aligned access controls

---

## 2. Scope

### 2.1 In Scope
- Encryption of identity identifiers (email, username, phone)
- Encryption of GDPR‑regulated personal data
- Encryption of secrets requiring later recovery
- Key lifecycle and rotation policy

### 2.2 Out of Scope
- Hashing and irreversible transformations (SEC‑01.01)
- Token signing (SEC‑03.xx)
- TLS transport security
- External key vault vendor specifics

---

## 3. Normative Principles

### 3.1 Encryption Is Reversible by Design
Encryption protects confidentiality but **does not minimise data**.
Access control and lawful purpose govern usage.

### 3.2 Lawful Purpose Required
Encrypted data must have a documented GDPR lawful basis
(e.g. contract, legitimate interest).

### 3.3 Core‑Exclusive Decryption
Only the **Core API** may decrypt protected values.

### 3.4 Key Separation
Encryption keys must never be stored with encrypted data.

---

## 4. Layer Authority Matrix

| Layer | Encrypt | Decrypt | Store Encrypted |
|------|---------|---------|-----------------|
| React Client | ❌ | ❌ | ❌ |
| App API / BFF | ❌ | ❌ | ❌ |
| Core API | ✅ | ✅ | ❌ |
| SQL Server | ❌ | ❌ | ✅ |

---

## 5. Approved Encryption Standards

### 5.1 Algorithms
- **AES‑256‑GCM** for data at rest
- Authenticated encryption required

### 5.2 Envelope Encryption
Where feasible:
- Data encrypted with per‑record data keys
- Data keys encrypted with master keys

---

## 6. Key Management Requirements

- Keys stored outside databases
- Regular rotation schedule
- Access restricted to Core API runtime identity
- Compromise requires revocation and re‑encryption

---

## 7. Logging and Exposure Controls

- Encrypted values must not be logged
- Masked representations only in diagnostics
- Access events must be auditable

---

## 8. GDPR Alignment

- Encryption supports confidentiality, not anonymisation
- Erasure must include encrypted values and keys
- Data subject access must be mediated by authorised tooling

---

## 9. Governance

- Annual review or post‑incident review required
- Algorithm changes require versioned update
- Key policy changes require security approval

---

## 10. Change History

| Version | Date | Notes |
|--------|------|------|
| 1.0.0 | 2025‑12‑13 | Initial authoritative version |
