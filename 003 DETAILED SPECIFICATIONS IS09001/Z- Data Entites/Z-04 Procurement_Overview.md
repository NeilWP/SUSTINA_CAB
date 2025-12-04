
# Data Entity Specification: Z-04 Procurement Overview

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |**Approved On** |
| :--- | :--- | :--- | :--- | :--- |:--- |
| Z-04 | 1.0.0 | **DRAFT** | Business Architect | Product Officer | | 

## 1. Description & Scope
The schematic below illustartes the structure and the relationships the  data objects involved in the Procurement workflow enjoy.  
See the dedictaed pages for the objects related to the CorporateEntity object

```mermaid
erDiagram
    %% ==========================================
    %% 1. CORE TRANSACTIONAL DATA
    %% ==========================================
    Z-04_03_Procurement_Document {
        bigint Doc_ID PK
        string Doc_Type "PO, INVOICE, EXPENSE"
        int Supplier_ID FK
        decimal Total_Amount
    }

    Z-04_02_Procurement_Line {
        bigint Line_ID PK
        bigint Doc_ID FK
        string Description
        decimal Line_Amount
        
        %% THE ACTIVITY BRIDGE
        string Activity_NACE_Code "Source of ESG Classification"
    }

    %% ==========================================
    %% 2. FINANCIAL & ACTIVITY STANDARDS
    %% ==========================================
    Z-04_Procurement_Supplier_Master {
        int Supplier_ID PK
        string Supplier_Name
        string Default_AP_Account_Code "Link -> CoA (Liability)"
    }

    Z-09_Finance_Chart_of_Accounts {
        string Account_Code PK "e.g. Accounts Payable"
        string Account_Name
    }
    
    Z-11_Ref_NACE_Code_Master {
        string NACE_Code PK
        string Description
        boolean Is_Taxonomy_Eligible
    }

    %% ==========================================
    %% 3. REPORTING LEDGERS
    %% ==========================================
    Z-01_Finance_General_Ledger {
        bigint Journal_ID PK
        string Account_Code FK
        bigint Source_Line_ID "Link -> Proc Line"
        decimal Amount
    }
    
    Z-12_Sustainability_Activity_Ledger {
        bigint Activity_ID PK
        bigint GL_Journal_ID FK
        string NACE_Code FK
        decimal Total_CO2e_kg
    }

    %% ==========================================
    %% RELATIONSHIPS (The Traceability Chain)
    %% ==========================================

    %% A. Supplier & Document Flow
    Z-04_Procurement_Supplier_Master ||--o{ Z-04_03_Procurement_Document : "issues"
    Z-04_03_Procurement_Document ||--o{ Z-04_02_Procurement_Line : "contains items"
    
    %% B. Financial Reporting Links
    %% B1: Supplier sets the default AP account
    Z-04_Procurement_Supplier_Master ||..o{ Z-09_Finance_Chart_of_Accounts : "uses default AP code"

    %% B2: The Line posts to the GL
    Z-04_02_Procurement_Line ||..|| Z-01_Finance_General_Ledger : "posts financial impact"
    
    %% C. Activity Reporting (The ESG Connection)
    %% C1: The NACE Master classifies the Line Item
    Z-11_Ref_NACE_Code_Master ||..o{ Z-04_02_Procurement_Line : "classifies spend activity"

    %% C2: The GL feeds the ESG Ledger
    Z-01_Finance_General_Ledger ||..o{ Z-12_Sustainability_Activity_Ledger : "source of spend data"

     Z-01_CorporateEntity {
        int CorporateEntityId PK
        uniqueidentifier CorporateEntityGuid "Unique Key"
        int ParentEntityId FK
        nvarchar EntityName
        nvarchar EntityType
        nvarchar PrimeContactNumber
        nvarchar AddressLine1 "Embedded Address"
        nvarchar City
        nvarchar Region
        char CountryCode
        nvarchar TaxAuthorityName "Snapshot Name"
        nvarchar TaxReference
        bit IsPartOfStructure
        bit IsActive
        uniqueidentifier CreatedBySiteUserGuid "Audit Trail"
        datetime2 CreatedAtUtc
    }
   %% C. Role Overlap (Supplier/Client)
    %% An entity becomes a supplier when linked in this table
    Z-01_CorporateEntity ||..o| Z-04_Procurement_Supplier_Master : "is supplier (if linked)"
```
## Core Details
The **[Procurement].[Supplier\_Master]** table is the foundational master data object for all **external and internal vendors** that transact with your corporate entities. Its primary role is to centralize vendor identity while enabling granular **ESG activity mapping** and **Internal/External boundary classification**.


---
