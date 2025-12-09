# Specification: SEC-99.00 Hashing Strategy (Argon2id & Sensitive Data Protection)

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |
|-----------------|-------------|------------|---------------------|------------------|
| SEC-99.00 | 1.0.0 | **DRAFT** | Business Architect | Product Officer |

---

# 1. Purpose
The purpose of this document is to define the security requirements, operational rules, and implementation standards for all hashing activities within the SUSTINA platform.

Hashing is a critical technical control used to protect:

- User passwords  
- Identity attributes (when appropriate)  
- GDPR-sensitive identifiers  
- Integrity-verification fields  
- Any data whose loss or breach would expose user privacy or violate regulatory obligations  

This specification defines **how**, **when**, and **why** hashing is applied and standardises all hashing functions under the **Core API**, ensuring consistent execution and traceable compliance.

---

# 2. Scope

This hashing strategy applies to:

- **Password creation and verification** (SEC-02.01, SEC-02.02)  
- **Password resets and updates**  
- **Identity attributes that are sensitive under GDPR** (e.g., NI numbers, unique identifiers, personal tracking keys)  
- **Integrity verification controls** (e.g., document digests, audit trails)  
- **Any field where irreversible transformation is required under data minimisation principles**  
- **Core API hashing operations**, centralising all hashing logic in one secure place  

It governs behaviour in:

- **Core API (`nrg_Core_API`)**  
- **Database Layer (SQL Server)**  
- **App API / BFF** — with respect to avoiding hashing outside Core  
- **Any system module requiring irreversible transformation of data**

NOT included:

- Encryption of tokens  
- API secrets  
- OAuth flows  
- TLS-level cryptographic functions  
- Reversible encryption requirements  

These are governed by other specifications.

---

# 3. Objectives

### 3.1 Ensure Irreversible Protection of Sensitive Data  
All hashed values must be **non-recoverable**, ensuring that no system component or attacker can derive original values.

### 3.2 Standardise Hashing Across All Components  
Hashing must be performed consistently in the **Core API**, not in the frontend, BFF, or SQL.

### 3.3 Strengthen GDPR Compliance  
Hashing supports GDPR’s *data minimisation* and *data protection by design* principles by ensuring that stored identifiers cannot reveal the underlying personal data.

### 3.4 Minimise Compromise Impact  
If the database is breached, hashed values must be computationally infeasible to reverse, preventing mass identity exposure.

### 3.5 Maintain ISO-9001 Process Integrity  
Hashing operations must be:

- Documented  
- Repeatable  
- Governed  
- Auditable  

---

# 4. Architectural Principles

## 4.1 Core-Exclusive Hashing  
Only the **Core API** is authorised to perform hashing operations for:

- Passwords  
- Sensitive identifiers  
- Integrity signatures  
- Any irreversible transformations  

React, App API, SQL Server, and other systems **must not perform hashing**.

## 4.2 Argon2id as the Standard Hashing Algorithm  
All high-sensitivity data (especially passwords) must be hashed using **Argon2id**, configured as follows:

| Parameter | Value | Notes |
|----------|--------|-------|
| Algorithm | Argon2id | OWASP preferred |
| Version | 1.3 | Latest stable |
| MemoryCost | 65536 KB (64MB) | High resistance to GPU attacks |
| TimeCost | 4 | OWASP baseline recommendation |
| Parallelism | 1 | Suitable for backend workloads |
| Salt | ≥ 16 bytes | Cryptographically generated |
| Output | Encoded Argon2id string | Includes salt + parameters + hash |

Argon2id is required for:

- Passwords  
- High-risk GDPR identifiers  
- Pseudonymisation processes  

## 4.3 SQL Stores Only Hash Outputs  
SQL Server stores only:

- Argon2id encoded hash strings  
- Internal digests used for integrity checks  

SQL must never receive:

- Plaintext sensitive values  
- Reversible encryption keys  
- Partial hash components  

Hashes must always be stored as complete, encoded Argon2id strings.

## 4.4 Verification Only Occurs Inside Core  
Verification logic is always handled by:

```
SEC_02_99_Hashing.Verify(rawInput, storedHash)
```

SQL does not compare hashed values.  
SQL retrieves records *by identity*, not by hashed secrets.

## 4.5 No Reversible Encryption for Sensitive User Data  
Sensitive fields (passwords, NI numbers, identity keys) must **never** be encrypted using AES or other reversible methods unless explicitly required by a separate policy.

Reversible encryption violates:

- GDPR (data minimisation)  
- OWASP ASVS  
- NIST credential-handling standards  

---

# 5. Operational Requirements

## 5.1 Hash Generation Workflow

1. Core receives the raw value (password or identifier) securely.  
2. Core calls:  
   ```
   SEC_02_99_Hashing.Hash(value)
   ```  
3. Argon2id generates:  
   - Unique salt  
   - Derived key  
   - Encoded hash string  
4. Core stores the encoded string in SQL.

## 5.2 Verification Workflow

1. Core retrieves stored hash from SQL.  
2. Core calls:  
   ```
   SEC_02_99_Hashing.Verify(rawValue, storedHash)
   ```  
3. Core approves or rejects the validation request.  

SQL is not part of verification logic.

## 5.3 Use of Hashing for Non‑Password GDPR Data  
Hashing may be used to irreversibly store:

- National insurance numbers  
- Government-issued identifiers  
- Internal tracking identifiers  
- Pseudonymised audit references  

This is permissible only when:

- The original value is not required by operations  
- GDPR mandates pseudonymisation  
- Data minimisation rules apply  

---

# 6. Implementation (Core API)

```csharp
public static class SEC_02_99_Hashing
{
    public static string Hash(string value)
    {
        var config = new Argon2Config
        {
            Type = Argon2Type.Id,
            Version = Argon2Version.Number13,
            TimeCost = 4,
            MemoryCost = 65536,
            Lanes = 1,
            Threads = 1,
            Salt = Argon2Generator.GenerateSalt(16),
            Password = value
        };

        using var argon2 = new Argon2(config);
        using var hash = argon2.Hash();
        return config.EncodeString(hash.Buffer);
    }

    public static bool Verify(string rawValue, string storedHash)
    {
        return Argon2.Verify(storedHash, rawValue);
    }
}
```

This reusable utility supports both password and general GDPR hashing.

---

# 7. Compliance and Audit

| Standard | Alignment |
|----------|-----------|
| **OWASP ASVS v4** | Hashing, sensitive data storage, credential protection |
| **NIST SP 800‑63B** | Memorised secret verifiers, secure hashing |
| **ISO-9001** | Controlled, auditable, documented process |
| **GDPR** | Data minimisation, pseudonymisation, privacy-by-design |

---

# 8. Governance

This hashing strategy must be reviewed annually or when:

- Threat modelling results change  
- Industry recommendations evolve  
- Hash parameters are increased for security  
- Penetration tests suggest strengthening  
- GDPR or ISO compliance requirements shift  

All changes must follow SUSTINA’s controlled change management process.

---
