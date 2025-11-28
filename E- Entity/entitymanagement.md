
```mermaid
flowchart LR
    %% ==========================================
    %% SYSTEM ENTRY & SECURITY LAYER
    %% ==========================================
    START((Start)) --> SEC_01[["ID: SEC-01<br/>Auth & SSO Logic<br/>(OAuth/SAML)"]]
    
    SEC_01 --> UI_01["ID: UI-01<br/>Navbar Component<br/>(Role-Based View)"]
    
    UI_01 --> UI_02{"ID: UI-02<br/>Action Router"}

    %% ==========================================
    %% ENTITY MANAGEMENT (CORE)
    %% ==========================================
    UI_02 -- "Manage Entities" --> UI_03["ID: UI-03<br/>Entity Dashboard"]
    
    UI_03 --> API_01["ID: API-01<br/>GET /entities"]
    API_01 --> DAT_01[("ID: DAT-01<br/>Entity Store<br/>(PostgreSQL)")]
    DAT_01 -.-> UI_03

    %% ==========================================
    %% ACTION HUB
    %% ==========================================
    UI_03 --> UI_04{"ID: UI-04<br/>Action Select"}

    %% ADMIN BRANCH
    UI_04 -- "Config Org" --> ENT_01[["ID: ENT-01<br/>Org Chart Module"]]
    UI_04 -- "Config Tax" --> ENT_02[["ID: ENT-02<br/>Tax Rules Engine"]]
    
    %% ==========================================
    %% THE PROCUREMENT AUDIT BRANCH (Detailed)
    %% ==========================================
    UI_04 -- "Scope 3 Ingest" --> PROC_01[/"ID: PROC-01<br/>File Uploader<br/>(Parser Service)"/]

    PROC_01 --> PROC_02{"ID: PROC-02<br/>Data Validator"}

    %% PROC AI MAPPING
    PROC_02 -- "Unmapped" --> PROC_03[["ID: PROC-03<br/>ML Classifier<br/>(Vendor -> Category)"]]
    
    %% PROC CALCULATION ENGINES
    PROC_02 -- "Clean" --> PROC_04{"ID: PROC-04<br/>Method Router"}

    PROC_04 -- "Spend Data" --> PROC_05[["ID: PROC-05<br/>EEIO Spend Engine<br/>(Inflation + Factor)"]]
    PROC_04 -- "Activity Data" --> PROC_06[["ID: PROC-06<br/>LCA Factor Engine<br/>(Mass * CO2e)"]]
    PROC_04 -- "Supplier Data" --> PROC_07[["ID: PROC-07<br/>PCF Ingestion<br/>(Primary Data)"]]

    %% CONSOLIDATION
    PROC_03 & PROC_05 & PROC_06 & PROC_07 --> PROC_08("ID: PROC-08<br/>Scope 3 Consolidator")
    
    PROC_08 --> DAT_02[("ID: DAT-02<br/>Scope 3 Ledger")]
    ```