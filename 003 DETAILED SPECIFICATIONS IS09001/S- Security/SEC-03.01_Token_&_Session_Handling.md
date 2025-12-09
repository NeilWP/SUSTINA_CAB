# Specification: SEC-03.01 Token & Session Handling

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |
|-----------------|-------------|------------|---------------------|------------------|
| SEC-03.01 | 1.0.0 | **DRAFT** | Business Architect | Product Officer |

---

# 1. Purpose
This document defines the standards, controls, and operational expectations for secure token issuance, session management, and session renewal across the SUSTINA platform.

Token and session handling is a core security component that ensures:
- Only authenticated users gain access to protected resources  
- Sessions are valid, traceable, and properly expired  
- Tokens cannot be forged, replayed, or used beyond their authorised lifespan  
- The architecture meets ISO-9001 auditability requirements and GDPR data protection standards  

This specification aligns with:
- **SEC-02.01 Registration**
- **SEC-02.02 Sign-In & Password Recovery**
- **SEC-99.00 Hashing Strategy**
- **OWASP ASVS Session Management requirements**

---

# 2. Scope
This specification governs:

### Session & Token Processes
- Token issuance at sign-in  
- Token structure and cryptographic requirements  
- Token renewal and refresh workflows  
- Session expiry and invalidation  
- Revocation flows (manual and automated)

### Covered Components
- **React Client Application**
- **App API / BFF (SUSTINA.AppApi)**
- **Core API (nrg_Core_API)**
- **SQL Server**, only for storing session metadata (if used)

### Out of Scope
- Password hashing (see SEC-99.00)
- Registration (see SEC-02.01)
- Password recovery (see SEC-02.02)

---

# 3. Token Types Supported

## 3.1 Access Tokens
Access Tokens:
- Are short-lived
- Are passed with each API request
- Contain claims identifying the user and their authorisation
- Must be cryptographically signed (JWT or equivalent)

They must **not** include:
- Passwords  
- Personal data beyond necessary claims  
- Reset tokens  
- Sensitive identifiers  

## 3.2 Refresh Tokens
Refresh Tokens:
- Are long-lived
- Are used to obtain new Access Tokens
- Must **never** be stored in local storage  
- Must be stored in secure HTTP-only cookies or secure session storage  
- Must be tied to device/session identifiers  

A refresh token allows re-authentication **without requiring the password again**.

---

# 4. Token Security Requirements

### 4.1 Cryptographic Signing
Tokens must be signed using:
- **RS256 (asymmetric RSA)** or  
- **HS512 (high-strength HMAC)**

Private signing keys:
- Must reside only in Core  
- Must never be exposed to BFF or frontend  
- Must rotate on a scheduled basis

### 4.2 Expiry Requirements
| Token Type | Maximum Lifetime | Notes |
|------------|------------------|-------|
| Access Token | 5–15 minutes | Minimises impact of token theft |
| Refresh Token | 7–30 days | Revoked on logout or inactivity |
| Verification Token | 24 hours | One-time use |
| Reset Token | 24 hours | One-time use |

### 4.3 Mandatory Token Claims
Every Access Token MUST include:
- `sub` (subject / user GUID)  
- `iat` (issued at)  
- `exp` (expiry)  
- `sid` (session identifier)  
- `roles` or `scopes` where applicable  

### 4.4 Token Confidentiality
- Access tokens may be included in `Authorization: Bearer` headers.
- **Refresh tokens must never be sent in JavaScript-accessible storage.**

---

# 5. Session Handling Model

## 5.1 Session Establishment
After successful Argon2id verification (SEC-02.02):

1. Core generates a new SessionId (GUID)  
2. Core issues:
   - Access Token (short-lived)
   - Refresh Token (long-lived)
3. React stores Access Token in memory  
4. Refresh Token is stored in an HTTP-only cookie  

## 5.2 Session Renewal
React detects Access Token expiry and silently requests a renewal:

```
POST /api/app/auth/session/refresh
```

BFF forwards request and Core:
- Validates refresh token  
- Issues new Access + Refresh token pair  
- Ensures session is not revoked  

## 5.3 Session Revocation
Sessions must be revoked when:
- User logs out  
- Password is reset  
- Suspicious activity is detected  
- Administrative actions require termination  

Revocation workflow:
- Refresh token is invalidated  
- Optionally, session state recorded in SQL  

---

# 6. API Definitions

## 6.1 Issue Token (Sign-In)
```
POST /api/core/auth/sign-in
```

Returns:
- Access Token  
- Refresh Token  
- SessionId  

## 6.2 Refresh Token
```
POST /api/core/auth/session/refresh
```

Validates:
- Refresh Token integrity  
- Session validity  
- Session expiration  

Returns:
- New Access Token  
- New Refresh Token  

## 6.3 Logout
```
POST /api/core/auth/logout
```

Revokes:
- Refresh token  
- SessionId  

---

# 7. GDPR & Privacy Requirements

- All tokens must be pseudonymised (GUIDs, not emails)
- Tokens may not contain sensitive personal data
- Access logs must not record full tokens
- Refresh tokens must be securely and irreversibly invalidated on logout
- Users must be able to terminate all active sessions

---

# 8. ISO-9001 Governance

Token issuance and session handling must be:
- Documented  
- Repeatable  
- Audited  
- Version-controlled  
- Updated under formal change control  

Significant updates include:
- Token cryptography changes  
- Key rotation schedule updates  
- Session expiry policy adjustments  

---

# 9. Change History

| Version | Date | Author | Notes |
|---------|------|--------|-------|
| 1.0.0 | 2025-12-09 | Business Architect | Initial draft for ISO-9001 security submission |

---
