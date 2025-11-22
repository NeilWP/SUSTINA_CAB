# Workflow Reference: T-001 (Transport & Logistics)

**Description:**
This subroutine handles the complex logic for Mobile Combustion (Scope 1) and Mobile Business Travel/Upstream Transport (Scope 3). It segregates emissions based on the **Transport Mode** and the **Asset Ownership** model.

### Key Logical Steps

1.  **Mode Selection:**
    Splits logic between Road, Air, Rail, and Sea.

2.  **Scope Determination:**
    * **Scope 1:** Applied only when the vehicle is **Company Owned/Leased**.
    * **Scope 3:** Applied to Personal Cars (Grey Fleet), Rentals, Flights, Trains, and 3rd Party Logistics (Couriers).

3.  **Sub-Routine Call (T-002):**
    For Road transport using **Distance**, the system calls a sub-routine (`T-002`) to handle precise license plate lookups and vehicle databases.

4.  **Cabin Class (Air):**
    Applies "Radiative Forcing" multipliers based on seat class (Economy vs. Business/First).

---

### Process Flow Diagram
```mermaid
---
config:
  layout: LR
---
flowchart LR
    START(["Start Routine: T-001<br/>Transport Logic"]) --> TR_TYPE{"Select Transport Mode"}

    %% ==========================================
    %% BRANCH 1: ROAD (Cars, Trucks, Vans)
    %% ==========================================
    TR_TYPE -- "Road (Car/Truck)" --> RD_OWN{"Asset Ownership"}
    
    RD_OWN -- "Company Fleet" --> RD_S1["SCOPE 1<br/>Direct Mobile Combustion"]
    RD_OWN -- "Personal / Rental" --> RD_S3["SCOPE 3 (Cat 6)<br/>Business Travel"]
    RD_OWN -- "3rd Party Courier" --> RD_LOG["SCOPE 3 (Cat 4)<br/>Upstream Transport"]
    
    RD_S1 & RD_S3 & RD_LOG --> RD_METHOD{"Calculation Method"}
    
    %% SUBROUTINES
    RD_METHOD -- "Fuel Receipts" --> SUB_FUEL[["REF: T-003<br/>Lookup Fuel Factor"]]
    RD_METHOD -- "Distance Log" --> SUB_PRECISION[["REF: T-002<br/>Vehicle Precision Logic"]]

    %% ==========================================
    %% BRANCH 2: AIR (Split: Pax vs Freight)
    %% ==========================================
    TR_TYPE -- "Air Travel" --> AR_CAT{"Category"}

    %% 2a. PASSENGER
    AR_CAT -- "Passenger" --> AR_TYPE{"Flight Type"}
    AR_TYPE -- "Domestic/Short" --> AR_CLASS{"Cabin Class"}
    AR_TYPE -- "Long Haul" --> AR_CLASS
    
    AR_CLASS -- "Economy" --> SUB_AIR_E[["REF: T-004-A<br/>Lookup Economy Factor"]]
    AR_CLASS -- "Business / First" --> SUB_AIR_P[["REF: T-004-B<br/>Lookup Premium Factor"]]
    
    SUB_AIR_E & SUB_AIR_P --> AR_DIST["Enter Distance (pkm)"]

    %% 2b. AIR FREIGHT
    AR_CAT -- "Freight / Cargo" --> AR_FRT{"Logistics Type"}
    AR_FRT -- "Bellyhold (Pax Plane)" --> SUB_AIR_F[["REF: T-004-C<br/>Lookup Freight Factor"]]
    AR_FRT -- "Dedicated Freighter" --> SUB_AIR_F
    
    SUB_AIR_F --> AR_WGT["Enter Weight (Tonnes)"]
    AR_WGT --> AR_DIST_F["Enter Distance (km)"]

    %% ==========================================
    %% BRANCH 3: RAIL (Trains)
    %% ==========================================
    TR_TYPE -- "Rail / Train" --> RL_TYPE{"Rail Network"}
    
    RL_TYPE -- "National Rail" --> SUB_RL_NAT[["REF: T-005-A<br/>Lookup National Avg"]]
    RL_TYPE -- "International" --> SUB_RL_INT[["REF: T-005-B<br/>Lookup Int'l Factor"]]
    RL_TYPE -- "Urban / Subway" --> SUB_RL_URB[["REF: T-005-C<br/>Lookup Urban Factor"]]
    
    SUB_RL_NAT & SUB_RL_INT & SUB_RL_URB --> RL_DIST["Enter Distance (Km)"]

    %% ==========================================
    %% BRANCH 4: SEA (Ferries & Freight)
    %% ==========================================
    TR_TYPE -- "Sea / Water" --> SE_TYPE{"Vessel Purpose"}
    
    SE_TYPE -- "Passenger (Foot)" --> SUB_SE_PAX[["REF: T-006-A<br/>Lookup Passenger Factor"]]
    SE_TYPE -- "Vehicle (Car on Ferry)" --> SUB_SE_VEH[["REF: T-006-B<br/>Lookup Vehicle Factor"]]
    SE_TYPE -- "Freight (Shipping)" --> SUB_SE_FRT[["REF: T-006-C<br/>Lookup Freight Factor"]]
    
    SUB_SE_PAX & SUB_SE_VEH & SUB_SE_FRT --> SE_DIST["Enter Distance (Km)"]

    %% ==========================================
    %% CONSOLIDATION
    %% ==========================================
    SUB_FUEL & SUB_PRECISION & AR_DIST & AR_DIST_F & RL_DIST & SE_DIST --> CALC_SUM("Sum Total tCO2e")
    
    CALC_SUM --> RETURN(["End Routine<br/>Return to Master Flow"])
```
