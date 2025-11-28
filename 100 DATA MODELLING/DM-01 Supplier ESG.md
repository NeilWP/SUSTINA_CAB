# Supplier ESG Data Model (Schema) 

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |
| :--- | :--- | :--- | :--- | :--- |
| DM- | 1.1.0 | **DRAFT** | Business Architect | Product Officer |

## 1. Description & Scope 
Supplier ESG Impact falls under (Scope 3), we need a Data Model that links Financial Spend (Ingestion) to Non-Financial Impact (Carbon & Risk).
Three tables to tell the full story for an auditor:
* **dim_suppliers**: Who are they and what is their inherent ESG risk?
* **ref_emission_factors**: The lookup table (EEIO) converting Money to Carbon.
* **fct_scope3_ledger**: The transactional table calculating the actual impact.

The SQL DDL (Data Definition Language) script for your DAT-02 (Scope 3 Ledger).

### Data Entity Relationship Diagram

```mermaid
erDiagram
    dim_suppliers ||--o{ fct_scope3_ledger : "incurs"
    ref_emission_factors ||--o{ fct_scope3_ledger : "calculates"

    dim_suppliers {
        string supplier_id PK
        string name
        string country
        string industry_sector
        float social_risk_score "0-100 (Child Labor/Slavery)"
        float governance_score "0-100 (Corruption)"
    }

    ref_emission_factors {
        string sector_code PK
        string description
        float kg_co2e_per_usd "The Multiplier"
    }

    fct_scope3_ledger {
        string transaction_id PK
        date transaction_date
        float spend_amount_usd
        float calculated_co2e_kg
        string calculation_method "SPEND_BASED | HYBRID"
        string data_quality_score "HIGH | MED | LOW"
    }
```
---
