# Specification: SEC-00 Overarching Security Strategy

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |
|-----------------|-------------|------------|---------------------|------------------|
| SEC-00 | 1.0.0 | **DRAFT** | Business Architect | Product Officer |

---

# 1. Purpose
The purpose of this document is to define SUSTINA’s overarching security strategy for user identity management, password handling, credential protection, and authentication operations across all system layers. This strategy establishes the principles, standards, and mandatory controls that govern **registration (SEC-02.01)**, **sign-in and password recovery (SEC-02.02)**, and **password hashing and verification (SEC-02.99)**.

SUSTINA processes personal user information and must therefore maintain a security model that is **resilient against credential compromise**, **compliant with recognised industry standards**, and **suitable for ISO-9001 audit**.

---

# 2. Scope
This strategy applies to all components of the SUSTINA authentication ecosystem:

- **Client Layer** (React-based Web Application)  
- **Application API / BFF** (SUSTINA.AppApi)  
- **Core Service Layer** (nrg_Core_API)  
- **Database Layer** (SQL Server Identity and Password Tables)  
- **Operational activities** including registration, login, password reset, and credential storage.

This strategy governs functional areas formally specified under:

- **SEC-02.01 Registration**  
- **SEC-02.02 Sign-In & Password Recovery**  
- **SEC-99.00 Hashing**  

---

# 3. Security Objectives

### 3.1 Protect User Credentials  
Ensure that passwords and other authentication secrets are never stored, transmitted, or logged in plaintext, and that a data breach cannot expose user secrets.

### 3.2 Enforce Industry-Best Practices  
Adopt OWASP-recommended password hashing (Argon2id), secure transport (TLS 1.2+), and strict separation of duties between application layers.

### 3.3 Maintain Compliance  
Enable alignment with ISO-9001, OWASP ASVS, NIST SP 800-63B, and GDPR security expectations for identity protection.

### 3.4 Minimise Attack Surface  
Ensure no single layer of the system can independently compromise credentials or bypass authentication controls.

---

# 4. Architectural Principles

## 4.1 End-to-End Secure Transport  
All authentication-related communication (React → BFF → Core) is transported exclusively over **HTTPS using TLS 1.2 or higher**.  
Passwords transmitted from the client are protected by TLS and are never stored or logged at any intermediate layer.

## 4.2 Plaintext Passwords Are Never Persisted  
No system layer may persist, echo, or log plaintext passwords.  
The only acceptable transformations are:
- Argon2id hashing (SEC-02.99)  
- In-memory verification using Argon2id  

No reversible encryption is permitted for authentication credentials.

## 4.3 Password Hashing Using Argon2id Only  
The Core API is the **only** component authorised to perform password hashing.

All password hashing must comply with the following Argon2id configuration:

- Argon2: **Argon2id**, Version 1.3  
- Memory Cost: **64 MB (65536 KiB)**  
- Time Cost: **4 iterations**  
- Parallelism: **1 lane/thread**  
- Salt: cryptographically secure, minimum 16 bytes  
- Output: Argon2id full encoded string (salt + parameters + hash)

The encoded string is stored verbatim in the database.

## 4.4 SQL Database Stores Hashes, Not Passwords  
The database stores only the **Argon2id encoded hash string**, with no possibility of reverse-engineering or extracting the plaintext password.

SQL Server does **not** validate passwords.  
It returns the stored hash to Core for Argon2id verification.

## 4.5 Single Point of Credential Verification  
Only **Core (nrg_Core_API)** is authorised to:

- Validate password correctness  
- Perform Argon2id verification  
- Approve authentication requests  

SQL is restricted to identity retrieval (via email) and must never attempt password comparison.

## 4.6 Separation of Concerns Across Layers

### Client (React)
- Collects credentials and transmits securely via HTTPS.
- Performs no hashing, transformations, or local storage beyond session.

### BFF / App API
- A pass-through orchestration layer for authentication.
- Performs validation of structure but **never** handles password logic.

### Core API
- Authoritative security layer.
- Performs hashing, verification, and identity operations.

### SQL Layer
- Stores Argon2id hashes.
- Maintains user records, email verification tokens, and reset tokens.

---

# 5. Mandatory Controls  

### 5.1 Password Strength Controls  
- Minimum length: **12 characters**  
- Recommended: passphrase approach  
- Enforced during SEC-02.01 registration  
- Checked during SEC-02.02 reset

### 5.2 Account Status Controls  
Authentication must fail when:
- Account not verified  
- Account locked  
- Account deactivated  
- Password reset token expired  
- Verification token expired  

Returned messages must not leak internal state.

### 5.3 No Credential Leakage  
- No logs may include passwords or hashes  
- No monitoring tool or tracing system may capture request bodies containing credentials  
- No debug builds may bypass Argon2id hashing  

### 5.4 Privacy and GDPR  
- Verification tokens and reset tokens must be random GUIDs  
- Tokens must expire within defined intervals  
- User data changes must be audited  
- Credential deletion must follow retention policy  

---

# 6. Compliance With Standards  

| Standard | Alignment |
|---------|-----------|
| **OWASP ASVS (v4)** | Advanced password storage, layered security, safe authentication handling |
| **NIST SP 800-63B** | Strong password hashing, secure credential lifecycle |
| **ISO-9001** | Process governance, controlled authentication flows |
| **GDPR** | Data minimisation, privacy by design, strong credential protection |
