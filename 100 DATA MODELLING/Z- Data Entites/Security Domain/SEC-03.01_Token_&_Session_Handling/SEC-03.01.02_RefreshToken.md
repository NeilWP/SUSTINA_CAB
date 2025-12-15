# Data Entity Specification: SEC-03.01.02 RefreshToken

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Reviewer** |
|-----------------|-------------|------------|---------------------|--------------|
| **SEC-03.01.02** | **1.0.0** | **DRAFT** | Business Architect | Product Officer |
<div><sub><strong>Table – 1 SEC-03.01.02 –</strong> Document control header</sub></div></br>

---

## 1. Description & Scope

The **RefreshToken** entity represents a **persisted renewal artefact** bound to a session.

It supports:
- Controlled session renewal
- Rotation and replay prevention
- Deterministic revocation

---

## 2. ERD (Context)

```mermaid
erDiagram
    Auth_SEC_03_01_Session {
        uniqueidentifier SessionId PK
    }

    Auth_SEC_03_01_RefreshToken {
        uniqueidentifier RefreshTokenId PK
        uniqueidentifier SessionId
        datetime2 IssuedAtUtc
        datetime2 ExpiresAtUtc
        datetime2 RevokedAtUtc
        bit IsRevoked
        int RotationCounter
    }

    Auth_SEC_03_01_Session ||--o{ Auth_SEC_03_01_RefreshToken : "SessionId (logical)"
```
<div><sub><strong>Figure – 1 SEC-03.01.02 –</strong> RefreshToken ERD</sub></div></br>

---

## 3. Table Definition

**Table:** `[Auth].[SEC_03_01_RefreshToken]`

| Column | Type | Null | Notes |
|------|------|------|------|
| `RefreshTokenId` | UNIQUEIDENTIFIER | NOT NULL | Surrogate identifier |
| `SessionId` | UNIQUEIDENTIFIER | NOT NULL | Logical session reference |
| `IssuedAtUtc` | DATETIME2(3) | NOT NULL | Issuance timestamp |
| `ExpiresAtUtc` | DATETIME2(3) | NOT NULL | Expiry boundary |
| `RevokedAtUtc` | DATETIME2(3) | NULL | Revocation timestamp |
| `IsRevoked` | BIT | NOT NULL | Revocation indicator |
| `RotationCounter` | INT | NOT NULL | Replay detection |

---

## 4. Business Rules

- Refresh tokens are single-use per rotation.
- Rotation invalidates prior token.
- Revoked tokens cannot renew sessions.


```mermaid
sequenceDiagram
    autonumber
    participant C as Client
    participant AS as Auth Service
    participant DB as Auth DB (SEC-03)
    participant AUD as Audit Spine (security events)

    Note over C,AS: Preconditions: Access token expired. Client holds current RefreshToken RT(n).

    C->>AS: POST /token/refresh (RT(n), SessionId, CorrelationId)
    AS->>DB: Load Session (SEC_03_01_Session) by SessionId
    DB-->>AS: Session state (IsActive, ExpiresAtUtc, RevokedAtUtc)

    alt Session revoked/expired/inactive
        AS->>AUD: Emit event: SESSION_RENEWAL_DENIED (reason)
        AS-->>C: 401/403 Deterministic failure (no new tokens)
    else Session active
        AS->>DB: Load RefreshToken RT(n) (SEC_03_01_RefreshToken)
        DB-->>AS: RT(n) state (IsRevoked, ExpiresAtUtc, RotationCounter)

        alt RT(n) revoked/expired
            AS->>AUD: Emit event: REFRESH_REJECTED (revoked/expired)
            AS-->>C: 401/403 Deterministic failure
        else RT(n) valid
            Note over AS,DB: Rotation is an atomic, single transaction.
            AS->>DB: Begin Txn
            AS->>DB: Re-check RT(n) not consumed/revoked (row lock)
            AS->>DB: Revoke RT(n) (IsRevoked=1, RevokedAtUtc=now, Reason=ROTATED)
            AS->>DB: Insert RT(n+1) (new RefreshTokenId, IssuedAtUtc, ExpiresAtUtc, RotationCounter+1)
            AS->>DB: Update Session LastActivityAtUtc=now
            AS->>DB: Commit Txn

            AS->>AUD: Emit event: REFRESH_ROTATED (old RT id -> new RT id, SessionId)
            AS->>AS: Mint new AccessToken AT(new) bound to SessionId (short-lived)
            AS-->>C: 200 {AccessToken AT(new), RefreshToken RT(n+1)}
        end
    end

    Note over C,AS: Replay attempt (attacker reuses RT(n) after rotation) will fail because RT(n) is revoked.

```


---

## 5. Change History

| Version | Date | Author | Notes |
|--------|------|--------|-------|
| 1.0.0 | 2025-12-14 | Business Architect | Initial RefreshToken entity |
