# Specification: OVR-01 (Address Management)

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |
| :--- | :--- | :--- | :--- | :--- |
| OVR-01 | 1.0.0 | **DRAFT** | Business Architect | Architext |


# Process flow  OVR-01

```mermaid
erDiagram
    %% =======================================================
    %% 1. THE PHYSICAL LOCATION (Source of Address Data)
    %% =======================================================
    dbo_Address_Master {
        int Address_ID PK
        nvarchar Addr_1
        nvarchar Addr_Town
        nvarchar Addr_PCODE
        char Addr_Country "Link -> Country Master"
        decimal Addr_Long
    }

    %% =======================================================
    %% 2. THE TYPE DISCRIMINATOR (The "What" is being addressed)
    %% =======================================================
    Ref_Business_Object_Type {
        nvarchar Object_Type_Code PK "ENTITY, SUPPLIER, TAX_OFFICE_LOCAL"
        nvarchar Description
    }
    
    %% =======================================================
    %% 3. THE COUNTRY STANDARD
    %% =======================================================
    Ref_Country_Master {
        nvarchar Country_Name PK
        char Country_2
        char Country_3
    }
    
    %% =======================================================
    %% 4. THE JUNCTION (The Linker / Polymorphic Map)
    %% =======================================================
    dbo_Address_Addressee {
        int Link_ID PK
        int Address_ID FK "Link to Physical Address"
        nvarchar Addressee_Type_Code FK "Link to Object Type"
        nvarchar Addressee_Object_Key "The Object's GUID or ID"
        nvarchar Address_Usage_Type "HQ, BILLING, SHIPPING"
    }

    %% =======================================================
    %% 5. EXAMPLE BUSINESS OBJECT (The Consumer/Internal Entity)
    %% =======================================================
    Entity_CorporateEntity {
        uniqueidentifier CorporateEntityGuid PK
        string EntityName "Example Consumer"
    }

    %% =======================================================
    %% RELATIONSHIPS (All Logical Soft Links)
    %% =======================================================

    %% A. Physical Location Links
    dbo_Address_Master ||--o{ dbo_Address_Addressee : "is physical location for"
    dbo_Address_Master }|..|| Ref_Country_Master : "is in"

    %% B. Polymorphic Validation
    Ref_Business_Object_Type ||--o{ dbo_Address_Addressee : "validates type"

    %% C. Consumption (Linking an Entity to the system)
    %% Corporate Entity uses the Junction table
    Entity_CorporateEntity ||..o{ dbo_Address_Addressee : "occupies/uses"
```
---
