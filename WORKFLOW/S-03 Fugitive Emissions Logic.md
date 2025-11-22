# Workflow Reference: S-03 (Fugitive Emissions Logic)

**Description:**
This workflow calculates **Scope 1 Fugitive Emissions**, primarily from leaks in refrigeration and air conditioning equipment. This includes HVAC systems, chillers, industrial cooling, and fire suppression systems.

Unlike combustion, this calculation relies on **Global Warming Potential (GWP)** values. A single kilogram of refrigerant gas (e.g., R404A) can have the same warming impact as 3,922 kg of CO2, making accurate reporting critical.

### Key Logical Steps

1.  **Equipment Identification:**
    Links the emission to a specific asset (e.g., "Server Room AC Unit 1"). This builds an asset register required for audit trails.

2.  **Gas Identification (GWP Lookup):**
    The user must select the specific blend (e.g., R410a, R32, R134a).
    * *System Action:* The system calls Subroutine `S-03-A` to retrieve the specific IPCC GWP factor for that gas.

3.  **Activity Logic (The "Leak Check"):**
    The system distinguishes between:
    * **New Installation:** The gas added is "Stock," not an emission (unless leaks occur during install).
    * **Maintenance / Top-up:** Gas added to replace what was lost. **This is the emission.**
    * **Disposal:** Gas recovered vs. Gas lost at end-of-life.

4.  **Calculation:**
    Formula: $Mass (kg) \times GWP = tCO2e$.

### Process Flow Diagram

```mermaid
flowchart LR
    %% INPUT PHASE
    START(["Start S-03<br/>Fugitive Logic"]) --> ASSET{"Select Asset/Equipment"}
    
    ASSET -- "HVAC / AC Unit" --> GAS_SEL
    ASSET -- "Chiller / Fridge" --> GAS_SEL
    ASSET -- "Fire Suppression" --> GAS_SEL

    %% GAS SELECTION
    GAS_SEL{"Select Gas Type"}
    
    GAS_SEL -- "HFCs (e.g. R410a, R134a)" --> SUB_GWP
    GAS_SEL -- "Blends (e.g. R404A)" --> SUB_GWP
    GAS_SEL -- "Natural (e.g. Ammonia)" --> SUB_GWP

    %% SUBROUTINE: GWP LOOKUP
    SUB_GWP[["REF: S-03-A<br/>Lookup GWP Factor"]] --> ACTIVITY{"Maintenance Activity"}

    %% ACTIVITY LOGIC
    ACTIVITY -- "New Installation<br/>(Initial Charge)" --> NO_EMIT[("Log as Inventory<br/>(Zero Emission)")]
    
    ACTIVITY -- "Maintenance / Repair<br/>(Top-up)" --> LEAK["Enter Qty Added (Kg)"]
    
    ACTIVITY -- "End of Life<br/>(Disposal)" --> DISP_CALC{"Recovery Data?"}
    
    DISP_CALC -- "Gas Recovered" --> REC_LOG[("Log Recovery")]
    DISP_CALC -- "Gas Lost / Vented" --> LEAK

    %% CALCULATION
    LEAK --> CALC("Calculate tCO2e<br/>(Kg * GWP)")
    
    CALC & NO_EMIT & REC_LOG --> EVIDENCE[/"Upload Service Log<br/>or F-Gas Record"/]
    
    EVIDENCE --> SAVE[("Save to Database")]
```
