# Z-00 Database Catalog:SUSTINA

| **Document ID** | **Version** | **Status** | **Owner (Author)** | **Approved By** | **Approved On** |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Z-00 | 2.0.0 | **DRAFT** | Business Architect | Product Officer |  |

## 1. Description & Scope
The **Z-00 Database Catalog** lists the table, and programming elements (stored prcoedures the entity documents in the DATA MODELLING folder defein.

# SUSTINA Database Object Catalogue

| Schema | Object Name | Object Type | Category | Source Spec |
| --- | --- | --- | --- | --- |
| Core | Z_02_01_Address_Master | Table | Table | Z-02.01 Address_Master.md |
| Core | Z_02_02_Contact_Master | Table | Table | Z-02.02 Contact_Master.md |
| Core | Z_02_03_Contact_Channel_Master | Table | Table | Z-02.03 Contact_Channel_Master.md |
| ESG | usp_Z_10_02_DQ_ESG_Forecast_FactorValidity | Stored Procedure | DQ Stored Procedure | Z-10.02 ESG_Forecast_Ledger |
| ESG | usp_Z_10_02_DQ_ESG_Forecast_OrphanCheck | Stored Procedure | DQ Stored Procedure | Z-10.02 ESG_Forecast_Ledger |
| ESG | usp_Z_10_02_DQ_ESG_Forecast_ScenarioCheck | Stored Procedure | DQ Stored Procedure | Z-10.02 ESG_Forecast_Ledger |
| ESG | usp_Z_10_02_DQ_ESG_Forecast_ValidationReport | Stored Procedure | DQ Stored Procedure | Z-10.02 ESG_Forecast_Ledger |
| ESG | usp_Z_10_03_DQ_Factor_EffectiveWindowCheck | Stored Procedure | DQ Stored Procedure | Z-10.03 ESG_Factor_Library |
| ESG | usp_Z_10_03_DQ_Factor_NACECheck | Stored Procedure | DQ Stored Procedure | Z-10.03 ESG_Factor_Library |
| ESG | usp_Z_10_03_DQ_Factor_OverlapCheck | Stored Procedure | DQ Stored Procedure | Z-10.03 ESG_Factor_Library |
| ESG | usp_Z_10_03_DQ_Factor_UnitCheck | Stored Procedure | DQ Stored Procedure | Z-10.03 ESG_Factor_Library |
| ESG | usp_Z_10_03_DQ_Factor_ValidityCheck | Stored Procedure | DQ Stored Procedure | Z-10.03 ESG_Factor_Library |
| ESG | DQ_Z_10_02_Forecast_DataQualitySuite | Data Quality Process | Data Quality Process | Z-10.02 ESG_Forecast_Ledger |
| ESG | DQ_Z_10_03_Factor_DataQualitySuite | Data Quality Process | Data Quality Process | Z-10.03 ESG_Factor_Library |
| ESG | usp_Z_10_02_ESG_Forecast_GetByPeriod | Stored Procedure | Stored Procedure | Z-10.02 ESG_Forecast_Ledger |
| ESG | usp_Z_10_02_ESG_Forecast_GetHistory | Stored Procedure | Stored Procedure | Z-10.02 ESG_Forecast_Ledger |
| ESG | usp_Z_10_02_ESG_Forecast_InsertOrUpdate | Stored Procedure | Stored Procedure | Z-10.02 ESG_Forecast_Ledger |
| ESG | usp_Z_10_02_ESG_Forecast_Recalculate | Stored Procedure | Stored Procedure | Z-10.02 ESG_Forecast_Ledger |
| ESG | usp_Z_10_03_ESG_Factor_Deactivate | Stored Procedure | Stored Procedure | Z-10.03 ESG_Factor_Library |
| ESG | usp_Z_10_03_ESG_Factor_GetEffective | Stored Procedure | Stored Procedure | Z-10.03 ESG_Factor_Library |
| ESG | usp_Z_10_03_ESG_Factor_GetHistory | Stored Procedure | Stored Procedure | Z-10.03 ESG_Factor_Library |
| ESG | usp_Z_10_03_ESG_Factor_Upsert | Stored Procedure | Stored Procedure | Z-10.03 ESG_Factor_Library |
| ESG | Z_10_01_ESG_Position_Ledger | Table | Table | Z-10.01 ESG Position Ledger (Actual ESG Impact).md |
| ESG | Z_10_02_ESG_Forecast_Ledger | Table | Table | Z-10.02 ESG_Forecast_Ledger.md |
| ESG | Z_10_03_ESG_Factor_Library | Table | Table | Z-10.03 ESG_Factor_Library.md |
| ESG | vw_Z_10_02_ESG_Forecast_WithNACE | View | View | Z-10.02 ESG_Forecast_Ledger.md |
| ESG | vw_Z_10_03_ESG_Factor_ByNACE | View | View | Z-10.03 ESG_Factor_Library.md |
| Entity | CorporateEntity | Table | Table | Z-01 CorporateEntity.md, Z-01.02 Corporate_Entity_(History).md, Z-01.05 CorporateEntity_Industry_Map.md |
| Entity | CorporateEntity_History | Table | Table | Z-01 CorporateEntity.md, Z-01.02 Corporate_Entity_(History).md |
| Entity | CorporateEntity_Internal_Classification | Table | Table | Z-01.03 CorporateEntity_Internal_Classification.md |
| Entity | Z_01_05_CorporateEntity_Industry_Map | Table | Table | Z-01.05 CorporateEntity_Industry_Map.md |
| Finance | Z_09_00_Accounting_Entity_Master | Table | Table | Z-09.00 Finance_Entity_Master.md |
| Finance | Z_09_01_Finance_Chart_of_Accounts | Table | Table | Z-09.00 Finance_Entity_Master.md, Z-09.01 Finance_Chart_of_Accounts.md |
| Finance | Z_09_02_Finance_General_Ledger | Table | Table | Z-09.02 Finance_General_Ledger.md |
| Finance | Z_09_03_Finance_Trial_Balance | Table | Table | Z-09.03 Finance_Trial_Balance.md |
| Finance | Z_09_04_Finance_Exchange_Rates | Table | Table | Z-09.04 Finance_Exchange_Rates.md |
| Finance | Z_09_05_Finance_Cost_Centre_Master | Table | Table | Z-09.05 Finance_Cost_Centre_Master.md |
| Finance | Z_09_06_Finance_Budget_Ledger | Table | Table | Z-09.06_Finance_Budget_Ledger.md |
| Procurement | Z_04_01_Supplier_Master | Table | Table | Z-04.01 Procurement_Supplier_Master.md |
| Procurement | Z_04_02_Supplier_Activity_Map | Table | Table | Z-04.02 Supplier_Activity_Map.md |
| Product | Z_05_01_Product_Master | Table | Table | Z-05.01 Product_Master.md |
| Product | Z_05_02_Product_Category_Master | Table | Table | Z-05.02 Product_Category_Master.md |
| Product | Z_05_03_Product_Lifecycle_History | Table | Table | Z-05.03 Product_Lifecycle_History.md |
| Product | Z_05_04_Product_ESG_Impact_Map | Table | Table | Z-05.04 Product_ESG_Impact_Map.md |
| Product | Z_05_05_Product_Financial_Map | Table | Table | Z-05.05 Product_Financial_Map.md |
| Ref | usp_Z_10_10_GetActiveNACEVersion | Stored Procedure | Stored Procedure | Z-10 Ref_NACE_Version_Master |
| Ref | usp_Z_10_11_GetNACEVersionByDate | Stored Procedure | Stored Procedure | Z-10 Ref_NACE_Version_Master |
| Ref | usp_Z_10_12_SearchNACE_AllVersions | Stored Procedure | Stored Procedure | Z-10 Ref_NACE_Version_Master |
| Ref | usp_Z_10_13_SearchNACE_ByVersion | Stored Procedure | Stored Procedure | Z-10 Ref_NACE_Version_Master |
| Ref | usp_Z_10_14_SearchNACE_ByYear | Stored Procedure | Stored Procedure | Z-10 Ref_NACE_Version_Master |
| Ref | usp_Z_10_15_CompareNACEVersions | Stored Procedure | Stored Procedure | Z-10 Ref_NACE_Version_Master |
| Ref | usp_Z_10_16_GetNACEVersionHistory | Stored Procedure | Stored Procedure | Z-10 Ref_NACE_Version_Master |
| Ref | usp_Z_10_40_DQ_NACE_OrphanCheck | Stored Procedure | DQ Stored Procedure | Z-10 Ref_NACE_Version_Master |
| Ref | usp_Z_10_41_DQ_NACE_CompletenessCheck | Stored Procedure | DQ Stored Procedure | Z-10 Ref_NACE_Version_Master |
| Ref | usp_Z_10_42_DQ_NACE_TitleFormatCheck | Stored Procedure | DQ Stored Procedure | Z-10 Ref_NACE_Version_Master |
| Ref | usp_Z_10_43_DQ_NACE_DuplicateCodeCheck | Stored Procedure | DQ Stored Procedure | Z-10 Ref_NACE_Version_Master |
| Ref | usp_Z_10_44_DQ_NACE_CrossVersionCheck | Stored Procedure | DQ Stored Procedure | Z-10 Ref_NACE_Version_Master |
| Ref | usp_Z_10_45_DQ_NACE_ActivityIntegrityCheck | Stored Procedure | DQ Stored Procedure | Z-10 Ref_NACE_Version_Master |
| Ref | usp_Z_10_46_DQ_NACE_RevisionLineageCheck | Stored Procedure | DQ Stored Procedure | Z-10 Ref_NACE_Version_Master |
| Ref | usp_Z_10_47_DQ_NACE_ESGFactorLinkCheck | Stored Procedure | DQ Stored Procedure | Z-10 Ref_NACE_Version_Master |
| Ref | usp_Z_10_48_DQ_NACE_ESGLedgerLinkCheck | Stored Procedure | DQ Stored Procedure | Z-10 Ref_NACE_Version_Master |
| Ref | usp_Z_10_49_DQ_NACE_VersionAuditCheck | Stored Procedure | DQ Stored Procedure | Z-10 Ref_NACE_Version_Master |
| Ref | DQ_Z_10_50_NACE_DataQualitySuite | Data Quality Process | Data Quality Process | Z-10 Ref_NACE_Version_Master |
| Ref | NACE_Activity_Master | Table | Table | Z-01.05 CorporateEntity_Industry_Map.md |
| Ref | NACE_Version_Master | Table | Table | Z-10_Ref_NACE_Version_Master.md |
| Ref | Z_01_04_Classification_Type_Master | Table | Table | Z-01.04 Classification_Type_Master.md |
| Ref | Z_02_04_Address_Type_Master | Table | Table | Z-02.04 Address_Type_Master.md |
| Ref | Z_02_05_Channel_Type_Master | Table | Table | Z-02.05 Channel_Type_Master.md |
| Ref | Z_02_06_Country_Master | Table | Table | Z-02.06 Country_Master.md |
| Ref | vw_Z_10_17_NACEVersion_WithUsage | View | View | Z-10 Ref_NACE_Version_Master |
| dbo | CountryDetails | Table | Table | Z-02.06 Country_Master.md |
