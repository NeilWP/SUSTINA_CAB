# Critical Test Registry: PROC-02 (Data Validator)
| **Document ID** | **Version** | **Status** | **Owner** | **Last Review** |
| :--- | :--- | :--- | :--- | :--- |
| TST-001 | 1.0.0 | **DRAFT** | QA Lead | 2025-11-28 |

*These are the "Must-Haves." If these fail, we do not deploy.*

### Component: VAL-02 (Schema Check)
| Test ID | Title | Why we test this | Expected Result |
| :--- | :--- | :--- | :--- |
| **TC-VAL-005** | **Null Amounts** | Prevents database crashes. | **Fail:** Error `ERR_NULL_FIELD`. |
| **TC-VAL-006** | **Bad Dates** | Prevents "Time Travel" reporting. | **Fail:** Error `ERR_INVALID_DATE_FMT` for `13/01/2024`. |

### Component: VAL-03 (Business Logic)
| Test ID | Title | Why we test this | Expected Result |
| :--- | :--- | :--- | :--- |
| **TC-VAL-010** | **Future Date** | Regulatory requirement (GHG Protocol). | **Fail:** Error `ERR_FUTURE_DATE` for tomorrow's date. |
| **TC-VAL-011** | **Currency Typo** | Prevents forex calculation errors. | **Fail:** Error `ERR_INVALID_CURRENCY` for "US Dollar". |
| **TC-VAL-012** | **Negative Spend** | Prevents subtracting emissions by mistake. | **Pass:** Row is accepted but flagged `WRN_NEGATIVE_SPEND`. |
