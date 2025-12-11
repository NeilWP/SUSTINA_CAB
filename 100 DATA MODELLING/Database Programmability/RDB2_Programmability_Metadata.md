# RDB2 Programmability Metadata

---
## `ADCO.usp_Z_02_01_Address_Create`  (Stored Procedure)

### Extracted Documentation
```markdown
Validate Address_Type_Code
Validate Country_ISO2 if supplied
```

---
## `ADCO.usp_Z_02_01_Address_Get`  (Stored Procedure)

_No extracted documentation found._

---
## `ADCO.usp_Z_02_01_Address_Update`  (Stored Procedure)

### Extracted Documentation
```markdown
Validate Address_Type_Code
Validate Country_ISO2 if supplied
```

---
## `ADCO.usp_Z_02_02_Contact_Create`  (Stored Procedure)

### Extracted Documentation
```markdown
Validate PrimaryAddressId if supplied
```

---
## `ADCO.usp_Z_02_02_Contact_Get`  (Stored Procedure)

_No extracted documentation found._

---
## `ADCO.usp_Z_02_02_Contact_Update`  (Stored Procedure)

_No extracted documentation found._

---
## `ADCO.usp_Z_02_03_ContactChannel_Create`  (Stored Procedure)

### Extracted Documentation
```markdown
Validate ContactId
Validate Channel_Type_Code
```

---
## `ADCO.usp_Z_02_03_ContactChannel_Get`  (Stored Procedure)

_No extracted documentation found._

---
## `ADCO.usp_Z_02_03_ContactChannel_Update`  (Stored Procedure)

### Extracted Documentation
```markdown
Validate Channel_Type_Code
```

---
## `Auth.Z-02_01_ValidateSignIn`  (Stored Procedure)

### Extracted Documentation
```markdown
/*
==============================================================================
## 🔐 Stored Procedure: Auth.Z-02_01_ValidateSignIn
==============================================================================

**Description — SEC-02.01 (Sign-In Validation)**  
Supports sign-in validation by returning the user record and the stored
password hash for a given email address.

The actual password verification (Argon2id) is performed in the
application layer, using the stored hash returned by this procedure.
This avoids comparing two different salted hashes in SQL.

**Parameters**
| Name   | Type          | Req | Description                |
|--------|---------------|-----|----------------------------|
| @Email | NVARCHAR(256) | YES | Login email (unique key). |

**Returns**
At most one row containing:

- SiteUserId
- SiteUserGuid
- UserName
- Email
- IsActive
- CurrentStatus
- Temp_Guid_Verified
- PasswordHash (for server-side verification only)

> NOTE:
> - PasswordHash MUST NEVER be forwarded to clients.
> - This proc is for backend use only.

**Version History**
| Date       | Version | Author             | Description                               |
|------------|---------|--------------------|-------------------------------------------|
| 2025-12-08 | 1.0.0   | Neil Watcyn-Palmer | Initial version (string-compare hash).    |
| 2025-12-11 | 1.1.0   | Neil Watcyn-Palmer | Return stored hash for Argon2 verification|
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
## `Corp.usp_Z_01_02_CorporateEntityHistory_GetByEntity`  (Stored Procedure)

_No extracted documentation found._

---
## `Corp.usp_Z_01_02_CorporateEntity_WriteHistory`  (Stored Procedure)

_No extracted documentation found._

---
## `Corp.usp_Z_01_03_InternalClassification_Create`  (Stored Procedure)

_No extracted documentation found._

---
## `Corp.usp_Z_01_03_InternalClassification_GetByEntity`  (Stored Procedure)

_No extracted documentation found._

---
## `Corp.usp_Z_01_03_InternalClassification_Update`  (Stored Procedure)

_No extracted documentation found._

---
## `Corp.usp_Z_01_05_EntityIndustry_Add`  (Stored Procedure)

### Extracted Documentation
```markdown
If this is primary, reset existing primary flags for this entity
```

---
## `Corp.usp_Z_01_05_EntityIndustry_GetByEntity`  (Stored Procedure)

_No extracted documentation found._

---
## `Corp.usp_Z_01_05_EntityIndustry_Remove`  (Stored Procedure)

_No extracted documentation found._

---
## `Corp.usp_Z_01_05_EntityIndustry_Update`  (Stored Procedure)

_No extracted documentation found._

---
## `Corp.usp_Z_01_CorporateEntity_Create`  (Stored Procedure)

### Extracted Documentation
```markdown
Ensure GUID is unique
Validate parent (if provided)
```

---
## `Corp.usp_Z_01_CorporateEntity_Get`  (Stored Procedure)

_No extracted documentation found._

---
## `Corp.usp_Z_01_CorporateEntity_Update`  (Stored Procedure)

_No extracted documentation found._

---
## `ESG.usp_Z_10_01_ESG_Position_Insert`  (Stored Procedure)

### Extracted Documentation
```markdown
Basic factor existence and retrieval
```

---
## `ESG.usp_Z_10_01_ESG_Position_Recalc`  (Stored Procedure)

_No extracted documentation found._

---
## `ESG.usp_Z_10_02_ESG_Forecast_GetByPeriod`  (Stored Procedure)

_No extracted documentation found._

---
## `ESG.usp_Z_10_02_ESG_Forecast_GetHistory`  (Stored Procedure)

_No extracted documentation found._

---
## `ESG.usp_Z_10_02_ESG_Forecast_Insert`  (Stored Procedure)

_No extracted documentation found._

---
## `ESG.usp_Z_10_02_ESG_Forecast_RecalcScenario`  (Stored Procedure)

_No extracted documentation found._

---
## `ESG.usp_Z_10_03_ESG_Factor_Deactivate`  (Stored Procedure)

_No extracted documentation found._

---
## `ESG.usp_Z_10_03_ESG_Factor_GetEffective`  (Stored Procedure)

_No extracted documentation found._

---
## `ESG.usp_Z_10_03_ESG_Factor_GetHistory`  (Stored Procedure)

_No extracted documentation found._

---
## `ESG.usp_Z_10_03_ESG_Factor_Upsert`  (Stored Procedure)

_No extracted documentation found._

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

---
## `Fin.usp_Z_09_00_AccountingEntity_Create`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_00_AccountingEntity_Deactivate`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_00_AccountingEntity_Get`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_00_AccountingEntity_Update`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_01_Account_Create`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_01_Account_Deactivate`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_01_Account_Get`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_01_Account_Update`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_02_GL_ClosePeriod`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_02_GL_GetByEntityPeriod`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_02_GL_PostJournal`  (Stored Procedure)

### Extracted Documentation
```markdown
/* Validate Accounting Entity */
/* Validate Account (must be posting & active) */
/* Validate Cost Centre (if supplied) */
/* Derive Amount_Base using latest official FX rate (if currency differs) */
```

---
## `Fin.usp_Z_09_02_GL_PostRevaluation`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_03_TrialBalance_Build`  (Stored Procedure)

### Extracted Documentation
```markdown
Remove existing TB rows for this period and entity
default, updated via prior TB if available
```

---
## `Fin.usp_Z_09_03_TrialBalance_ClosePeriod`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_03_TrialBalance_Get`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_04_FXRate_Approve`  (Stored Procedure)

### Extracted Documentation
```markdown
Demote other official rates for same pair/date
Promote the chosen rate
```

---
## `Fin.usp_Z_09_04_FXRate_GetEffectiveRate`  (Stored Procedure)

### Extracted Documentation
```markdown
Prefer official rate on or before @RateDate
```

---
## `Fin.usp_Z_09_04_FXRate_Load`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_05_CostCentre_Create`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_05_CostCentre_Deactivate`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_05_CostCentre_Get`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_05_CostCentre_Update`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_06_Budget_ApproveScenario`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_06_Budget_GetByScenario`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_06_Budget_LoadScenario`  (Stored Procedure)

_No extracted documentation found._

---
## `Fin.usp_Z_09_06_Budget_UpdateLine`  (Stored Procedure)

_No extracted documentation found._

---
## `ING.usp_ING_01_04_ActivityDQ_Set`  (Stored Procedure)

_No extracted documentation found._

---
## `ING.usp_ING_01_04_DQDimension_Create`  (Stored Procedure)

_No extracted documentation found._

---
## `ING.usp_ING_01_04_DQDimension_Delete`  (Stored Procedure)

_No extracted documentation found._

---
## `ING.usp_ING_01_04_DQDimension_Update`  (Stored Procedure)

_No extracted documentation found._

---
## `ING.usp_ING_01_04_DQModelDimensionWeight_ClearModel`  (Stored Procedure)

_No extracted documentation found._

---
## `ING.usp_ING_01_04_DQModelDimensionWeight_Set`  (Stored Procedure)

_No extracted documentation found._

---
## `ING.usp_ING_01_04_DQTier_CreateOrUpdate`  (Stored Procedure)

_No extracted documentation found._

---
## `ING.usp_ING_01_04_DQTier_Delete`  (Stored Procedure)

_No extracted documentation found._

---
## `ING.usp_ING_01_04_DQWeightingModel_Close`  (Stored Procedure)

_No extracted documentation found._

---
## `ING.usp_ING_01_04_DQWeightingModel_Create`  (Stored Procedure)

_No extracted documentation found._

---
## `ING.usp_ING_01_04_DQWeightingModel_GetActive`  (Stored Procedure)

_No extracted documentation found._

---
## `Proc.usp_Z_04_01_Supplier_Create`  (Stored Procedure)

### Extracted Documentation
```markdown
Ensure Linked_Entity_Guid is unique in Supplier_Master
```

---
## `Proc.usp_Z_04_01_Supplier_Get`  (Stored Procedure)

_No extracted documentation found._

---
## `Proc.usp_Z_04_01_Supplier_Update`  (Stored Procedure)

_No extracted documentation found._

---
## `Proc.usp_Z_04_02_SupplierActivity_Add`  (Stored Procedure)

_No extracted documentation found._

---
## `Proc.usp_Z_04_02_SupplierActivity_GetBySupplier`  (Stored Procedure)

_No extracted documentation found._

---
## `Proc.usp_Z_04_02_SupplierActivity_Remove`  (Stored Procedure)

_No extracted documentation found._

---
## `Proc.usp_Z_04_02_SupplierActivity_Update`  (Stored Procedure)

_No extracted documentation found._

---
## `Prod.usp_Z_05_01_Product_Create`  (Stored Procedure)

### Extracted Documentation
```markdown
Enforce unique GUID
Enforce unique Product_Code
Optional validation of Primary_Category_Id
```

---
## `Prod.usp_Z_05_01_Product_Get`  (Stored Procedure)

_No extracted documentation found._

---
## `Prod.usp_Z_05_01_Product_Update`  (Stored Procedure)

_No extracted documentation found._

---
## `Prod.usp_Z_05_02_ProductCategory_Reparent`  (Stored Procedure)

_No extracted documentation found._

---
## `Prod.usp_Z_05_02_ProductCategory_Upsert`  (Stored Procedure)

### Extracted Documentation
```markdown
Basic self-parent prevention
Validate parent existence (if provided)
INSERT: enforce unique Category_Code
UPDATE: enforce unique Category_Code (excluding this row)
```

---
## `Prod.usp_Z_05_03_ProductLifecycle_AddEvent`  (Stored Procedure)

### Extracted Documentation
```markdown
Optional existence check within domain
```

---
## `Prod.usp_Z_05_04_ProductESG_MapFactor`  (Stored Procedure)

### Extracted Documentation
```markdown
Optional domain-level existence check
If this mapping is primary, demote others for the same product
```

---
## `Prod.usp_Z_05_04_ProductESG_RemoveFactor`  (Stored Procedure)

_No extracted documentation found._

---
## `Prod.usp_Z_05_05_ProductFinancial_MapAccounts`  (Stored Procedure)

### Extracted Documentation
```markdown
Optional domain-level existence check
If this mapping is default, demote other defaults for same product & entity
```

---
## `Prod.usp_Z_05_05_ProductFinancial_RemoveMapping`  (Stored Procedure)

_No extracted documentation found._

---
## `Ref.usp_Z_02_04_AddressType_Create`  (Stored Procedure)

_No extracted documentation found._

---
## `Ref.usp_Z_02_04_AddressType_Get`  (Stored Procedure)

_No extracted documentation found._

---
## `Ref.usp_Z_02_04_AddressType_Update`  (Stored Procedure)

_No extracted documentation found._

---
## `Ref.usp_Z_02_05_ChannelType_Create`  (Stored Procedure)

_No extracted documentation found._

---
## `Ref.usp_Z_02_05_ChannelType_Get`  (Stored Procedure)

_No extracted documentation found._

---
## `Ref.usp_Z_02_05_ChannelType_Update`  (Stored Procedure)

_No extracted documentation found._

---
## `Ref.usp_Z_02_06_Country_Create`  (Stored Procedure)

_No extracted documentation found._

---
## `Ref.usp_Z_02_06_Country_Get`  (Stored Procedure)

_No extracted documentation found._

---
## `Ref.usp_Z_02_06_Country_Update`  (Stored Procedure)

_No extracted documentation found._

---
## `Ref.usp_Z_10_10_GetActiveNACEVersion`  (Stored Procedure)

_No extracted documentation found._

---
## `Ref.usp_Z_10_11_GetNACEVersionByDate`  (Stored Procedure)

_No extracted documentation found._

---
## `Ref.usp_Z_10_12_SearchNACE_AllVersions`  (Stored Procedure)

_No extracted documentation found._

---
## `Ref.usp_Z_10_13_SearchNACE_ByVersion`  (Stored Procedure)

_No extracted documentation found._

---
## `Ref.usp_Z_10_14_SearchNACE_ByYear`  (Stored Procedure)

_No extracted documentation found._

---
## `Ref.usp_Z_10_15_CompareNACEVersions`  (Stored Procedure)

_No extracted documentation found._

---
## `Ref.usp_Z_10_16_GetNACEVersionHistory`  (Stored Procedure)

_No extracted documentation found._

---
## `Ref.usp_Z_10_40_DQ_NACE_OrphanCheck_ESGFactors`  (Stored Procedure)

### Extracted Documentation
```markdown
/*
      NOTE:
      - This procedure assumes that ESG.Z_10_03_ESG_Factor_Library exists.
      - If it does not yet exist, execution will fail at runtime, but the
        procedure itself can be created thanks to deferred name resolution.
    */
```

---
## `Ref.usp_Z_10_41_DQ_NACE_Completeness`  (Stored Procedure)

_No extracted documentation found._

---
## `Ref.usp_Z_10_42_DQ_NACE_TitleFormatting`  (Stored Procedure)

### Extracted Documentation
```markdown
/*
==============================================================================
## ⚙️ Stored Procedure: Ref.usp_Z_10_42_DQ_NACE_TitleFormatting
==============================================================================

**Description — Z_10_42**
Performs data quality checks on NACE titles:

- Leading/trailing spaces  
- Double spaces  
- Any formatting anomalies  

**Parameters**
_None._

**Returns**
One row per NACE activity with an Issue description summarising what was found.

**Version History**
| Date       | Version | Author             | Description                                     |
|------------|---------|--------------------|-------------------------------------------------|
| 2025-12-10 | 1.0.1   | Neil Watcyn-Palmer | Fixed syntax error; simplified issue detection. |

==============================================================================
*/
```

---
## `Ref.usp_Z_10_43_DQ_NACE_Duplicates`  (Stored Procedure)

### Extracted Documentation
```markdown
1) Intra-version duplicates
2) Cross-version re-use counts
```

---
## `Ref.usp_Z_10_44_DQ_NACE_CrossVersionConsistency`  (Stored Procedure)

_No extracted documentation found._
