# Workflow Reference: M-003 (Entity Management Master)

**Description:**
This is the master workflow for the **Manage Entities** module. Access is strictly controlled; the flow begins with a dependency on **M-002 (Authentication)**. Only users who have successfully passed the security gateway can access the Authorized Navbar.

Upon entry, the system immediately calls the API to **Load Existing Entities** (Read Operation) to populate the dashboard. From there, the user acts as a "Dispatcher," selecting specific administrative tasks.

### Process Flow Diagram
```mermaid
flowchart LR
    %% PRE-REQUISITE (Used Subroutine Shape for consistency)
    START((Start)) --> AUTH[["REF: M-002<br/>Auth & Security"]]
    
    %% UI LAYER
    AUTH --> NAV["Navbar (Authorized)"]
    NAV --> MENU{"User Selection"}
    
    MENU -- "Manage Entities" --> DASH["Entity Dashboard"]
    
    %% INITIAL DATA LOAD
    DASH --> API_LOAD["API: Load Existing Entities"]
    API_LOAD --> DB[("Datastore")]
    DB -.-> DASH

    %% ACTION ROUTING
    DASH --> ACTION{"Select Action"}

    %% ROUTING TO SUBROUTINES
    ACTION -- "Create New" --> SUB_CREATE[["REF: ENT-01<br/>Create Entity"]]
    ACTION -- "Org & Teams" --> SUB_TEAM[["REF: ENT-02<br/>Manage Org Chart"]]
    ACTION -- "Cost Centers" --> SUB_COST[["REF: ENT-03<br/>Manage Cost Centers"]]
    ACTION -- "Tax Config" --> SUB_TAX[["REF: ENT-04<br/>Manage Tax"]]
    ACTION -- "Documents" --> SUB_DOCS[["REF: ENT-05<br/>Manage Documents"]]

    %% CONSOLIDATION
    SUB_CREATE & SUB_TEAM & SUB_COST & SUB_TAX & SUB_DOCS --> REFRESH("Refresh Dashboard")
    REFRESH --> DASH
