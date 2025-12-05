# Data Entity Specification: Z-02 Address & Contacts

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** | **Approved On** |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Z-02 | 1.1.0 | **DRAFT** | Business Architect | Product Officer |  |

## 1. Description & Scope

The **Z-02 Address & Contacts** family defines the shared, reusable structures used to model:

- Physical and postal **addresses**
- **Contact parties** (persons, teams, roles)
- **Communication channels** (emails, telephones, other mechanisms)
- **Reference classifications** for addresses, channels, and countries

These entities are **cross-domain**, meaning they may be referenced by Corporate Entities, Suppliers, Sites, Facilities, Assets, and other higher-level business structures.

---

## Referential Integrity Standard

> **Referential Integrity Standard**  
> All entity relationships shown here are **logical only** and are enforced by application services and reporting layers.  
> No physical FOREIGN KEY constraints are implemented at database level, according to the enterprise architecture standard.

The Z-02 family contains:

- **Z-02.01 Address_Master** – canonical physical/postal address
- **Z-02.02 Contact_Master** – the logical contact identity
- **Z-02.03 Contact_Channel_Master** – email, phone, and other communication channels
- **Z-02.04 Address_Type_Master** – controlled vocabulary of address classifications
- **Z-02.05 Channel_Type_Master** – controlled vocabulary of communication channel types
- **Z-02.06 Country_Master** – ISO country reference (dial code, ISO2/ISO3)

The ERD below also illustrates how the Z-02 domain integrates with **Z-01 CorporateEntity**.

---

## 2. Entity–Relationship Diagram (ERD)

```mermaid
erDiagram

    %% =========================================
    %% Z-01 CORPORATE ENTITY
    %% =========================================
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

    %% =========================================
    %% Z-02 ADDRESS & CONTACT ENTITIES
    %% =========================================

    Z-02_01_Address_Master {
        int AddressId PK
        string Address_Line1
        string Address_Line2
        string Address_Town
        string Address_County
        string Postal_Code
        string Country_ISO2
        string Address_Type_Code
        float Longitude
        float Latitude
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

    %% =========================================
    %% Z-02 REFERENCE ENTITIES
    %% =========================================

    Z-02_04_Address_Type_Master {
        string Address_Type_Code PK
        string Address_Type_Name
        string Description
    }

    Z-02_05_Channel_Type_Master {
        string Channel_Type_Code PK
        string Channel_Type_Name
        string Description
    }

    Z-02_06_Country_Master {
        string CountryName
        string Country_Dialing
        string Country2C
        string Country3C
    }

    %% =========================================
    %% LOGICAL RELATIONSHIPS (NO PHYSICAL FKs)
    %% =========================================

    %% Corporate Entity relationships
    Z-01_CorporateEntity }o--|| Z-02_01_Address_Master : "RegisteredAddressId → AddressId"
    Z-01_CorporateEntity }o--|| Z-02_02_Contact_Master : "PrimaryContactId → ContactId"

    %% Contact → Channel
    Z-02_02_Contact_Master ||--o{ Z-02_03_Contact_Channel_Master : "ContactId"

    %% Address classification
    Z-02_04_Address_Type_Master ||--o{ Z-02_01_Address_Master : "Address_Type_Code"

    %% Address → Country reference
    Z-02_06_Country_Master ||--o{ Z-02_01_Address_Master : "Country_ISO2 → Country2C"

    %% Channel classification
    Z-02_05_Channel_Type_Master ||--o{ Z-02_03_Contact_Channel_Master : "Channel_Type_Code"
