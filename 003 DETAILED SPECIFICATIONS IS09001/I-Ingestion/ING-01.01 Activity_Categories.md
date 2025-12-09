# Specification: ING-01.01 Activity_Categories
| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |
|-----------------|-------------|------------|---------------------|------------------|
| **ING-01.01** | 1.0.0 | **DRAFT** | Business Architect | Product Officer |

---

# 1. Purpose

ING-01.05 defines the **canonical activity categories** used in the Normalised Activity layer (ING-01).  
It classifies activity records into consistent types that align with **GHG Protocol scopes and categories**, independent of source system or ingestion method.

This document is a **logical taxonomy only**; structural and mapping rules are defined in **ING-01** and **ING-01.06 Normalisation_Rules**.

---

# 2. Activity Categories

ING‑01 supports activity classes relevant to all GHG Protocol scopes.

## 2.1 Energy Activities
- Electricity (kWh)  
- Gas (kWh or m³)  
- Steam, heating, cooling (if applicable)  
- Location-based vs market-based data attribution  

## 2.2 Travel & Transport
- Flights (distance, cabin class, route)  
- Road travel (vehicle km, fuel type)  
- Rail / public transit  
- Hotel nights  

## 2.3 Fleet Activity
- VIN-linked vehicle attributes  
- Monthly fuel litres / kWh  
- Distance travelled  

## 2.4 Supplier Spend (Scope 3)
From GL + supplier mapping:
- Purchased goods (3.1)  
- Services (3.1)  
- Transportation (3.4)  
- Capital goods (3.2)  

## 2.5 Product- or Process-Based Activities
- Production output  
- Material usage  
- Waste generation  
- Water use  

## 2.6 Manually Uploaded Activities
Including feasibility-test datasets and spreadsheets uploaded directly.

---

# 3. Relationship to ING-01

- The values of `Activity_Type` in **Normalised_Activity** must belong to one of the categories in this document.  
- Category-to-scope mappings (e.g. Scope 1 vs Scope 3.1 vs 3.6) are defined in **ING-02 Emission_Factor_Mapping**.  
- Structural representation (fields, keys, units) is defined in **ING-01 Activity_Normalisation**.

---

# 4. Change History

| Version | Date | Author | Notes |
|---------|------|--------|-------|
| 1.0.0 | 2025-12-10 | Business Architect | Initial extraction of activity categories from ING-01 into standalone spec |
