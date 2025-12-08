# RDB2 Programmability Metadata

---
## `Auth.Z-02_01_ValidateSignIn`  (Stored Procedure)

### Extracted Documentation
```markdown
/*
==============================================================================
## 🔐 Stored Procedure: Auth.Z-02_01_ValidateSignIn
==============================================================================

**Description — SEC-02.01 (Sign-In Validation)**  
Validates a sign-in attempt by checking the supplied **Email** and 
client-side hashed **PasswordHash** against the Auth tables.

All credential validation is performed inside this procedure.  
If the combination is valid, a single row is returned;  
if not, **no rows** are returned.

**Parameters**
| Name           | Type              | Req | Description                            |
|----------------|-------------------|-----|----------------------------------------|
| @Email         | NVARCHAR(256)     | YES | Login email.                           |
| @PasswordHash  | NVARCHAR(512)     | YES | Hashed password received from the app. |

**Returns**
A single row only when the credentials are valid, containing:

- SiteUserId
- SiteUserGuid
- UserName
- Email
- IsActive
- CurrentStatus
- Temp_Guid_Verified

**No password hash is returned by this procedure.**

**Version History**
| Date       | Version | Author             | Description                                  |
|------------|---------|--------------------|----------------------------------------------|
| 2025-12-08 | 1.0.0   | Neil Watcyn-Palmer | Initial creation for SEC-02.01 sign-in flow. |
============================================================================== 
*/
```

---
## `Auth.usp_CompletePasswordReset`  (Stored Procedure)

### Extracted Documentation
```markdown
Look up token (Exact Match required due to Collation)
Update Password (Stored exactly as provided)
```

---
## `Auth.usp_DeactivateSiteUser`  (Stored Procedure)

### Extracted Documentation
```markdown
/*
==============================================================================
## ⚙️ Stored Procedure: Auth.usp_DeactivateSiteUser
==============================================================================
**Description**
Sets a user to Inactive (IsActive=0) and Suspended status (-2).
Replaces legacy 'Scenario 3' functionality.

**Parameters**
- @SiteUserGuid (UNIQUEIDENTIFIER): The user to deactivate.

**Returns**
Standard result set.

**Version History**
| Date       | Version | Author             | Description |
| :---       | :---    | :---               | :--- |
| 2025-11-21 | 1.0.0   | Neil Watcyn-Palmer | Initial creation to replace legacy deactivation. |
==============================================================================
*/
| :---    | :---               | :--- |
Suspended/Deactivated
```

---
## `Auth.usp_GetLoginDetailsByEmail`  (Stored Procedure)

### Extracted Documentation
```markdown
Formatted
Returns the exact binary-matched hash
```

---
## `Auth.usp_InitiatePasswordReset`  (Stored Procedure)

### Extracted Documentation
```markdown
Generate a text based token (lowercase formatting enforced here if desired)
Cleanup old tokens
Insert into table (Collation protects casing)
```

---
## `Auth.usp_RegisterSiteUser`  (Stored Procedure)

### Extracted Documentation
```markdown
/*
==============================================================================
## ⚙️ Stored Procedure: Auth.usp_RegisterSiteUser
==============================================================================
**Description**
Handles the creation of a new SiteUser and their initial Password.
It performs validation (duplicate check) and executes the insert within a 
transaction across both [SiteUser] and [SiteUserPassword] tables.

**Parameters**
- @UserName (NVARCHAR 100): The display name.
- @Email (NVARCHAR 256): Unique email address.
- @PasswordHash (NVARCHAR 500): The pre-hashed password string.

**Returns**
A single-row result set containing:
- StatusCode (0 = Success, >0 = Error)
- StatusMessage
- ResultSiteUserId (The INT ID of the new user)
- ResultSiteUserGuid (The GUID for the API, returned as GUID type)
- VerificationToken (For the welcome email, returned as GUID type)

**Version History**
| Date       | Version | Author             | Description |
| :---       | :---    | :---               | :--- |
| 2025-11-21 | 2.0.0   | Neil Watcyn-Palmer | Refactored from usp_SiteUser_Manage. |
| 2025-11-23 | 2.0.5    | AI Final Fix       | Reverted GUID output to UNIQUEIDENTIFIER type. This is CRITICAL for C# model mapping to enable email sending, while the App API handles the final lowercase JSON formatting. |
==============================================================================
*/
=============================================================================
3. STORED PROCEDURES
=============================================================================
|
Temporary table to hold results before final formatting/output
Validation
Insert Profile
Insert Password (Protected by Case-Sensitive Table Collation)
Store raw GUIDs temporarily
FINAL OUTPUT: Return raw GUIDs to C# (uniqueidentifier type)
```

---
## `Auth.usp_SiteUser_AssignRole`  (Stored Procedure)

### Extracted Documentation
```markdown
/*
==============================================================================
## ⚙️ Stored Procedure: Auth.usp_SiteUser_AssignRole
==============================================================================
**Description**
Assigns a role to a user by RoleCode. 
Resolves both SiteUserId and RoleId internally.

**Parameters**
- @SiteUserId (INT): Optional if Guid provided.
- @SiteUserGuid (GUID): Optional if ID provided.
- @RoleCode (NVARCHAR 64): The code (e.g. 'ADMIN', 'USER') to assign.
- @AssignedBySiteUserId (INT): Audit ID of the admin performing the action.

**Version History**
| Date       | Version | Author             | Description |
| :---       | :---    | :---               | :--- |
| 2025-11-21 | 1.0.0   | Neil Watcyn-Palmer | Moved to Auth schema and standardized headers. |
==============================================================================
*/
| :---    | :---               | :--- |
```

---
## `Auth.usp_SiteUser_Manages_Special_Users`  (Stored Procedure)

### Extracted Documentation
```markdown
/*
==============================================================================
## ⚙️ Stored Procedure: Auth.usp_SiteUser_Manages_Special_Users
==============================================================================
**Description**
Toggles the reserved/special status of a user.

**Parameters**
- @SiteUserGuid (GUID): The user to update.
- @IsSpecial (BIT): 1 = Reserved, 0 = Normal.

**Version History**
| Date       | Version | Author             | Description |
| :---       | :---    | :---               | :--- |
| 2025-11-17 | 1.0.0   | Neil Watcyn-Palmer | Initial procedure for special user management. |
==============================================================================
*/
| :---    | :---               | :--- |
```

---
## `Auth.usp_VerifySiteUserEmail`  (Stored Procedure)

### Extracted Documentation
```markdown
/*
==============================================================================
## ⚙️ Stored Procedure: Auth.usp_VerifySiteUserEmail
==============================================================================
**Description**
Completes the email verification flow by validating a temporary GUID.
If valid:
1. Updates PreviousStatus to the old value (0).
2. Updates CurrentStatus to Active (1).
3. Updates UpdatedAtUtc timestamps.
4. Clears the temporary security tokens.

**Parameters**
- @VerifyTempGuid (UNIQUEIDENTIFIER): The token from the email link.

**Returns**
Standard result set (StatusCode 0 = Success).

**Version History**
| Date       | Version | Author             | Description |
| :---       | :---    | :---               | :--- |
| 2025-11-21 | 2.0.0   | Neil Watcyn-Palmer | Initial creation. |
| 2025-11-22 | 2.1.0   | Neil Watcyn-Palmer | Added PreviousStatus and UpdatedAtUtc logic. |
==============================================================================
*/
| :---    | :---               | :--- |
1. Find the User ID based on the token
2. Validation Checks
3. Perform the State Transition
1. Move current status to previous (SQL uses the value BEFORE the update)
2. Set new status
Active
3. Update Audit Timestamps
4. Security Cleanup
```

---
## `Entity.usp_CreateCorporateEntity`  (Stored Procedure)

### Extracted Documentation
```markdown
/*
==============================================================================
## ⚙️ Stored Procedure: Entity.usp_CreateCorporateEntity
==============================================================================
**Description**
Handles the creation of a new Entity (ENT-01) and optionally links it to a 
Parent (ENT-02).
Enforces the mandatory "User Attestation" by capturing the creator's GUID.

**Parameters**
- @ParentEntityId (INT): Optional. If provided, creates a Child entity.
- @EntityName, @Address... : Core profile data.
- @UserGuid (GUID): The ID of the user performing the Attestation.
- @UserName (NVARCHAR): The Name of the user attesting.

**Returns**
- ResultEntityId, ResultEntityGuid

**Version History**
| Date       | Version | Author             | Description |
| :---       | :---    | :---               | :--- |
| 2025-11-22 | 1.0.0   | Neil Watcyn-Palmer | Initial creation. |
==============================================================================
*/
| :---    | :---               | :--- |
1. Uniqueness Check (Data Layer Enforcement)
2. Insert Record
3. Log Initial History (Creation Event)
In dev, you might re-throw: THROW;
```

---
## `Entity.usp_UpdateEntityStructure`  (Stored Procedure)

### Extracted Documentation
```markdown
/*
==============================================================================
## ⚙️ Stored Procedure: Entity.usp_UpdateEntityStructure
==============================================================================
**Description**
Handles the Re-Organization of an entity (ENT-02).
Moves an entity from one Parent to another, or detaches it.
Logs the FULL HISTORY of the move (Old Parent -> New Parent).

**Parameters**
- @CorporateEntityGuid (GUID): The entity being moved.
- @NewParentEntityGuid (GUID): The new parent (NULL if becoming independent).
- @UserGuid (GUID): The user performing the re-org.

**Version History**
| Date       | Version | Author             | Description |
| :---       | :---    | :---               | :--- |
| 2025-11-22 | 1.0.0   | Neil Watcyn-Palmer | Initial creation for Org Chart management. |
==============================================================================
*/
| :---    | :---               | :--- |
Resolved from GUID
1. Resolve IDs (Enforcing Software Referential Integrity)
If a new parent is provided, verify it exists and isn't the entity itself (Circular check simple)
2. Perform Update & Log History
Update the Record
Log the History (Re-Org)
```
