# Specification: SEC-01.01 Hashing & Irreversible Transformation

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |
|-----------------|-------------|------------|---------------------|------------------|
| **SEC-01.01** | 1.0.0 | **DRAFT** | Business Architect | Product Officer |

---

## 1. Purpose

SEC-01.01 defines the **authoritative, irreversible hashing standards** used across the SUSTINA platform to protect security‑critical secrets and to support GDPR‑aligned data minimisation.

This specification establishes:
- Where hashing is permitted
- Which algorithms are approved
- Minimum security parameters
- Which system layers may hash or verify
- Governance and audit expectations

This document is **implementation‑agnostic**. It defines *policy and constraints*, not code.

---

## 2. Scope

### 2.1 In Scope
- Password hashing and verification
- Irreversible pseudonymisation of sensitive identifiers
- One‑way integrity digests for audit evidence

### 2.2 Out of Scope
- Encryption and key management (see **SEC‑01.02**)
- Token signing and session handling (SEC‑03.xx)
- TLS and transport security
- OAuth / federation protocols

---

## 3. Normative Principles

### 3.1 Irreversibility Is Mandatory
All hashing covered by this specification must be **computationally irreversible**.
No system component may derive the original value from stored outputs.

### 3.2 Hashing Is a Security Primitive
Hashing is a **cross‑cutting security control**, not an IAM workflow.
It may be used by authentication, privacy, audit, or analytics functions.

### 3.3 Core‑Exclusive Authority
Only the **Core API** may:
- Generate hashes
- Verify hashes
- Apply irreversible transformations

All other layers are explicitly prohibited.

### 3.4 Hash Strength Never Decreases
Parameter changes may only **increase** resistance to attack.
Reductions are prohibited.

---

## 4. Layer Authority Matrix

| Layer | Hash | Verify | Store Hash |
|------|------|--------|------------|
| React Client | ❌ | ❌ | ❌ |
| App API / BFF | ❌ | ❌ | ❌ |
| Core API | ✅ | ✅ | ❌ |
| SQL Server | ❌ | ❌ | ✅ |

---

## 5. Approved Hashing Use‑Cases

### 5.1 Password Hashing
Purpose: protect authentication secrets.

Requirements:
- Algorithm: **Argon2id only**
- Unique cryptographic salt per password
- Full encoded hash string stored verbatim
- Verification performed only in Core API

### 5.2 Irreversible Pseudonymisation
Purpose: GDPR‑aligned minimisation where re‑identification is not required.

Requirements:
- Argon2id with random salt
- Non‑deterministic outputs
- No matching or lookup capability

### 5.3 Integrity Digests
Purpose: detect change and provide audit evidence.

Requirements:
- SHA‑256
- Not used for authentication
- Not treated as a security boundary

---

## 6. Approved Algorithms & Minimum Parameters

### 6.1 Argon2id Baseline

| Parameter | Minimum |
|---------|---------|
| Version | 1.3 |
| Memory Cost | 64 MB |
| Time Cost | 4 |
| Parallelism | 1 |
| Salt | ≥ 16 bytes |
| Output | Encoded string |

Implementations may exceed but not weaken this baseline.

---

## 7. Storage Requirements

- Only encoded hash outputs may be stored
- No plaintext or partial values permitted
- Hashes must never appear in logs or telemetry

---

## 8. Compliance Alignment

- OWASP ASVS v4
- NIST SP 800‑63B
- ISO‑9001
- GDPR (data minimisation, privacy‑by‑design)

---

## 9. Governance

- Reviewed annually or after security events
- Changes require version increment and change record
- Parameter increases must be documented

---

## 10. Change History

| Version | Date | Notes |
|--------|------|------|
| 1.0.0 | 2025‑12‑13 | Initial authoritative version |
