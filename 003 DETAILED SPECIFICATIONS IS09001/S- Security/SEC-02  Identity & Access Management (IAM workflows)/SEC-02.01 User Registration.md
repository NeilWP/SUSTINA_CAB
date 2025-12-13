# Specification: SEC-02.01 User Registration

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |
|-----------------|-------------|------------|---------------------|------------------|
| **SEC-02.01** | **1.1.0** | **DRAFT** | Business Architect | Product Officer |

---

## 1. Purpose

SEC-02.01 defines the **user registration workflow** and mandatory controls for creating a new SUSTINA user identity.

Registration establishes:
- A new **subject** (User GUID) and associated identity record
- One or more **identity identifiers** (e.g. email, username) governed by SEC-02.04
- A **credential verifier** (password hash) governed by SEC-01.01
- A verification/onboarding path (e.g. email verification token) governed by SEC-02.01 and referenced by SEC-03.xx where sessions apply

This specification is **workflow-focused** and implementation-agnostic.

---

## 2. Relationship to Other Security Specifications

| Spec | Role |
|------|------|
| **SEC-00** | Security domain master and overarching strategy |
| **SEC-01.01** | Hashing & irreversible transformation policy (password hashing) |
| **SEC-01.02** | Encryption & key management policy (identifier protection at rest) |
| **SEC-02.03** | Account protection controls (abuse/rate limiting, enumeration safety) |
| **SEC-02.04** | Identity identifiers & canonicalisation (email/username rules) |
| **SEC-03.01** | Token & session handling (post-authentication) |
| **SEC-10.00** | GDPR handling strategy (lawful basis, minimisation, retention) |
<div><sub><strong>Tabel 1-</strong>Relationship to Other Security Specifications</sub></div>

---

## 3. Scope

This specification governs:
- Creation of new user identities
- Canonicalisation and uniqueness checks of identity identifiers
- Secure handling of plaintext passwords in transit and memory
- Hashing of credentials before storage
- Generation of verification tokens and onboarding flow
- Orchestration across React → BFF → Core → SQL

Out of scope:
- Session/token issuance (SEC-03.xx)
- Encryption algorithm details (SEC-01.02)
- Password recovery (SEC-02.02)

---

## 4. Registration Flow Overview

```mermaid
flowchart LR
    U[User] --> R[React Registration Form]
    R --> BFF[BFF App API]

    BFF --> CORE[Core API: Registration Orchestration]

    CORE --> ID_CAN[Canonicalise Identifiers<br/>SEC-02.04]
    ID_CAN --> ID_UNIQ{Unique Identifiers?}

    ID_UNIQ -->|No| FAIL[Return Registration Failure<br/>Enumeration-safe]
    ID_UNIQ -->|Yes| PWD_HASH[Hash Password Verifier<br/>SEC-01.01]

    PWD_HASH --> STORE[Persist Identity + Verifier]
    STORE --> TOKEN[Generate Verification Token]
    TOKEN --> EMAIL[Send Verification Email]

    EMAIL --> CONFIRM[Return Registration Confirmation]
    FAIL --> CONFIRM
```
<div><sub><strong>Fig 1-</strong>SEC-02.01 Registration flow</sub></div>  
</br>  

**Note:** All error responses must be **enumeration-safe** and protected under SEC-02.03.

---

## 5. Mandatory Controls

### 5.1 Identifier Handling (SEC-02.04)
- Email and username must be canonicalised in Core before any uniqueness check.
- Uniqueness checks must operate on canonical form.
- Identifier storage must be protected at rest per SEC-01.02.

### 5.2 Password Handling (SEC-01.01)
- Passwords are collected as plaintext in React and transmitted only via TLS.
- No layer may log, persist, or echo the plaintext password.
- Core must derive and store only an irreversible password hash per SEC-01.01.

### 5.3 Account Protection (SEC-02.03)
- Registration endpoints must be rate-limited and protected against automation.
- Responses must not disclose whether an email/username already exists.

### 5.4 Verification Tokens
- Verification tokens must be cryptographically random and time-bound.
- Token issuance and consumption must be auditable.
- Tokens must be single-use.

---

## 6. Validation Requirements

### 6.1 Username Validation (if enabled)
- Meets allowed character set and length rules (SEC-02.04)
- Unique on canonical form
- Not in reserved word list

### 6.2 Email Validation
- RFC-conformant format
- Canonicalised and unique (SEC-02.04)
- Must pass operational email validation rules (policy-controlled)

### 6.3 Password Policy
- Minimum length: 12 characters
- Avoid known weak-password patterns where feasible
- Must be hashed under SEC-01.01 before storage

---

## 7. Registration Service Contract (Logical)

The platform shall provide a **Registration operation** that accepts a registration request and returns a deterministic outcome.

```mermaid
flowchart LR
    A["Registration Request Submitted"] --> B["Core: Receive Registration Operation"]

    B --> C["Canonicalise Identity Identifiers
(SEC-02.04)"]
    C --> D["Apply Account Protection Controls
(SEC-02.03)"]

    D --> E{"Request Valid?"}
    E -->|No| F["Return Deterministic Failure Outcome
Enumeration-safe"]
    E -->|Yes| G["Derive Password Verifier
(SEC-01.01)"]

    G --> H["Persist Identity Identifiers
Protected at Rest (SEC-01.02)"]
    H --> I["Persist Password Verifier"]

    I --> J["Generate Verification Token + Expiry"]
    J --> K["Trigger Verification Notification"]

    K --> L["Return Deterministic Success Outcome
No Secrets Returned"]
    F --> M["End"]
    L --> M

```
<div><sub><strong>Fig 2-</strong>SEC-02.01.07: Registration operation flow (logical)</sub></div>

### 7.1 Required Information

A registration request must supply the following information elements:

| Element | Required | Description |
|--------|----------|-------------|
| Email Address | Yes | Primary identity identifier; subject to canonicalisation and uniqueness rules (SEC-02.04) |
| Password | Yes | Plaintext credential supplied transiently for verifier derivation (SEC-01.01); must never be persisted or logged |
| Username | Conditional | Optional human-friendly identifier, if enabled by policy (SEC-02.04) |
<div><sub><strong>Tabel 2-</strong>Required Information</sub></div>  
</br>

No assumptions are made about:
- transport protocol
- message encoding
- field naming conventions
- client or server technology

### 7.2 Processing Semantics (Normative)

Upon receipt of a registration request, the Registration operation must:

1. Canonicalise all supplied identity identifiers (SEC-02.04)
2. Apply account protection controls (SEC-02.03)
3. Validate completeness and policy compliance of supplied information
4. Derive a password verifier using irreversible hashing (SEC-01.01)
5. Persist identity identifiers protected at rest (SEC-01.02)
6. Persist the derived password verifier
7. Generate a verification token and associated expiry metadata
8. Return a deterministic, enumeration-safe outcome

### 7.3 Outcome

The operation must produce a result that includes:

- A deterministic status indicator suitable for audit and workflow control
- A stable subject reference (User GUID) where registration succeeds
- No sensitive material (no passwords, no verifiers, no raw tokens)

Representation, encoding, and transport bindings are **explicitly out of scope** for this specification.


---

## 8. Data Storage Requirements (Logical)

The registration persistence operation must ensure that:

- A **password verifier** derived under SEC-01.01 is persisted as the sole credential representation  
  (plaintext passwords must never be stored).

- **Identity identifiers** are persisted in a form protected at rest under SEC-01.02.

- **Canonical identity identifiers** (as defined in SEC-02.04) are enforced as **unique within the identity domain**,  
  independent of the underlying storage technology.

- **Verification tokens** and their associated expiry metadata are persisted in a manner that:
  - supports single-use semantics
  - enforces expiry deterministically
  - prevents token reuse after consumption

- The persistence outcome produces a **deterministic, auditable result code** suitable for:
  - ISO-9001 evidence
  - operational tracing
  - downstream workflow control

These requirements define **logical guarantees**.  
The physical storage technology, indexing strategy, and enforcement mechanism are **explicitly out of scope** for this specification.


---

## 9. GDPR & Privacy Requirements

- Collect only minimum required identity attributes
- Do not log plaintext passwords or raw tokens
- Mask identifiers in logs (SEC-02.04)
- Token retention and expiry must be enforced

---

## 10. ISO-9001 Governance

Registration must be:
- Documented and repeatable
- Version-controlled
- Auditable (inputs, outcomes, token issuance)
- Updated under controlled change management

---

## 11. Change History

| Version | Date | Author | Notes |
|---------|------|--------|-------|
| 1.0.0 | 2025-12-09 | Business Architect | Initial draft |
| **1.1.0** | **2025-12-13** | Business Architect | Updated to reference SEC-01.01/SEC-01.02 primitives and SEC-02.03/SEC-02.04 IAM controls; removed implementation details and legacy SEC-99 references |
