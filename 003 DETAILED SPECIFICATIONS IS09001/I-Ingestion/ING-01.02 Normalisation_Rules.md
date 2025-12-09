# Specification: ING-01.02 Normalisation_Rules
| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |
|-----------------|-------------|------------|---------------------|------------------|
| **ING-01.02** | 1.0.0 | **DRAFT** | Business Architect | Product Officer |

---

# 1. Purpose

ING-01.06 defines the **normalisation rules** applied to RAW data to produce the **Normalised Activity** outputs described in ING-01.

It covers:
- Structural flattening  
- Unit harmonisation  
- Time handling  

Emission factor mapping and scope attribution are handled in **ING-02** and **ING-03**.

---

# 2. Structural Rules

- All inbound data is flattened into normalised fields.  
- Nested JSON or XML structures must be decomposed into tabular attributes.  
- Units are standardised using the ING‑01 unit catalogue.  
- Source-specific field names are mapped to the canonical Normalised_Activity schema.

---

# 3. Unit Harmonisation

Examples of mandatory conversions:

- `Wh` → `kWh`  
- `MWh` → `kWh`  
- `miles` → `km`  
- `USD`, `EUR`, etc. → standard financial currency mapping via Z‑09 FX structures  
- `L/100km` → normalised representation using `km` and `litres`  

The unit catalogue must:
- Define allowed base units per activity category.  
- Provide conversion factors from common source units.  
- Be versioned and governed under change control.

---

# 4. Time Handling

- All activities must have a valid temporal window (`Start_Date`, `End_Date`).  
- Hourly/30-min data may be aggregated to daily unless finer granularity is explicitly required by ING‑03 or Z‑10 ESG ledgers.  
- Billing periods spanning multiple calendar months may be split or allocated according to configuration (e.g. pro-rata by days).  
- All timestamps are stored in UTC or clearly annotated with timezone for reproducibility.

---

# 5. Relationship to ING-01

- ING‑01.02 provides the **rules**, while ING‑01 defines the **structure** of Normalised_Activity.  
- Quality scoring, enterprise-key mapping, and validation are described at a higher level in ING‑01 and may reference these rules.

---

# 6. Change History

| Version | Date | Author | Notes |
|---------|------|--------|-------|
| 1.0.0 | 2025-12-10 | Business Architect | Initial extraction of normalisation rules from ING-01 into standalone spec |
