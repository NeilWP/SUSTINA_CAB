
# Data Entity Specification: Z-10 Ref_NACE_Version_Master

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |**Approved On** |
| :--- | :--- | :--- | :--- | :--- |:--- |
| Z-10 | 1.0.0 | **DRAFT** | Business Architect | Product Officer | | 

## 1. Description & Scope
The schematic below illustartes the structure and the relationships the  Ref_NACE_Version_Master data object enjoys.  


```mermaid
erDiagram

```

## Core Details
The **[Ref].[NACE\_Version\_Master]** table is the **foundational reference data object** for managing the chronology and stability of all economic activity codes used in the application. Its primary role is to ensure **accurate, auditable, and historically consistent** reporting by linking NACE classifications to a specific official publication date.

**Key Characteristics and Data Fields**
The table structure is designed to support version control, allowing the system to accurately report historical data using the NACE standard that was valid during that period.

---

## 1 Identity and Structure Management
This table establishes the metadata for every published version of the NACE standard used by the organization.

| Feature | Attribute / Column | Primary Function |
| :--- | :--- | :--- |
| **Primary Key** | NACE_Version_ID | A standard auto-incrementing integer (`INT IDENTITY`) used internally for simple database lookups and linking to the specific NACE codes. |
| **Version Identifier** | Version_Code, Publication_Name | Stores the concise code (e.g., `REV_2_1`) and the official name (e.g., `NACE Rev. 2.1`) for clear identification. |
| **Chronology Data** | Publication_Date | Stores the official date the standard was released, essential for compliance and historical reporting accuracy. |

Audit Trail: The table maintains standard audit fields (`Is_Active`) to control which version of the NACE codes is currently available for application use.

---

## 2 Core Operational & Compliance Data
The table serves as a dependency for every NACE code, making it critical for compliance and classification accuracy across financial and sustainability reporting.

| Feature | Attributes / Columns | Purpose |
| :--- | :--- | :--- |
| **Identification** | Publication_Name | Stores the official, regulatory name of the NACE standard for documentation. |
| **Compliance Data** | Publication_Date | Provides the **auditable date** required to ensure that historical economic activity is classified using the NACE revision that was in effect at that time. |
| **Activity Dependency** | *Implied* | Serves as the **one-to-many parent** to the `Ref.NACE_Code_Master` table; every single NACE code record must logically refer to an ID in this table. |
| **Control Flags** | Is_Active | Binary flag used to control which version of the NACE standard is currently active and available for use by the system's ML classification engines. |

---

## 3. Data Management
| Obejct Type | Name | Description |
| :--- | :--- | :--- |
| **Utility SP** | usp_GetActiveNACEVersion | Procedure used by the application to retrieve the currently approved and active `NACE_Version_ID` for transactional processing. |
| **Reporting SP** | usp_GetNACEbyDate | Procedure used to efficiently retrieve the specific NACE codes that were valid for a given reporting period or date. |

---

## Architectural Role
This table is the source of truth for **version control and chronology** within the economic activity module.
* **Compliance Control:** It dictates which version of the NACE standard is used for **EU Taxonomy** and **Scope 3** calculations during any specific time period.
* **Data Integrity:** It ensures that the classification applied by the ML classifier (`PROC-03`) is linked to a permanent, auditable version.
---