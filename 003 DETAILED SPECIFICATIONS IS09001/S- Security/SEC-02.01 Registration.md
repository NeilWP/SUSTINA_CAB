# Specification: SEC-02.01 User Registration

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |
|-----------------|-------------|------------|---------------------|------------------|
| SEC-02.01 | 1.0.0 | **DRAFT** | Business Architect | Product Officer |

---

# 1. Purpose
The purpose of this document is to define the registration workflow and security controls used when creating a new user account within the SUSTINA ecosystem. Registration is the first step in establishing a user identity, and therefore must follow strict authentication, validation, hashing, and GDPR-compliant data handling standards.

This specification aligns with:
- **SEC-99.00 Hashing Strategy**
- **SEC-02.02 Sign‑In & Password Recovery**
- **ISO-9001 quality controls**
- **OWASP ASVS authentication requirements**

---

# 2. Scope
This registration process governs:

- Creation of new user accounts  
- Validation of username and email uniqueness  
- Secure handling of passwords and sensitive user attributes  
- Application of Argon2id hashing during credential creation  
- Creation of verification tokens and onboarding flows  
- Orchestration between React → BFF → Core → SQL layers  

This specification applies to:

- React UI components
- App API / BFF (`SUSTINA.AppApi`)
- Core API (`nrg_Core_API`)
- SQL Server authentication tables

---

# 3. Registration Flow Overview

## 3.1 High-Level Sequence

1. **React UI** collects:
   - Username
   - Email
   - Password (plaintext)
2. **React → BFF** transmits the request securely via HTTPS.
3. **BFF**:
   - Validates request shape (not password)
   - Forwards the payload to Core
4. **Core API**:
   - Validates email format and MX records
   - Validates username constraints
   - Calls `SEC_02_99_Hashing.Hash(password)` to generate Argon2id hash
   - Creates user record and password entry in SQL
   - Generates a verification token (`GUID`)
5. **SQL Server**:
   - Stores the Argon2id hash in `[Auth].[SiteUserPassword]`
   - Stores identity profile in `[Auth].[SiteUser]`
6. **Core API** triggers the welcome email with a verification link.
7. **React** displays confirmation messaging to user.

---

# 4. Architectural Principles

## 4.1 Registration as a Multi-Layer Controlled Process
Each layer has strict responsibilities:

| Layer | Responsibility |
|-------|----------------|
| React | Collects data, ensures UX validation |
| BFF | Performs structural validation, forwards to Core |
| Core API | Performs business security logic + hashing + verification token generation |
| SQL | Stores hashed credentials + identity data |

---

## 4.2 Password Handling Must Follow SEC-99.00
- React never hashes or encrypts.
- BFF never hashes or encrypts.
- Core must hash using Argon2id.
- SQL stores **only** the Argon2id encoded hash string.

---

## 4.3 Reserved and Test Accounts
The system may define reserved account rules, such as:

- Preventing registration of protected usernames
- Blocking re-registration of demo or seed accounts
- Denying verification bypass unless explicitly configured

These rules support operational reliability and product safety.

---

# 5. Validation Requirements

## 5.1 Username Validation
- Must be unique
- Must meet minimum/maximum length requirements
- Must not contain restricted characters
- Must not conflict with reserved words or system accounts

## 5.2 Email Validation
- Must be unique
- Must be RFC-compliant
- Must pass MX record validation
- Must not match any reserved/demo email

## 5.3 Password Validation
- Minimum 12 characters
- Should avoid common/weak passwords
- Should encourage passphrase behaviour
- Must be hashed using Argon2id before storage

---

# 6. Registration API Definition (Core)

The Core API exposes a formal endpoint:

```
POST /api/core/auth/register   (SEC_02_01_RegisterSiteUser)
```

### Expected Payload
```json
{
  "userName": "ExampleUser",
  "email": "example@example.com",
  "password": "plainTextPassword"
}
```

### Process in Core
1. Validate inputs  
2. Call hashing:

```csharp
var hash = SEC_02_99_Hashing.Hash(request.Password);
```

3. Persist:

- user profile (`SiteUser`)
- Argon2id hash (`SiteUserPassword`)

4. Generate verification token  
5. Send verification email  
6. Return Core registration response DTO  

---

# 7. SQL Stored Procedure Requirements

The registration stored procedure must:

- Accept: `@UserName`, `@Email`, `@PasswordHash`
- Store the **Argon2id hash string** exactly as provided
- Create a unique `SiteUserGuid`
- Create a verification token with 24-hour TTL
- Prevent duplicate username/email registration
- Return:
  - StatusCode  
  - StatusMessage  
  - ResultSiteUserId  
  - ResultSiteUserGuid  
  - VerificationToken  

This aligns with the existing operation of `[Auth].[usp_RegisterSiteUser]`.

---

# 8. GDPR & Privacy Requirements

Registration must comply with GDPR principles:

- Only required user information is collected  
- Sensitive data (password, identifiers) must never appear in logs  
- Verification tokens must use random GUIDs  
- Passwords are never stored, even temporarily  
- Database stores irreversible hash strings only  

---

# 9. ISO‑9001 Governance and Control

Registration processes must:

- Be documented  
- Be repeatable  
- Follow controlled change paths  
- Produce predictable outputs  
- Maintain compliance with internal audit requirements  

Changes to:
- Password policies  
- Hashing algorithms  
- Registration workflow  

…must be reviewed through SUSTINA change control procedures.

---

# 10. Appendices

## 10.1 Example Successful Registration Response

```json
{
  "statusCode": 0,
  "message": "User registered successfully.",
  "siteUserId": 3501,
  "siteUserGuid": "9c3e18c0-6d0e-4fc8-8c18-4f602a8d2bfd",
  "verificationToken": "f851f8f7-c70a-42f5-b6dd-79b469103dd1"
}
```

## 10.2 Example Core Logging Policy (non-sensitive)
- Log: email (hashed or masked)
- Log: success/failure status
- Never log: passwords, raw tokens, hash outputs

---

# 11. Change History

| Version | Date | Author | Notes |
|---------|------|--------|-------|
| 1.0.0 | 2025‑12‑09 | Business Architect | Initial draft for ISO‑9001 submission |

---
