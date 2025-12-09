# Specification: ING-00 Sustainability_Data_Ingestion_Framework
| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |
|-----------------|-------------|------------|---------------------|------------------|
| **ING-00** | 1.2.0 | **DRAFT** | Business Architect | Product Officer |

---

# 1. Description & Scope

The **Sustainability Data Ingestion Framework (ING-00)** defines all inbound data sources used to support ESG calculations, emission factor mapping, activity modelling, and supply‑chain sustainability analytics.

This domain overview establishes:
- The classification of data sources  
- The ingestion lifecycle (RAW → NORMALISED → CURATED)  
- The boundaries and responsibilities of downstream specifications (ING‑10, ING‑01, ING‑02, ING‑03)  
- How internal enterprise data (Z‑domains) integrates with externally sourced datasets  

ING‑00 is *not* a technical implementation document.  
It identifies **where detailed behaviour is specified**.

---

# 2. Purpose

ING‑00 ensures SUSTINA ingests data in a **governed, repeatable, and auditable** way.

It defines:
- What sources exist  
- How they are categorised  
- Where the detailed rules are held  
- How ESG‑relevant data transitions through ingestion layers  

Downstream specifications include:

| Spec | Purpose |
|------|---------|
| **ING‑01** | Activity Data Normalisation |
| **ING‑02** | Emission Factor Mapping |
| **ING‑03** | Scope Engine Input Assembly |
| **ING‑10** | API Collection, Manual Uploads & Staging Specification |

---

# 3. Data Source Taxonomy

## 3.1 Tier‑1: Internal Corporate Master Data (Authoritative)

These entities represent the **canonical truth** for suppliers, products, finance, and organisational structure.

- **Supplier Master (Z‑04.01)**  
- **Supplier Activity Map (Z‑04.02)**  
- **Product Category / Lifecycle / ESG Maps (Z‑05.xx)**  
- **Finance Entities, Chart of Accounts, General Ledger, Cost Centres (Z‑09.xx)**  

Tier‑1 data:
- Is never overwritten by external feeds  
- Forms the reference backbone for mapping activity and spend  
- Drives attribution into the ESG Ledger  

## 3.2 Tier‑2: Internal Operational Systems (High Trust)

Examples:
- Utility billing feeds  
- Travel & expense systems  
- Fleet fuel/charging systems  
- Supplier invoices  
- Financial GL postings (spend‑based Scope 3 inputs)  

Tier‑2 data enters via **ING‑01 Activity Normalisation**.

## 3.3 Tier‑3: External APIs & Industry Datasets (Augmentation)

Examples:
- National Grid carbon intensity  
- NHTSA vehicle metadata  
- ElectricityMaps grid intensity  
- Climatiq emission factors  
- DEFRA/EPA factor spreadsheets (if programmatically retrieved)  

Tier‑3 datasets:
- Are stored **as‑is** in RAW_ZONE  
- Provide enrichment but do not replace enterprise master data  

## 3.4 Tier‑3: Direct / Manual Uploads

Manual uploads cover:
- CSV/JSON files generated during feasibility testing  
- Real corporate documents (utility statements, travel summaries)  
- Government emission factor spreadsheets (DEFRA, EPA, IEA)  
- Output from prototype tools (e.g., Python collector runs, data‑collection markdown summaries)  

Manual uploads:
- Are versioned, timestamped, and validated  
- Must pass through **ING‑01 Normalisation**  
- Are treated with the same controls as external sources  

---

# 4. Ingestion Architecture

```
        Tier‑1 → RAW_ZONE ┐
        Tier‑2 → RAW_ZONE ├→ ING‑01 → NORMALISED_ZONE → ING‑03 → CURATED_ZONE → ESG Ledger
        Tier‑3 → RAW_ZONE ┘
```

### 4.1 RAW Zone
- Immutable copies of uploads, system extracts, and API payloads  
- No structural changes permitted  

### 4.2 Normalised Zone
Defined in **ING‑01**.  
Contains harmonised, typed, and enterprise‑keyed activity data.

### 4.3 Curated Zone
Defined in **ING‑03**.  
Represents fully prepared inputs to emissions calculations.

---

# 5. Key Principles

### 5.1 Enterprise Keys Dominate
All data is mapped to core Z‑domain keys:
- Supplier_Id  
- Product_Id  
- Accounting_Entity_Code  
- Account_Code  
- Cost_Centre_Code  

### 5.2 External Data Never Overrides Master Data
External APIs and manual uploads inform or enrich but do not define master records.

### 5.3 All Ingested Data Must Be Traceable
A complete lineage chain is required:

```
Source → RAW → NORMALISED → CURATED → Factor Mapping → Scope Calculation → ESG Ledger
```

### 5.4 Ingestion Logic Must Be Implementation‑Agnostic
ING‑00 sets governance, not Python/SQL details.  
Technical specifics reside in ING‑10 and ENG‑layer implementation docs.

---

# 6. Detailed Behaviour is Held In:

| Behaviour | Defined In |
|-----------|------------|
| API polling, manual upload rules, staging tables | **ING‑10** |
| Activity structure & harmonisation | **ING‑01** |
| Emission factor resolution rules | **ING‑02** |
| Scope attribution and input assembly | **ING‑03** |
| ESG Ledger representation | **Z‑10.xx** |

---

# 7. Governance & Compliance

- **ISO‑9001**: repeatable, version‑controlled ingestion  
- **GHG Protocol**: transparent boundary, source provenance, auditability  
- **GDPR**: minimisation, pseudonymisation where applicable  
- **Internal audit**: immutable RAW_ZONE, controlled CURATED_ZONE  

---

# 8. Change History

| Version | Date | Author | Notes |
|---------|------|--------|-------|
| 1.0.0 | 2025‑12‑08 | Business Architect | Initial draft |

