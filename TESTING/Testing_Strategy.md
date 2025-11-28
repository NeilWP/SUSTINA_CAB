# Quality Assurance Strategy: TST-001

| **Document ID** | **Version** | **Status** | **Owner** | **Last Review** |
| :--- | :--- | :--- | :--- | :--- |
| T- | 1.1.0 | **DRAFT** | QA Lead | 2025-11-28 |

## 1. Testing Philosophy (Lean/Agile)
As a small team, we prioritize **High-Risk Logic** over 100% code coverage. We do not write unit tests for simple getters/setters or boilerplate UI code.

We explicitly test **Carbon Math** (Scope calculations) and **Data Validation** (Integrity rules) because errors in these areas create financial and regulatory liability.

**Rule of Thumb:** *"If a bug could change the reported Carbon Number, it must have a test."*

### Toolchain
* **Management:** JIRA (Test definition, traceability, and defect remediation).
* **Backend Execution:** PyTest (Python).
* **Frontend Execution:** Jest (React/JS).

---

## 2. Test Documentation Architecture
To maintain modularity, test cases are not listed in this strategy document. Instead, they are decentralized into **Critical Test Registries (CTR)** mapped to specific Component IDs.

### The "One-to-One" Rule
For every major architectural component (e.g., `PROC-01`, `DAT-02`), there must be a corresponding Registry File.

* **File Naming Convention:** `Critical-Test-Registry_{COMPONENT_ID}.md`
* **Storage Location:** `/docs/qa/registries/`

### Handling Components with No Critical Logic
Not every component requires high-risk testing (e.g., a static "About Us" page).
If a component has no critical logic, the Registry File must still exist, but must contain the **Non-Critical Declaration**:

> *"Risk Assessment completed. No critical business logic identified for this component. Standard integration testing is sufficient."*

*In this way we seek to  assure Auditors we did not forget to test this, we activley determined it did not require a test.*

---

## 3. How to Read a Critical Test Plan
Our Test Registries follow a strict tabular format. Here is how to interpret the columns:

| Column | Description |
| :--- | :--- |
| **Test ID** | A unique identifier (e.g., `TC-VAL-005`). This ID must be referenced in JIRA tickets and Code Comments. |
| **Title** | A short, human-readable name for the scenario. |
| **Why we test this** | The **Risk Justification**. This explains the business impact of failure (e.g., "Prevents database crashes" or "Regulatory Requirement"). |
| **Expected Result** | The specific condition that defines "Pass." This is the contract the developer must meet. |

---

## 4. Traceability Matrix (Master Index)
*This section links the Architecture to the Test Registries.*

| Component ID | Component Name | Registry File Link | Status |
| :--- | :--- | :--- | :--- |
| **PROC-01** | File Ingestion API | [Link to CTR-PROC-01](./registries/Critical-Test-Registry_PROC-01.md) | Pending |
| **PROC-02** | Data Validator | [Link to CTR-PROC-02](./registries/Critical-Test-Registry_PROC-02.md) | **Active** |
| **PROC-03** | AI Classifier | [Link to CTR-PROC-03](./registries/Critical-Test-Registry_PROC-03.md) | Draft |
| **UI-01** | Navbar | [Link to CTR-UI-01](./registries/Critical-Test-Registry_UI-01.md) | **Non-Critical** |