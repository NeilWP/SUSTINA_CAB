## 💰 The General Ledger (GL) and NACE Codes in Carbon Accounting

The **General Ledger (GL)** and **NACE codes** are fundamental to calculating Scope 3, Category 1 (Purchased Goods and Services)—often the largest part of a company's footprint.


' ' 'mermaid
graph LR
    %% Define the SME as the central target
    SME[SME Carbon Footprint Calculation]

    %% Input Data Sources (Left Side)
    subgraph Data Inputs
        A[General Ledger (Spend Data)] --> |Maps to| B(NACE Code Factor);
        C[Activity Data (e.g., kWh, Miles)] --> D(Custom/Specific Factor);
        E[Supplier-Specific Data] --> D;
    end

    %% Scope Calculation Methodologies
    subgraph Calculation Scopes
        S1[Scope 1: Direct Emissions]
        S2[Scope 2: Purchased Energy]
        S3[Scope 3: Value Chain Emissions]
    end

    %% Core Data Flow from SME Perspective
    A --> |Initial S3 Estimate (Spend-Based)| S3;
    B --> |Refines S3 Estimate| S3;
    C --> |Calculate S1, S3 (Activity-Based)| S1;
    D --> |Most Accurate S3 Input| S3;

    %% Scope 2 Dual Reporting Detail
    S2 --> S2A[Location-Based (Grid Average)]
    S2 --> S2B[Market-Based (EACs/PPAs)]

    %% Final Output
    SME --> S1;
    SME --> S2;
    SME --> S3;
    S1 & S2 & S3 --> Report[GHG Protocol Report for Stakeholders];

    %% Style the SME Node to highlight the target
    style SME fill:#2E8B57,stroke:#3C3C3C,stroke-width:4px,color:#FFFFFF
    style Report fill:#ADD8E6,stroke:#3C3C3C,stroke-width:2px,color:#333333
    ' ' '
---

### 1. The Role of the General Ledger (GL)

The GL serves as the **primary data source** for the **Spend-Based Method** of emission calculation.

* **Source of Spend Data:** The GL tracks all company expenditures, organized by **G/L Account Codes** (e.g., "Raw Materials," "Utilities," "Consulting Fees").
* **Spend-Based Formula:** For an initial screening estimate, the amount spent in a category is multiplied by a corresponding emission factor:
    $$\text{Emissions} = \text{Spend} \times \text{Spend-Based Emission Factor}$$
* **Challenge:** Financial classifications (G/L codes) are often too broad (e.g., "Other Expenses") to accurately map to precise environmental impacts.

---

### 2. Industry Classification: NACE Codes

**NACE** (Nomenclature of Economic Activities in the European Community) is the European standard for classifying business activities. It provides the necessary **industry granularity** to apply relevant emission factors.

* **Linking Spend to Emissions:** Emission factor databases (often derived from **EEIO** or Input-Output models) are organized by economic sectors, such as NACE codes.
* **The Mapping Process:** The core task for carbon accounting software is to map the company's internal **G/L Account Code** (financial) to an external, public **NACE Code** (industry/environmental).
    * *Example:* G/L Account "6020: IT Equipment" $\rightarrow$ NACE Code **26.20** ("Manufacture of computers...").
* **EU Relevance:** NACE is the standard classification used across key EU sustainability frameworks, including the **CSRD** and **EU Taxonomy**.

### Other Global Classification Standards

To handle international data and different types of spend, platforms use other classification systems:

| Classification System | Geographic Focus | Use in Carbon Accounting |
| :--- | :--- | :--- |
| **NACE Rev. 2** | **European Union** | Primary basis for EU-focused emission factors and regulations. |
| **NAICS** | **North America** (US, Canada, Mexico) | Used to link spend data to North American emission factors (e.g., US EPA). |
| **UNSPSC** | **Global** (Product/Service Code) | Classifies **products and services** (not companies), offering higher granularity for procurement data. |

---

### 3. The Mapping Challenge and Iterative Improvement

The biggest hurdle for an SME solution is achieving mapping accuracy and enabling improvement over time:

1.  **Initial Mapping:** Automatically or manually assigning a NACE code to every significant G/L line item.
2.  **Accuracy:** Ensuring the map reflects the **actual activity** (e.g., mapping a purchase from a *retailer* back to the *manufacturer's* NACE code for better accuracy).
3.  **Transition:** Using the GL/NACE map for the initial screening, but guiding the SME to replace these broad **Spend-Based** estimates with more accurate **Activity Data** (e.g., actual flight distance) or **Supplier-Specific Data** over time.

This mapping process is the foundational link that converts financial records into actionable environmental disclosures.
