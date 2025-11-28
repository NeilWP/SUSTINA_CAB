# Critical Test Registry: PROC-01 (File Ingestion)
| **Document ID** | **Version** | **Status** | **Owner** | **Last Review** |
| :--- | :--- | :--- | :--- | :--- |
| CTR-PROC-01 | 1.0.0 | **DRAFT** | QA Lead | 2025-11-28 |

*These are the "Must-Haves." If these fail, we do not deploy.*

### Component: SVC-01 (Integrity & Security)
| Test ID | Title | Why we test this | Expected Result |
| :--- | :--- | :--- | :--- |
| **TC-ING-001** | **Hash Mismatch** | Prevents "Time-of-Check" security attacks (replacing safe file with malicious one). | **Fail:** Error `409 Conflict` or `ERR_HASH_MISMATCH`. |
| **TC-ING-002** | **Formula Injection** | Prevents Remote Code Execution via Excel Macros. | **Pass:** Formulas (starting with `=`) are stripped or escaped. |

### Component: PROC-01-B (Column Mapper)
| Test ID | Title | Why we test this | Expected Result |
| :--- | :--- | :--- | :--- |
| **TC-ING-005** | **Mapping Execution** | Ensures we read the correct data column (e.g. "Cost" -> "Amount"). | **Pass:** Output JSON `amount_gross` matches values from the mapped source column. |
| **TC-ING-006** | **Missing Header** | Prevents silent failures if user uploads wrong template. | **Fail:** Specific Error: *"Column 'Total Spend' not found in file."* |
