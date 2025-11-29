# Specification: ENT-01 (Create Entity)

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** |**Approved On** |
| :--- | :--- | :--- | :--- | :--- |:--- |
| P- | 1.0.0 | **DRAFT** | Business Architect | Product Officer | | 

## 1. Description & Scope
This subroutine handles the detailed onboarding of a new corporate entity. It enforces strict data quality standards (Address & Phone formatting) and legal compliance via a mandatory **User Attestation**.

The "Submit" function is strictly gated: it remains disabled on the frontend until all screen-level validations pass and the user has affirmatively declared they are authorized to create this record.

### Key Logical Steps

1.  **Data Input & Classification:**
    The user provides the core identity data.
    * **Mandatory:** Entity Name, Full Address (Postal + Country), Entity Type (Sole Trader, Partnership, LLC, Public, Other), Prime Contact (Intl. Format), Tax Authority Name, "Part of Structure" Flag.
    * **Optional:** Tax ID / Reference.

2.  **Attestation (The Legal Gate):**
    The user must check a declaration box: *"I affirm I am authorized to create this entity."*
    * **System Action:** This action captures the User's GUID, Name, and the current Timestamp to create a permanent audit trail.
    * **UI Behavior:** The **Submit** button remains inactive until this check is passed.

3.  **API Chain Validations (Backend):**
    Upon submission, the API performs two critical checks:
    * **Address Verification:** Validates the postal data against global address services.
    * **Uniqueness Check:** Ensures this entity does not already exist to prevent duplicates.

4.  **Success State:**
    If successful, the system generates a unique **Entity Reference ID** and returns it. This unlocks the subsequent sub-routines (Teams, Documents, etc.) for this new entity.

### Process Flow Diagram

```mermaid
flowchart LR
    %% UI LAYER: INPUTS
    START((Start)) --> FORM["<b>MANDATORY INPUTS</b><br/>• Entity Name<br/>• Full Address & Country<br/>• Entity Type (LLC, etc.)<br/>• Contact # (Intl. Format)<br/>• Local Tax Authority<br/>• 'Part of Structure' Flag"]
    
    FORM --> OPT["<b>OPTIONAL</b><br/>• Tax ID / Ref"]
    
    OPT --> ATTEST{"<b>ATTESTATION</b><br/>User Declares Authority?"}
    
    %% UI LOGIC: SUBMIT BUTTON
    ATTEST -- No --> DISABLED(["`Submit Button<br/>DISABLED`"])
    DISABLED -.-> ATTEST
    
    ATTEST -- Yes --> PAYLOAD["Package Data:<br/>+ User GUID<br/>+ Timestamp"]
    
    PAYLOAD --> SUBMIT(["`Submit Button<br/>ENABLED`"])
    
    %% BACKEND LAYER: API CHAIN
    SUBMIT --> API["API Chain<br/>(Gateway)"]
    
    API --> VAL_ADDR{"1. Validate<br/>Address?"}
    
    VAL_ADDR -- Invalid --> ERR_ADDR["Error: Address Not Found"]
    ERR_ADDR --> FORM
    
    VAL_ADDR -- Valid --> VAL_UNIQ{"2. Entity<br/>Unique?"}
    
    VAL_UNIQ -- Duplicate --> ERR_DUP["Error: Entity Exists"]
    ERR_DUP --> FORM
    
    %% SUCCESS & PERSISTENCE
    VAL_UNIQ -- Unique --> DB[("Datastore:<br/>Insert Record & Audit Log")]
    
    DB --> SUCCESS[["Return: Entity Ref ID"]]
    SUCCESS --> UNLOCK((("Sub-Menus unlocked:<br/>(Teams, Docs, etc.)")))

    


