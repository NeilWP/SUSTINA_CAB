# Data Entity Specification: Z-02 Address & Contacts

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** | **Approved On** |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Z-02 | 1.0.0 | **DRAFT** | Business Architect | Product Officer |  |

## 1. Description & Scope

The **Z-02 Address & Contacts** family defines the shared structures used to hold physical addresses, contact parties, and their communication channels (email, telephone, etc.).  

These entities are **cross-domain** and may be referenced by Corporate Entities, Suppliers, Sites, and other business objects.  

> **Note on referential integrity standard**  
> Logical relationships are shown in the ERD for clarity and enforced in **application and reporting layers**.  
> In line with the agreed standard, **no physical FOREIGN KEY constraints** are implemented in the database.

The Z-02 family currently contains:

- **Z-02.01 Address_Master** – canonical physical/postal address.
- **Z-02.02 Contact_Master** – the contact party (person, team, or generic contact).
- **Z-02.03 Contact_Channel_Master** – email, phone and other channels linked to a contact.

The diagram also shows how Z-02 integrates with **Z-01 CorporateEntity**, via logical pointer columns.
See dedicated pages for the entity for further details.
---

## 2. Entity–Relationship Diagram (ERD)

```mermaid
erDiagram
    %% =======================================================
    %% CORE OBJECT AND Z-02 FAMILY
    %% =======================================================
    Z-01_CorporateEntity {
        int CorporateEntityId PK
        string CorporateEntityGuid
        string EntityName
        string EntityTypeCode
        int RegisteredAddressId
        int PrimaryContactId
        bool IsActive
        datetime2 CreatedAtUtc
        datetime2 ModifiedAtUtc
    }

    Z-02_01_Address_Master {
        int AddressId PK
        string Address_Line1
        string Address_Line2
        string Address_Town
        string Address_County
        string Postal_Code
        string Country_ISO2
        float Longitude
        float Latitude
        string Address_Type_Code
        bool IsActive
        datetime2 CreatedAtUtc
        datetime2 ModifiedAtUtc
    }

    Z-02_02_Contact_Master {
        int ContactId PK
        string Contact_Name
        string Contact_Type_Code
        int PrimaryAddressId
        bool IsActive
        datetime2 CreatedAtUtc
        datetime2 ModifiedAtUtc
    }

    Z-02_03_Contact_Channel_Master {
        int ContactChannelId PK
        int ContactId
        string Channel_Type_Code
        string Channel_Value
        bool IsPrimary
        bool IsActive
        datetime2 CreatedAtUtc
        datetime2 ModifiedAtUtc
    }

    %% =======================================================
    %% LOGICAL RELATIONSHIPS (ENFORCED IN SOFTWARE / REPORTING)
    %% =======================================================
    Z-01_CorporateEntity }o--|| Z-02_01_Address_Master : "RegisteredAddressId to AddressId"
    Z-01_CorporateEntity }o--|| Z-02_02_Contact_Master : "PrimaryContactId to ContactId"
    Z-02_02_Contact_Master ||--o{ Z-02_03_Contact_Channel_Master : "ContactId to ContactId"
```
---
