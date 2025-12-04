
# Process Specification: P- (Procurement Master Workflow)

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |**Approved On** |
| :--- | :--- | :--- | :--- | :--- |:--- |
| P- | 1.0.0 | **DRAFT** | Business Architect | Product Officer | | 

## 1. Description & Scope
This document defines the master architectural workflow for the **Procurement & Scope 3 Data Ingestion** module.
* **Naming Convention:**
    * All objects owned by this workflow are dsignated by the ALpha prefix "P-" followed by a numeric ID.
    * Objects not owned by this workflow but impacting it (creating a dependency) will bear an alternative pair of characters followed by a numeric id. 
* **Objective:**
    * To securely ingest, validate, and categorize supply chain data to calculate Scope 3 (Category 1) emissions in compliance with the GHG Protocol.
    * To  introduce the  objects supporting the workflow whcih are then described in other pages.
* **Access Control:** Strict **Role-Based Access Control (RBAC)** is enforced. The flow creates a dependency on **M-002 (Authentication)**; only authenticated sessions with `WRITE_SCOPE_3` permissions may initiate this routine.

## 2. Process Flow Diagram
*The following diagram illustrates the logical path from User Login through to Scope 3 Ledger commit.*

```mermaid
flowchart LR
    %% =======================================================
    %% STYLING: HIGH CONTRAST WIREFRAME
    %% =======================================================
    classDef nodeStyle fill:none,stroke:#ffffff,stroke-width:2px,color:#ffffff;
    classDef dbStyle fill:none,stroke:#ffffff,stroke-width:2px,stroke-dasharray: 2 2,color:#ffffff;
    linkStyle default stroke:#ffffff,stroke-width:2px;

    %% =======================================================
    %% 1. ENTRY
    %% =======================================================
    START((Start)) --> UI_04{"ID: UI-04<br/>Action Hub"}

    %% =======================================================
    %% 2. INGESTION (THE SOURCE)
    %% =======================================================
    UI_04 -- "Action: Ingest" --> PROC_01
    
    PROC_01[["ID: PROC-01<br/>Ingestion<br/>(File / Manual)"]]

    %% =======================================================
    %% 3. THE OUTPUTS (Branching)
    %% =======================================================
    
    %% Output A: It's a New Supplier -> Send to MDM
    PROC_01 -- "Output:<br/>New Supplier" --> PROC_21[["ID: PROC-21<br/>Upsert Supplier"]]
    
    %% Output B: It's a PO -> Send to Audit
    PROC_01 -- "Output:<br/>PO Data" --> PROC_09[["ID: PROC-09<br/>PO Analyzer"]]

    %% Output C: It's an Invoice -> Send to Audit
    PROC_01 -- "Output:<br/>Invoice Data" --> PROC_10[["ID: PROC-10<br/>Inv Analyzer"]]

    %% Output D: It's an Expense -> Send to Audit
    PROC_01 -- "Output:<br/>Expense Data" --> PROC_11[["ID: PROC-11<br/>Exp Analyzer"]]

    %% =======================================================
    %% 4. DOWNSTREAM PROCESSING
    %% =======================================================
    
    %% Supplier MDM Storage
    PROC_21 --> DAT_SUP[("ID: DAT-SUP<br/>Supplier DB")]
    
    subgraph PROC_ENGINE [Audit Engine]
        direction LR
        %% Analyzers feed Intelligence
        PROC_09 & PROC_10 & PROC_11 --> PROC_03[["ID: PROC-03<br/>ML Classifier"]]
        
        %% Intelligence feeds Ledger
        PROC_03 --> PROC_08[["ID: PROC-08<br/>Ledger Write"]]
        PROC_08 --> DAT_02[("ID: DAT-02<br/>S3 Ledger")]
    end

    %% =======================================================
    %% STYLES
    %% =======================================================
    class START,UI_04,PROC_01,PROC_21,PROC_09,PROC_10,PROC_11,PROC_03,PROC_08 nodeStyle;
    class DAT_SUP,DAT_02 dbStyle;
    
    style PROC_ENGINE fill:none,stroke:#ffffff,stroke-width:1px,color:#ffffff,stroke-dasharray: 5 5
```
---

## Entity Relationship diagram for Procurement
```mermaid
erDiagram
    %% =================================================
    %% 1. MASTER DATA (Supplier & Location)
    %% =================================================
    Procurement_Supplier_Master {
        int Supplier_ID PK
        string Supplier_Name
        string Tax_ID
        string Default_NACE_Code "Default Activity"
    }

    dbo_Address_Master {
        int Address_ID PK
        string Addr_1
        string City
        string Country
    }

    %% Junction to link Supplier to Address
    dbo_Address_Addressee {
        int Link_ID PK
        int Address_ID FK
        string Addressee_Object_Key "Supplier ID"
        string Type "SUPPLIER"
    }

    %% =================================================
    %% 2. TRANSACTIONAL DATA (The Document Chain)
    %% =================================================
    Procurement_Document {
        bigint Doc_ID PK
        string Doc_Type "PO, INVOICE, RECEIPT"
        string Doc_Reference "Vendor Invoice #"
        date Doc_Date
        decimal Total_Amount
        int Supplier_ID FK
    }

    %% The "Good or Service" Supplied
    Procurement_Line {
        bigint Line_ID PK
        bigint Doc_ID FK
        string Description "e.g. 100 Laptops, Consulting"
        decimal Quantity
        string Unit "Each, Hours, Liters"
        decimal Line_Amount
        
        %% THE CRITICAL ACTIVITY LINK
        string Activity_NACE_Code "The Business Activity"
    }

    %% =================================================
    %% 3. KNOWLEDGE & REPORTING (Finance & Activity)
    %% =================================================
    
    %% The Definition of the Activity (Business Knowledge)
    Ref_NACE_Code_Master {
        string NACE_Code PK
        string Description "e.g. Computer Programming"
        boolean Is_Green "Taxonomy Eligible"
    }

    %% The Financial Result (GL Reporting)
    Finance_General_Ledger {
        bigint Journal_ID PK
        bigint Source_Line_ID "Link -> Proc Line"
        string Account_Code "GL Account"
        decimal Amount_Debit
        date Posting_Date
    }

    %% =================================================
    %% RELATIONSHIPS
    %% =================================================

    %% 1. Location Logic
    Procurement_Supplier_Master ||--o{ dbo_Address_Addressee : "is located at"
    dbo_Address_Master ||--o{ dbo_Address_Addressee : "provides address for"

    %% 2. Purchasing Logic
    Procurement_Supplier_Master ||--o{ Procurement_Document : "issues"
    Procurement_Document ||--o{ Procurement_Line : "contains items"

    %% 3. Business Activity Knowledge
    %% The NACE Code defines what the goods/services actually represent
    Ref_NACE_Code_Master ||..o{ Procurement_Line : "defines activity of"
    
    %% 4. Financial Reporting Integration
    %% The GL Entry is derived directly from the Line Item
    Procurement_Line ||..|| Finance_General_Ledger : "generates financial impact"

```

---

## 3. Component Dictionary (Traceability Matrix)

The following components are subject to **ISO 9001 Quality Assurance** and **ISO 27001 Security** testing.

### A. Security & Interface Layer
| Component ID | Name | Functional Description | Audit Control |
| :--- | :--- | :--- | :--- |
| **SEC-01** | Auth & SSO Logic | Validates JWT tokens and enforces session timeouts. | **Verification:** Penetration Test Report (Annual). |
| **UI-02** | Action Router | Routes user to modules based on their Entity permissions. | **Control:** Unit tests ensuring cross-tenant data isolation. |

### B. Procurement Logic Layer (The Core Engine)
| Component ID | Name | Functional Description | Methodology / Standard |
| :--- | :--- | :--- | :--- |
| **PROC-01** | File Uploader | Accepts `.csv`, `.xlsx`. Sanitizes inputs to prevent SQL injection/XSS. | **Security:** Virus scan on upload. |
| **PROC-02** | Data Validator | Checks for required columns (Date, Vendor, Amount, Currency). Rejects malformed rows. | **Quality:** Rejection log is presented to user. |
| **PROC-03** | ML Classifier | Maps vendor strings (e.g., "AWS") to Economic Sectors (e.g., "Data Processing"). | **AI Gov:** Users must approve/override AI suggestions (Human-in-the-loop). |
| **PROC-05** | **EEIO Spend Engine** | 1. Calls `ENT-02` to strip Tax/VAT.<br>2. Applies **Inflation Adjustment** (CPI/PPI).<br>3. Maps to USEEIO/Exiobase factors. | **CSRD:** "Spend-based method" (GHG Protocol Scope 3 Guidance). |
| **PROC-06** | LCA Factor Engine | Calculates based on physical units (kg, liters) using standard libraries (Ecoinvent, DEFRA). | **CSRD:** "Average-data method." |
| **PROC-07** | PCF Ingestion | Ingests verified Product Carbon Footprints (ISO 14067) directly from suppliers. | **CSRD:** "Supplier-specific method" (Gold Standard). |
| **PROC-08** | Consolidator | Merges calculated lines into a unified dataset. | **Data Integrity:** Checksum validation. |

### C. Data Persistence
| Component ID | Name | Storage Policy |
| :--- | :--- | :--- |
| **DAT-02** | Scope 3 Ledger | Immutable append-only log of all calculated emissions. |

---

## 4. Regulatory Compliance Note
This workflow supports the principle of **Double Materiality** by enabling a "Hybrid Methodology."
1.  **Default:** The system falls back to `PROC-05` (Spend-based) to ensure 100% coverage of the supply chain.
2.  **Refinement:** As data quality improves, specific line items are moved to `PROC-07` (Supplier Specific), increasing accuracy without breaking the audit trail.
