# DAT-02-1 Supplier ESG Data Model (Schema) 

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |
| :--- | :--- | :--- | :--- | :--- |
| DAT-02-1 | 1.1.0 | **DRAFT** | Business Architect | Product Officer |

## 1. Description & Scope 
This defines the SQL DDL (Data Definition Language) script for Supplier ESG entities required by DAT-02 (Scope 3 Ledger).

* This script utilizes Soft Constraints.
* A **Database Trigger** guarantees that no matter what script or API inserts the data, data integrity rules are always enforced by the databasee. Using a trigger allows the application to retain control over all aspects of error handinling and validation.
* **No Foreign Keys**: Tables are decoupled. The Application Layer (PROC-02/PROC-08) is responsible for ensuring a supplier_id exists before inserting a transaction.
* **Indexing**: Since we removed Foreign Keys, Indices on the joining columns ensure dashboard queries prefrom reasonably.
* **Data Types**: The type **DECIMAL** is used instead of **FLOAT**. This is critical for Carbon Accounting to prevent IEEE-754 floating-point errors (e.g., 0.1 + 0.2 = 0.300000004).


## 2. Build Model
The scripts "pre-execution" building the database are to be found in the databse assigned to the CAB or Client instance.
Temporarilly they are placed here for review.

### 2.a Create Entities

```sql
/* * ==============================================================
 * SCOPE 3 DATAMODEL (Software-Managed Referential Integrity)
 * Dialect: PostgreSQL / Standard SQL
 * ==============================================================
 */

-- 1. Clean up existing tables (Dev Mode)
DROP TABLE IF EXISTS fct_scope3_ledger;
DROP TABLE IF EXISTS dim_suppliers;
DROP TABLE IF EXISTS ref_emission_factors;

/* * TABLE: dim_suppliers
 * Context: The "Who". Stores profile and risk data.
 */
CREATE TABLE dim_suppliers (
    supplier_id         VARCHAR(64) NOT NULL PRIMARY KEY,  -- UUID or ERP ID
    supplier_name       VARCHAR(255) NOT NULL,
    country_iso         CHAR(2),                           -- ISO 3166-1 alpha-2
    industry_sector     VARCHAR(100),                      -- e.g. "Textiles"
    gl_code_default     VARCHAR(50),                       -- For auto-mapping
    
    -- ESG Risk Metrics (0.00 - 100.00)
    social_risk_score   DECIMAL(5, 2) DEFAULT 0.00,
    governance_score    DECIMAL(5, 2) DEFAULT 0.00,
    
    -- Audit Columns
    created_at          TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at          TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

/* * TABLE: ref_emission_factors
 * Context: The "Math". Lookup table for EEIO factors.
 */
CREATE TABLE ref_emission_factors (
    sector_code         VARCHAR(50) NOT NULL PRIMARY KEY,  -- e.g. "EEIO-511"
    description         TEXT,
    
    -- The Multiplier (High precision required for small factors)
    -- e.g. 0.004532 kgCO2e per USD
    kg_co2e_per_usd     DECIMAL(12, 6) NOT NULL,
    
    source_library      VARCHAR(100),                      -- e.g. "USEEIO v2.0"
    year_valid          INT
);

/* * TABLE: fct_scope3_ledger
 * Context: The "Impact". Transactional history.
 * CONSTRAINT NOTE: No FOREIGN KEY on supplier_id (App Managed)
 */
CREATE TABLE fct_scope3_ledger (
    transaction_id      VARCHAR(64) NOT NULL PRIMARY KEY,
    batch_id            VARCHAR(64),                       -- Links to Upload Batch
    
    -- Temporal
    transaction_date    DATE NOT NULL,
    reporting_period    VARCHAR(20),                       -- e.g. "2024-Q3"
    
    -- Linkage (Soft Links)
    supplier_id         VARCHAR(64) NOT NULL,
    sector_code         VARCHAR(50),
    
    -- Financials (19 digits total, 4 decimal places)
    spend_amount_usd    DECIMAL(19, 4) NOT NULL,
    
    -- Carbon Impact
    calculated_co2e_kg  DECIMAL(19, 4) NOT NULL,
    
    -- Methodology Tracking (Critical for Audit)
    calculation_method  VARCHAR(50) NOT NULL,              -- 'SPEND_BASED', 'SUPPLIER_SPECIFIC'
    data_quality_score  VARCHAR(20) DEFAULT 'LOW',         -- 'HIGH', 'MED', 'LOW'
    
    created_at          TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

/* * ==============================================================
 * PERFORMANCE INDEXING
 * Since we lack Foreign Keys, we must index join columns manually
 * to ensure fast aggregation for dashboards.
 * ==============================================================
 */

-- Speed up "Impact by Supplier" queries
CREATE INDEX idx_ledger_supplier ON fct_scope3_ledger(supplier_id);

-- Speed up "Impact by Time" queries
CREATE INDEX idx_ledger_date ON fct_scope3_ledger(transaction_date);

-- Speed up "Impact by Batch" (for deleting bad uploads)
CREATE INDEX idx_ledger_batch ON fct_scope3_ledger(batch_id);

-- Speed up Sector Lookups
CREATE INDEX idx_suppliers_sector ON dim_suppliers(industry_sector);
```
---

### 2.b Create Trigger

```sql
/* * ==============================================================
 * DB TRIGGER: SCOPE 3 GUARDIAN
 * Logic: Enforces Logic & Math at the Engine Level
 * ==============================================================
 */

-- 1. Create the Logic Function
CREATE OR REPLACE FUNCTION fn_scope3_guardian()
RETURNS TRIGGER AS $$
DECLARE
    v_factor DECIMAL(12, 6);
BEGIN
    -- ==========================================
    -- A. INTEGRITY CHECKS (The "Bouncer")
    -- ==========================================

    -- Rule 1: Time Travel Prevention
    IF NEW.transaction_date > CURRENT_DATE THEN
        RAISE EXCEPTION 'Data Integrity Violation: Transaction Date % is in the future.', NEW.transaction_date
        USING HINT = 'Check client system clock or date parsing logic.';
    END IF;

    -- Rule 2: Negative Spend (Allow only if flagged as 'Correction')
    -- If spend is negative, we assume it's a refund, but we warn/block if not labeled.
    IF NEW.spend_amount_usd < 0 AND NEW.calculation_method != 'CORRECTION' THEN
         RAISE WARNING 'Negative spend detected without CORRECTION flag. Proceeding, but audit flag raised.';
    END IF;

    -- ==========================================
    -- B. AUTO-CALCULATION (The "Calculator")
    -- ==========================================
    
    -- If the App sent the Spend, but NOT the Carbon, we calculate it here.
    IF NEW.calculated_co2e_kg IS NULL OR NEW.calculated_co2e_kg = 0 THEN
        
        -- 1. Lookup the factor for this sector
        SELECT kg_co2e_per_usd INTO v_factor
        FROM ref_emission_factors
        WHERE sector_code = NEW.sector_code;

        -- 2. Handle missing factors (Soft Constraint)
        IF v_factor IS NULL THEN
            -- Fallback: If no factor found, create a "Unmapped" entry or Raise Error
            -- Here we Raise Error to force the team to fix the factor table
            RAISE EXCEPTION 'Missing Emission Factor for Sector: %', NEW.sector_code;
        END IF;

        -- 3. Perform the Math
        NEW.calculated_co2e_kg := ROUND(NEW.spend_amount_usd * v_factor, 4);
    
    END IF;

    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- 2. Attach the Trigger to the Table
DROP TRIGGER IF EXISTS trg_scope3_before_insert ON fct_scope3_ledger;

CREATE TRIGGER trg_scope3_before_insert
BEFORE INSERT OR UPDATE ON fct_scope3_ledger
FOR EACH ROW
EXECUTE FUNCTION fn_scope3_guardian();
```
---

### 2.b Create Stored procedures

* 1. Supplier Upsert (sp_upsert_supplier)
Context: When ingestion runs, we might see a supplier again. We want to update their Risk Score if it changed, but keep their ID constant.

```sql
CREATE OR REPLACE PROCEDURE sp_upsert_supplier(
    p_supplier_id       VARCHAR,
    p_name              VARCHAR,
    p_country           CHAR,
    p_sector            VARCHAR,
    p_social_score      DECIMAL,
    p_gov_score         DECIMAL
)
LANGUAGE plpgsql
AS $$
BEGIN
    INSERT INTO dim_suppliers (
        supplier_id, supplier_name, country_iso, industry_sector, 
        social_risk_score, governance_score, updated_at
    )
    VALUES (
        p_supplier_id, p_name, p_country, p_sector, 
        p_social_score, p_gov_score, CURRENT_TIMESTAMP
    )
    ON CONFLICT (supplier_id) 
    DO UPDATE SET
        supplier_name     = EXCLUDED.supplier_name,
        social_risk_score = EXCLUDED.social_risk_score, -- Risk scores might change over time
        governance_score  = EXCLUDED.governance_score,
        updated_at        = CURRENT_TIMESTAMP;
END;
$$;
```
---

* 2. Transaction Upsert (sp_upsert_transaction)
Context: This is the workhorse. It handles the "Soft Constraint" check (verifying the supplier exists) and refreshes the Carbon Math via the Trigger.

```sql
CREATE OR REPLACE PROCEDURE sp_upsert_transaction(
    p_transaction_id    VARCHAR,
    p_batch_id          VARCHAR,
    p_date              DATE,
    p_supplier_id       VARCHAR,
    p_sector            VARCHAR,
    p_spend             DECIMAL,
    p_method            VARCHAR
)
LANGUAGE plpgsql
AS $$
DECLARE
    v_supplier_exists BOOLEAN;
BEGIN
    -- =========================================================
    -- 1. SOFT CONSTRAINT CHECK (Application Integrity)
    -- =========================================================
    -- Since we removed the Foreign Key, we MUST check this here.
    SELECT EXISTS(SELECT 1 FROM dim_suppliers WHERE supplier_id = p_supplier_id)
    INTO v_supplier_exists;

    IF NOT v_supplier_exists THEN
        RAISE EXCEPTION 'Integrity Error: Cannot insert transaction % because Supplier % does not exist.', 
        p_transaction_id, p_supplier_id;
    END IF;

    -- =========================================================
    -- 2. THE UPSERT (Insert or Update)
    -- =========================================================
    INSERT INTO fct_scope3_ledger (
        transaction_id, batch_id, transaction_date, 
        supplier_id, sector_code, spend_amount_usd, 
        calculation_method, calculated_co2e_kg, -- Pass NULL to trigger auto-calc
        created_at
    )
    VALUES (
        p_transaction_id, p_batch_id, p_date, 
        p_supplier_id, p_sector, p_spend, 
        p_method, NULL, -- We force NULL so the Trigger runs the math
        CURRENT_TIMESTAMP
    )
    ON CONFLICT (transaction_id) 
    DO UPDATE SET
        spend_amount_usd   = EXCLUDED.spend_amount_usd,
        sector_code        = EXCLUDED.sector_code,
        calculation_method = EXCLUDED.calculation_method,
        -- Important: We reset Carbon to NULL on update so the Trigger re-calculates it
        -- based on the new Spend amount.
        calculated_co2e_kg = NULL; 
END;
$$;
```
---
