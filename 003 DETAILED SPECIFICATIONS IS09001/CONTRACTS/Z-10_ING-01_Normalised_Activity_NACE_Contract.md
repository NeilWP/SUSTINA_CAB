# Contract Specification: Z-10 ↔ ING-01  
**Normalised Activity ↔ NACE Sector Resolution Contract**

| **Document ID** | **Version** | **Status** | **Owner (Author)** |
|---|---|---|---|
| **Z-10.CON.01** | 1.0.0 | **DRAFT** | Business Architect |

---

## 1. Purpose

This document defines the **formal contract** between:

- **ING-01 Activity Normalisation** (source of normalised economic activity), and  
- **Z-10 NACE Reference Domain** (source of sector meaning and hierarchy)

The contract specifies **inputs, outputs, responsibilities, and failure handling** to ensure
that ESG calculations remain **deterministic, auditable, and reproducible**.

This document is aligned with:
- ING-01 Activity_Normalisation
- ING-01.01 Activity_Categories
- Z-10 NACE entity and functional specifications

---

## 2. Contracting Domains

### Upstream (Producer)
**ING-01 Activity Normalisation**  
Responsible for producing a **Normalised Activity record** representing “what economic activity occurred”.

### Downstream (Consumer)
**Z-10 NACE Reference Domain**  
Responsible for resolving **sector meaning** and providing **hierarchical classification**.

---

## 3. Contract Boundary

This contract is invoked at the point where a **Normalised Activity** is ready for sector interpretation,
but before ESG factor resolution.

```
Normalised Activity (ING-01)
        ↓
   Z-10 ↔ ING-01 Contract
        ↓
NACE-resolved Activity Context
        ↓
ESG Factor Resolution (Z-10.F03)
```

---

## 4. Input Interface (ING-01 → Z-10)

### 4.1 Required Inputs

| Field | Source | Mandatory | Description |
|---|---|---|---|
| `Normalised_Activity_ID` | ING-01 | YES | Unique identifier of the normalised activity |
| `Activity_Category_Code` | ING-01.01 | YES | High-level activity classification |
| `Accounting_Entity_ID` | Z-09.00 | YES | Finance-facing entity identifier |
| `Activity_Date` | ING-01 | YES | Reference date of the activity |
| `Quantity` | ING-01 | YES | Normalised quantity (unit defined upstream) |
| `Unit_Code` | ING-01 | YES | Unit of measure |

### 4.2 Optional Inputs

| Field | Source | Description |
|---|---|---|
| `Supplier_ID` | ING-01 | Supplier context if available |
| `Product_Code` | ING-01 | Product/service identifier |
| `Geo_Context` | ING-01 | Location context for activity |

---

## 5. Resolution Steps (Z-10 Responsibilities)

Z-10 performs the following **deterministic resolution steps**:

1. Resolve `Accounting_Entity_ID` → `CorporateEntity_ID` (Z-09 → Z-01)
2. Resolve `CorporateEntity_ID` → `NACE_Code` via Z-01.05 CorporateEntity_Industry_Map
3. Resolve `NACE_Code` → `NACE_Version_ID` via Z-10.01 Activity Master
4. Resolve structural hierarchy via Z-10.03 (Section / Division / Group / Class)

All resolutions are **version-scoped**.

---

## 6. Output Interface (Z-10 → ESG)

### 6.1 Mandatory Outputs

| Field | Description |
|---|---|
| `Normalised_Activity_ID` | Echo from input |
| `NACE_Code` | Resolved economic activity code |
| `NACE_Version_ID` | Version used for resolution |
| `NACE_Section_Code` | Top-level section |
| `NACE_Division_Code` | Division |
| `NACE_Group_Code` | Group (if applicable) |
| `NACE_Class_Code` | Class (if applicable) |

### 6.2 Derived Context Flags

| Flag | Meaning |
|---|---|
| `Is_Primary_Industry` | Derived from entity mapping |
| `Is_Fallback_Assignment` | True if non-primary logic applied |
| `Is_Deprecated_Code` | True if resolved code is inactive |

---

## 7. Failure Handling & Data Quality

| Scenario | Behaviour |
|---|---|
| Missing entity → NACE mapping | Activity flagged; ESG calculation continues with gap |
| Invalid NACE code | Activity rejected from ESG calculation |
| Missing hierarchy row | Activity flagged; aggregation disabled |
| Mixed-version resolution | Not permitted; processing fails |

All failures are **logged, reportable, and auditable**.

---

## 8. Non-Responsibilities (Explicit)

This contract **does NOT**:
- Assign NACE codes to entities
- Modify ING-01 activity data
- Resolve ESG factors directly
- Override Finance quantities or periods

---

## 9. Governance & Change Control

- Changes to this contract require joint approval by:
  - ESG Data Owner
  - Reference Data Steward (NACE)
- Contract changes are versioned independently of entity schemas
- Backward compatibility is mandatory

---

## 10. Architectural Statement

> ING-01 determines **what happened**  
> Z-09 determines **who posted it**  
> Z-10 determines **what it means**  
> ESG determines **why it matters**

---

## 11. Change History

| Version | Date | Author | Notes |
|---|---|---|---|
| 1.0.0 | 2025-12-12 | Business Architect | Initial ING-01 ↔ Z-10 contract definition |
