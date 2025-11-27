# Sustina Platform – Map of Demo Functionality
Below is the most recent version of the DEMO functionality in readiness for the CAB.
NOTE The workflow is an illustrative  non technicla overview to assist particpanet of the CAB to undertand oour offering and to stimulate thought and  inputs to the design meetings.

| **Document ID** | **Version** | **Status** | **Owner** | **Last Review** |
| :--- | :--- | :--- | :--- | :--- |
| GEN-001 | 1.1.0 | **DRAFT** | Architect | 2025-11-28 |

```mermaid
---
config:
  layout: elk
---
flowchart TB
    ReactApp["React App"] --> Home["Home"]
    Home --> Header["Header"] & Main["Main"] & Footer["Footer"] & Contact["Contact"]
    Home --> Contact["Contact"]
    Demo --> Contact["Contact"]
    Header --> Navbar["Navbar"]
    Navbar --> Home & Demo["Demo"] & Contact["Contact"]
    Demo --> SignIn["Sign In"] & Register["Register"] & Contact
    Contact --> APIChain["API Chain"]
    SignIn --> APIChain & Dashboard["Dashboard"] & ForgotPassword["Forgot Password"] & ResetPassword["Reset Password"]
    SignIn --> AuthOK{"Passed Security?"}
    AuthOK -->|YES| Navbar2["Navbar 2"]
    AuthOK -->|No| SignIn
    Register --> SignIn & ValidateReg["Validate Registration"] & ResendValidation["ReValidate Registration"]
    Navbar2 --> ManageEntities["Manage Entities"] & ManageEmissions["Manage Emissions"] & ManageSocial[["`Manage Social`"]] & ManageGovernance[["`Manage Governance`"]] & ManageTax["Tax Authorities"] & ManageSuppliers["Manage Suppliers"] & Export[["`Export`"]]
    ManageEntities --> CreateEntity["Create Entity"] & ManageTeam["Manage Team"] & ManageDocuments[["`Manage Documents`"]] & ManageCostCenters[["`Manage Cost Centers`"]]
    CreateEntity --> ManageOrg["Manage Organisation Structure"] & APIChain
    ManageTeam --> ManageOrg & APIChain
    ManageOrg --> APIChain
    ManageEmissions --> Transport & Travel & Power & Water
    ManageEmissions --> ManageDocuments2[["`Manage Documents`"]]
    Travel & Power & Transport & Water ---> LocalValidations["Local Validation"]
    LocalValidations --> decisionId{"`OK`"}
    decisionId -->|Yes| APIChain3["API Chain"] --> AssessImpact["Impact Assessment"]
    decisionId -->|No| RejectFlow["Remediations"]--->ManageEmissions
    AssessImpact --> ImpactOK{"OK"}
    ImpactOK -->|No| Rj2["Amendments"]-->ManageEmissions
    ImpactOK -->|Yes| PublishEMissions[["`Reporting Preparation`"]] 
    ManageTax --> ManageTaxDates[["`Manage Tax IDs`"]] & ManageTaxStandard[["`Manage Tax Codes`"]]
    ManageSuppliers --> ManageAddress[["`Manage Address`"]] & ManageInvoices[["`Manage Invoices`"]] & ManageDeclarations[["`Manage Declarations`"]]
    ManageSuppliers --> ManageDocuments2[["`Manage Documents`"]]
    ManageSocial --> ManageDocuments2[["`Manage Documents`"]]
    ManageDeclarations -->  SupplierImpact[["`ImpactAssessment`"]]
    ManageInvoices -->  SupplierImpact[["`ImpactAssessment`"]]
    SupplierImpact --> SImpactOK{"OK"}
    SImpactOK -->|No| Rj3["Amendments"]-->ManageDeclarations
    SImpactOK -->|Yes| PublishEMissions[["`Reporting Preparation`"]]     
```
