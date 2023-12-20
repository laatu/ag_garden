---
{"dg-publish":true,"permalink":"/code/c-trialbalanceq0001/","tags":["#CDS","Vercel_p"]}
---

```sql
@AbapCatalog: { sqlViewName: 'CFITRIALBALQ0001',
                preserveKey: true,
                compiler.compareFilter: true,
                buffering.status: #NOT_ALLOWED }
@AccessControl.authorizationCheck: #PRIVILEGED_ONLY
@Analytics.query: true
@Analytics.settings.maxProcessingEffort: #HIGH 
@ClientHandling.algorithm: #SESSION_VARIABLE
@EndUserText.label: 'Trial Balance'
@Metadata: { ignorePropagatedAnnotations: true,
             allowExtensions: true }
@ObjectModel: { supportedCapabilities: [ #ANALYTICAL_QUERY ],
                modelingPattern: #ANALYTICAL_QUERY, 
                usageType: { sizeCategory: #XXL,
                             serviceQuality: #D,
                             dataClass: #MIXED } }
@OData.publish: true
@VDM.viewType: #CONSUMPTION 
 
define view C_Trialbalanceq0001
with parameters
@Consumption.hidden: true
@Environment.systemField: #SYSTEM_LANGUAGE
  P_Language        : sylangu,
@Consumption.hidden: true
@Environment.systemField: #SYSTEM_DATE
  P_KeyDate         : sydate,

//@Consumption.derivation: { lookupEntity: 'I_CalendarDate', 
//    resultElement: 'FirstDayofMonthDate', binding: [
//    { targetElement : 'CalendarDate' , type : #PARAMETER, value : 'P_KeyDate' } ]
//   }    
@Consumption.derivation: { lookupEntity: 'I_CalendarDate', 
    resultElement: 'FirstDayofMonthDate', 
    binding: [
    { targetElement : 'CalendarDate' , type : #PARAMETER, value : 'P_ToPostingDate' } ]
   }    
  P_FromPostingDate : fis_budat_from,

//@Consumption.derivation: { lookupEntity: 'I_CalendarDate', 
//    resultElement: 'CalendarDate', binding: [      
//    { targetElement : 'CalendarDate' , type : #PARAMETER, value : 'P_KeyDate' } ]
//   }  
@Consumption.derivation: { lookupEntity: 'I_MySessionContext', 
    resultElement: 'UserLocalDate', binding: [
    { targetElement : 'UserID' , type : #SYSTEM_FIELD, value : '#USER' } ]
   }    
  P_ToPostingDate   : fis_budat_to      


as select from I_GLAcctBalanceCube 
( P_FromPostingDate: $parameters.P_FromPostingDate, P_ToPostingDate: $parameters.P_ToPostingDate ) as I_GLAcctBalanceCube

{

// Filter/ Fixed Rows 
@Consumption.filter: {selectionType: #SINGLE, multipleSelections: false, mandatory: true}
@Consumption.derivation: { lookupEntity: 'I_LedgerStdVH', 
      resultElement: 'Ledger', binding: [
      { targetElement : 'IsLeadingLedger' , type : #CONSTANT, value : 'X' } ]
     }
@AnalyticsDetails.query.variableSequence : 10
//@AnalyticsDetails.query.axis: #FREE
Ledger,

@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: true}
@AnalyticsDetails.query.variableSequence : 20
@AnalyticsDetails.query.axis: #ROWS
@AnalyticsDetails.query.totals: #SHOW
CompanyCode,
_CompanyCode.CompanyCodeName,

@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 30
@AnalyticsDetails.query.axis: #ROWS
@AnalyticsDetails.query.totals: #SHOW
@AnalyticsDetails.query.display: #KEY_TEXT
GLAccount,
//_GLAccountInChartOfAccounts._Text[1: Language = $parameters.P_Language].GLAccountName,

@Consumption.filter: {  selectionType: #HIERARCHY_NODE, multipleSelections: true, mandatory: false,
                        hierarchyBinding : [{type : #USER_INPUT, value : 'P_GLAccountHierarchyName', variableSequence: 31 } ] }
@EndUserText.label: 'G/L Account Hierarchy Node'                        
GLAccountHierarchy,
cast( _GLAccountInChartOfAccounts._Text[1: Language = $parameters.P_Language].GLAccountName as fis_racct_hierarchy_name preserving type ) as GLAccountHierarchyName,

//@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: true}
//@AnalyticsDetails.query.variableSequence : 40
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PostingDate,

@Consumption.filter: {selectionType: #SINGLE, multipleSelections: false, mandatory: false}
@AnalyticsDetails.query.variableSequence : 50
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
FiscalYear,

//  @Consumption.derivation: { lookupEntity: 'I_FsclYrIntvlDrvtnForPostgDate',
//  
//  resultElement: 'FromFiscalYear',
//  resultElementHigh: 'ToFiscalYear',
//  binding: [{ targetParameter : 'P_FromPostingDate' ,type : #PARAMETER, value : 'P_FromPostingDate' },
//            { targetParameter : 'P_ToPostingDate' ,type : #PARAMETER, value : 'P_ToPostingDate' },
//                     { targetElement: 'Ledger' , type : #ELEMENT, value : 'Ledger' }
////                     { targetElement: 'CompanyCode' , type : #ELEMENT, value : 'CompanyCode' }
//  ]
//  }
//  @Consumption.filter: { selectionType: #RANGE, multipleSelections: true, mandatory: false, hidden: true }
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
LedgerFiscalYear,
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//GLRecordType,
@Consumption.filter.hidden: true
@Consumption.filter: {selectionType: #SINGLE, multipleSelections: true} 
@Consumption.derivation: { lookupEntity: 'I_LedgerSourceLedger', 
      resultElement: 'SourceLedger', binding: [
      { targetElement : 'Ledger' , type : #ELEMENT, value : 'Ledger' } ]
     }
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
SourceLedger,

///////////////////////////////////////////////////////////////////////////////
// .INCLUDE ACDOC_SI_00  Unified Journal Entry: Transaction, Currencies, Units
///////////////////////////////////////////////////////////////////////////////
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@AnalyticsDetails.query.display: #KEY_TEXT
FinancialTransactionType,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
BusinessTransactionType,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ReferenceDocumentType,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
LogicalSystem,
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//ReferenceDocumentContext,
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//ReferenceDocument,
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//ReferenceDocumentItem,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ReferenceDocumentItemGroup,
//SUBTA,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
IsReversal,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
IsReversed,
//XTRUEREV,
//AWTYP_REV,
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//ReversalReferenceDocumentCntxt,
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//ReversalReferenceDocument,
//SUBTA_REV,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
IsSettlement,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
IsSettled,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PredecessorReferenceDocType,
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//PredecessorReferenceDocCntxt,
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//PredecessorReferenceDocument,
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//PredecessorReferenceDocItem,
//PREC_SUBTA,

////////////////////////////////////////////////////////////////////////////////////
// .INCLUDE  ACDOC_SI_GL_ACCAS Unified Journal Entry: G/L additional account assignments
////////////////////////////////////////////////////////////////////////////////////
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 70
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ProfitCenter,
_ProfitCenter[1:ValidityEndDate   >= $parameters.P_KeyDate and 
                ValidityStartDate <= $parameters.P_KeyDate]._Text[1:Language = $parameters.P_Language].ProfitCenterName,                
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 90
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
FunctionalArea,
_FunctionalArea._Text[1:Language = $parameters.P_Language].FunctionalAreaName,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 95
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
BusinessArea,
_BusinessArea._Text[1:Language = $parameters.P_Language].BusinessAreaName,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 90
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ControllingArea,
_ControllingArea.ControllingAreaName,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 80
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
Segment,
_Segment._Text[1:Language = $parameters.P_Language].SegmentName,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 305
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerCostCenter,
_PartnerCostCenter[1:ValidityEndDate  >= $parameters.P_KeyDate and 
                     ValidityStartDate <= $parameters.P_KeyDate]._Text[1:Language = $parameters.P_Language].CostCenterName as SenderCostCenterName,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 310
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerProfitCenter,
_PartnerProfitCenter[1:ValidityEndDate   >= $parameters.P_KeyDate and 
                       ValidityStartDate <= $parameters.P_KeyDate]._Text[1:Language = $parameters.P_Language].ProfitCenterName as PartnerProfitCenterName,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 315
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerFunctionalArea,
_PartnerFunctionalArea._Text[1:Language = $parameters.P_Language].FunctionalAreaName as PartnerFunctionalAreaName,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 320
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerBusinessArea,
_PartnerBusinessArea._Text[1:Language = $parameters.P_Language].BusinessAreaName as PartnerBusinessAreaName,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 325
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerCompany,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 330
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerSegment,
_PartnerSegment._Text[1:Language = $parameters.P_Language].SegmentName as PartnerSegmentName,

/////////////////////////////////////////////////////////////////////////////
// .INCLUDE ACDOC_SI_FIX  Unified Journal Entry: Mandatory fields for G/L
////////////////////////////////////////////////////////////////////////////
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
DebitCreditCode,
_DebitCreditCode._Text[1:Language = $parameters.P_Language].DebitCreditCodeName,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
FiscalYearVariant,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 990
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
FiscalYearPeriod,
//@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
//@AnalyticsDetails.query.variableSequence : 995
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//FiscalPeriod,
//bldat,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
AccountingDocumentType,
_AccountingDocumentType._Text[1:Language = $parameters.P_Language].AccountingDocumentTypeName,
//buzei,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
AssignmentReference,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PostingKey,
//_PostingKey._Text[1:Language = $parameters.P_Language].PostingKeyName,
_PostingKey._PostingKeyText[1:Language = $parameters.P_Language].PostingKeyName as PostingKeyName,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
AccountingDocumentCategory,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
TransactionTypeDetermination,
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//SubLedgerAcctLineItemType,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
AccountingDocCreatedByUser,
//timestamp,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
EliminationProfitCenter,
_EliminationProfitCenter[1:ValidityEndDate   >= $parameters.P_KeyDate and 
                       ValidityStartDate <= $parameters.P_KeyDate]._Text[1:Language = $parameters.P_Language].ProfitCenterName as EliminationProfitCenterName,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
OriginObjectType,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
GLAccountType,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ChartOfAccounts,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 161
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@AnalyticsDetails.query.display: #KEY_TEXT
AlternativeGLAccount,
//_AlternativeGLAccount._Text[1: Language = $parameters.P_Language].GLAccountName as AlternativeGLAccountName,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
CountryChartOfAccounts,

///////////////////////////////////////////////////////////////////////////
// .INCLUDE ACDOC_SI_GEN  Unified Journal Entry: Fields for several subledgers
//////////////////////////////////////////////////////////////////////////
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//InvoiceReference,
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//InvoiceReferenceFiscalYear,
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//FollowOnDocumentType,
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//InvoiceItemReference,
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//ReferencePurchaseOrderCategory,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PurchasingDocument,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PurchasingDocumentItem,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
AccountAssignmentNumber,
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//DocumentItemText,
@API.element.releaseState: #DEPRECATED
@API.element.successor:   'Product'
@VDM.lifecycle.status:    #DEPRECATED
@VDM.lifecycle.successor: 'Product'
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 105
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
Material,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 105
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
Product,
//_Material._Text[1:Language = $parameters.P_Language].MaterialName,
_Product._Text[1:Language = $parameters.P_Language].ProductName as MaterialName,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 100
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
Plant,
_Plant.PlantName,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
Supplier,
_Supplier.SupplierName,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
Customer,
_Customer.CustomerName,

/////////////////////////////////////////////////////////////////////////
// .INCLUDE ACDOC_SI_FI  Unified Journal Entry: Fields for FI subledgers
/////////////////////////////////////////////////////////////////////////
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
FinancialAccountType,
_FinancialAccountType._Text[1:Language = $parameters.P_Language].FinancialAccountTypeName,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
SpecialGLCode,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
TaxCode,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
HouseBank,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
HouseBankAccount,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
IsOpenItemManaged,
//augdt,
@API.element.releaseState: #DEPRECATED
@API.element.successor:    'ClearingJournalEntry'
@VDM.lifecycle.status:    #DEPRECATED
@VDM.lifecycle.successor: 'ClearingJournalEntry'
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ClearingAccountingDocument,
@API.element.releaseState: #DEPRECATED
@API.element.successor:    'ClearingJournalEntryFiscalYear'
@VDM.lifecycle.status:    #DEPRECATED
@VDM.lifecycle.successor: 'ClearingJournalEntryFiscalYear' 
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ClearingDocFiscalYear,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ClearingJournalEntry,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ClearingJournalEntryFiscalYear,
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//IsCleared,


/////////////////////////////////////////////////////////////////////////
// .INCLUDE ACDOC_SI_FAA  Unified Journal Entry: Fields for Asset Accounting
/////////////////////////////////////////////////////////////////////////
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
AssetDepreciationArea,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 110
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
MasterFixedAsset,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 120
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
FixedAsset,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
AssetValueDate,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
AssetTransactionType,
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//AssetAcctTransClassfctn,
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//DepreciationFiscalPeriod,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
GroupMasterFixedAsset,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 130
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
GroupFixedAsset,
//settlement_rule,

//////////////////////////////////////////////////////////////////////////
// .INCLUDE ACDOC_SI_ML Unified Journal Entry: Fields for Material Ledger
//////////////////////////////////////////////////////////////////////////
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//CostEstimate,
@API.element.releaseState: #DEPRECATED
@API.element.successor:   'InvtrySpecialStockValnType_2'
@VDM.lifecycle.status:    #DEPRECATED
@VDM.lifecycle.successor: 'InvtrySpecialStockValnType_2'          
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
InventorySpecialStockValnType,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
InvtrySpecialStockValnType_2,
//xobew,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
InventorySpecialStockType,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
InventorySpclStkSalesDocument,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
InventorySpclStkSalesDocItm,
@API.element.releaseState: #DEPRECATED
@API.element.successor:    'InvtrySpclStockWBSElmntExtID'
@VDM.lifecycle.status:    #DEPRECATED
@VDM.lifecycle.successor: 'InvtrySpclStockWBSElmntExtID'
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
InventorySpclStockWBSElement,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
InvtrySpclStockWBSElmntExtID,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
InventorySpecialStockSupplier,
_InventorySpecialStockSupplier.SupplierName as InvtrySpecialStockSupplierName,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
InventoryValuationType,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ValuationArea,

////////////////////////////////////////////////////////////////////////////
// .INCLUDE ACDOC_SI_CFIN   Unified Journal Entry: Fields for Central Finance
//////////////////////////////////////////////////////////////////////////////
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
SenderGLAccount,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
SenderAccountAssignment,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
SenderAccountAssignmentType,

////////////////////////////////////////////////////////////////////////////
//  .INCLUDE  ACDOC_SI_CO  Unified Journal Entry: CO fields
///////////////////////////////////////////////////////////////////////////
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//UtilsProfileConstcyChkGrp,
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//ControllingDebitCreditCode,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ControllingObjectDebitType,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
QuantityIsIncomplete,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
OffsettingAccount,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
OffsettingAccountType,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
LineItemIsCompleted,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 140
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PersonnelNumber,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ControllingObjectClass,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerCompanyCode,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerControllingObjectClass,
//aufnr_org,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 330
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
OriginCostCenter,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 335
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
OriginCostCtrActivityType,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 150
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
AccountAssignment,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
AccountAssignmentType,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 210
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ProjectNetwork,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
RelatedNetworkActivity,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 220
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
BusinessProcess,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 230
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
CostObject,
//bemot,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
CustomerServiceNotification,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
OperatingConcern,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 340
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerAccountAssignment,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerAccountAssignmentType,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 345
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerCostCtrActivityType,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 350
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerOrder,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 355
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerOrderCategory,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 360
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@API.element.releaseState: #DEPRECATED
@API.element.successor:   'PartnerWBSElementExternalID'
@VDM.lifecycle.status:    #DEPRECATED
@VDM.lifecycle.successor: 'PartnerWBSElementExternalID'
PartnerWBSElement,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerWBSElementExternalID,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 365
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@API.element.releaseState: #DEPRECATED
@API.element.successor:   'PartnerProjectExternalID'
@VDM.lifecycle.status:    #DEPRECATED
@VDM.lifecycle.successor: 'PartnerProjectExternalID'
PartnerProject,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerProjectExternalID,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 370
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerSalesDocument,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerSalesDocumentItem,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 375
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerProjectNetwork,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerProjectNetworkActivity,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 380
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerBusinessProcess,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 385
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerCostObject,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ServiceDocumentType,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ServiceDocument,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ServiceDocumentItem,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerServiceDocumentType,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerServiceDocument,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerServiceDocumentItem,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ServiceContract,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ServiceContractType,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ServiceContractItem,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
TimeSheetOvertimeCategory,
//////////////////////////////////////////////////////////////////////
//  .INCLUDE  ACDOC_SI_COPA  Unified Journal Entry: CO-PA fields
//////////////////////////////////////////////////////////////////////
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 240
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
BillingDocumentType,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 250
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
SalesOrganization,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 260
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
DistributionChannel,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 270
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
OrganizationDivision,
@API.element.releaseState: #DEPRECATED
@API.element.successor:   'SoldProduct'      
@VDM.lifecycle.status:    #DEPRECATED
@VDM.lifecycle.successor: 'SoldProduct'
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 280
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
SoldMaterial,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 280
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
SoldProduct,
@API.element.releaseState: #DEPRECATED
@API.element.successor:   'SoldProductGroup'
@VDM.lifecycle.status:    #DEPRECATED
@VDM.lifecycle.successor: 'SoldProductGroup'
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 290
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
MaterialGroup,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 290
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
SoldProductGroup,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 290
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@API.element.releaseState: #DEPRECATED
@API.element.successor:   'SoldProductGroup'
@VDM.lifecycle.status:    #DEPRECATED
@VDM.lifecycle.successor: 'SoldProductGroup'

ProductGroup,
//_MaterialGroup._Text[1:Language = $parameters.P_Language].MaterialGroupName,
//_ProductGroup._Text[1:Language = $parameters.P_Language].MaterialGroupName,
_SoldProductGroup_2._ProductGroupText[1:Language = $parameters.P_Language].ProductGroupName as MaterialGroupName,
//_ProductGroup_2._ProductGroupText[1:Language = $parameters.P_Language].ProductGroupName as MaterialGroupName,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 300
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
CustomerGroup,
_CustomerGroup._Text[1:Language = $parameters.P_Language].CustomerGroupName,

//////////////////////////////////////////////////////////////////////
// .INCLUDE ACDOC_SI_PS  Unified Journal Entry: Fields for Public Sector
/////////////////////////////////////////////////////////////////////
//re_bukrs,
//re_account,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 385
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
FinancialManagementArea,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 390
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
Fund,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 393
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
FundsCenter,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 395
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
GrantID,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 400
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
BudgetPeriod,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 405
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerFund,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 410
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerGrant,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 415
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
PartnerBudgetPeriod,

///////////////////////////////////////////////////////////////////////
// .INCLUDE ACDOC_SI_JVA  Unified Journal Entry: Fields for Joint Venture Accounting
///////////////////////////////////////////////////////////////////////
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
JointVenture,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
JointVentureEquityGroup,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
JointVentureCostRecoveryCode,
//vptnr,
//btype,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
JointVentureEquityType,
//prodper,

///////////////////////////////////////////////////////////////////////
// .INCLUDE ACDOC_SI_RE STRU  0 0 Unified Journal Entry: Fields for Real Estate
///////////////////////////////////////////////////////////////////////
//swenr,
//sgenr,
//sgrnr,
//smenr,
//recnnr,
//snksl,
//sempsl,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
SettlementReferenceDate,
//pswenr,
//psgenr,
//psgrnr,
//psmenr,
//precnnr,
//psnksl,
//psempsl,
//pdabrz,

@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 60
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
CostCenter,
_CostCenter[1:ValidityEndDate   >= $parameters.P_KeyDate and 
              ValidityStartDate <= $parameters.P_KeyDate]._Text[1:Language = $parameters.P_Language].CostCenterName,

@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 160
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
CostCtrActivityType,
//@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
//@AnalyticsDetails.query.variableSequence : 170
//@AnalyticsDetails.query.axis: #FREE
//@AnalyticsDetails.query.totals: #SHOW
//InternalOrder,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 180
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
OrderID,
//@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 190
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
OrderCategory,
//@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 200
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@API.element.releaseState: #DEPRECATED
@API.element.successor:   'WBSElementExternalID'
@VDM.lifecycle.status:    #DEPRECATED
@VDM.lifecycle.successor: 'WBSElementExternalID'
WBSElement,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
WBSElementExternalID,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 210
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@API.element.releaseState: #DEPRECATED
@API.element.successor:   'ProjectExternalID'
@VDM.lifecycle.status:    #DEPRECATED
@VDM.lifecycle.successor: 'ProjectExternalID'
Project,
@Consumption.filter: {selectionType: #RANGE, multipleSelections: true }
@AnalyticsDetails.query.variableSequence : 211
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ProjectExternalID,
@API.element.releaseState: #DEPRECATED
@API.element.successor:   'SalesDocument'
@VDM.lifecycle.status:    #DEPRECATED
@VDM.lifecycle.successor: 'SalesDocument'      
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
SalesOrder,
@API.element.releaseState: #DEPRECATED
@API.element.successor:   'SalesDocumentItem'
@VDM.lifecycle.status:    #DEPRECATED
@VDM.lifecycle.successor: 'SalesDocumentItem'   
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
SalesOrderItem,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
SalesDocument,
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
SalesDocumentItem,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
ClearingDate,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@Semantics.currencyCode:true
CompanyCodeCurrency,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@Semantics.currencyCode:true
GlobalCurrency,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@Semantics.currencyCode:true
FunctionalCurrency,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@Semantics.currencyCode:true
FreeDefinedCurrency1,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@Semantics.currencyCode:true
FreeDefinedCurrency2,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@Semantics.currencyCode:true
FreeDefinedCurrency3,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@Semantics.currencyCode:true
FreeDefinedCurrency4,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@Semantics.currencyCode:true
FreeDefinedCurrency5,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@Semantics.currencyCode:true
FreeDefinedCurrency6,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@Semantics.currencyCode:true
FreeDefinedCurrency7,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@Semantics.currencyCode:true
FreeDefinedCurrency8,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@Semantics.currencyCode:true
BalanceTransactionCurrency,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@Semantics.currencyCode:true
TransactionCurrency,

@AnalyticsDetails.query.axis: #FREE
@Consumption.hidden: true
FiscalPeriodDate,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
CalendarYear,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
CalendarQuarter,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
CalendarYearQuarter,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
CalendarMonth,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
CalendarYearMonth,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
CalendarWeek,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
CalendarYearWeek,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
FiscalQuarter,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
FiscalWeek,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
FiscalYearQuarter,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
FiscalYearWeek,

@Consumption.filter: {selectionType: #RANGE, multipleSelections: true, mandatory: false}
@AnalyticsDetails.query.variableSequence : 171
@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
@AnalyticsDetails.query.display: #KEY_TEXT
CorporateGroupAccount,
//_GLAccountInChartOfAccounts._CorporateGroupAccountText[1: Language = $parameters.P_Language].GLAccountName as CorporateGroupAccountName,

@AnalyticsDetails.query.axis: #FREE
@AnalyticsDetails.query.totals: #SHOW
CorporateGroupChartOfAccounts,

@Consumption.filter: {selectionType: #SINGLE, multipleSelections: false, mandatory: false}
@AnalyticsDetails.query.variableSequence : 999
@AnalyticsDetails.query.axis: #FREE
IsBalanceSheetAccount,

  @Consumption.derivation: { lookupEntity: 'I_FsclYrIntvlDrvtnForPostgDate',
  
  resultElement: 'FromFiscalYear',
  resultElementHigh: 'ToFiscalYear',
  binding: [{ targetParameter : 'P_FromPostingDate' ,type : #PARAMETER, value : 'P_FromPostingDate' },
            { targetParameter : 'P_ToPostingDate' ,type : #PARAMETER, value : 'P_ToPostingDate' },
                     { targetElement: 'Ledger' , type : #ELEMENT, value : 'Ledger' }
//                     { targetElement: 'CompanyCode' , type : #ELEMENT, value : 'CompanyCode' }
  ]
  }
  @Consumption.filter: { selectionType: #INTERVAL, multipleSelections: true, mandatory: false, hidden: true }
//  @Consumption.hidden: true
//  @AnalyticsDetails.query.hidden: true
  FlowOfFundsLedgerFiscalYear,


////// Measures

// Calculation of starting balance is done in the query for performance reasons
@AnalyticsDetails.query.axis: #COLUMNS
//@EndUserText.label: 'Starting Balance CC'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'CompanyCodeCurrency'
@AnalyticsDetails.query.formula : '$projection.EndingBalanceAmtInCoCodeCrcy - $projection.DebitAmountInCoCodeCrcy - CreditAmountInCoCodeCrcy' 
cast( cast( 1 as abap.dec(23,2)) as fis_start_bal_hsl_ui ) as StartingBalanceAmtInCoCodeCrcy,  


// Debit and Credit Balance has an exception aggregation to make sure that LedgerFiscalYear is part of the select list
// this is necessary to push down the aggregation to the lowest level, since LedgerFiscalYear is part of the join condition of the FiscalCalenderDate
@AnalyticsDetails.query.axis: #COLUMNS
//@EndUserText.label: 'Debit Balance CC'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'CompanyCodeCurrency'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.GLAcctDebitAmtInCoCodeCrcy' cast( cast( 1 as abap.dec(23,2)) as fis_dr_bal_hsl ) as DebitAmountInCoCodeCrcy,  

@AnalyticsDetails.query.axis: #COLUMNS
//@EndUserText.label: 'Credit Balance CC'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'CompanyCodeCurrency'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.GLAcctCreditAmtInCoCodeCrcy' cast( cast( 1 as abap.dec(23,2)) as fis_cr_bal_hsl ) as CreditAmountInCoCodeCrcy,  

@AnalyticsDetails.query.axis: #COLUMNS
//@EndUserText.label: 'Ending Balance CC'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'CompanyCodeCurrency'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #LAST, 
                                      exceptionAggregationElements: ['FiscalPeriodDate']
//                                      exceptionAggregationElements: ['PostingDate']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdEndingBalAmtInCoCodeCrcy' cast( cast( 1 as abap.dec(23,2)) as fis_end_bal_hsl_ui ) as EndingBalanceAmtInCoCodeCrcy,  


// Hidden fields
@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'CompanyCodeCurrency'
@Consumption.hidden: true
EndingBalanceAmtInCoCodeCrcy as IntmdEndingBalAmtInCoCodeCrcy,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'CompanyCodeCurrency'
@Consumption.hidden: true
DebitAmountInCoCodeCrcy as GLAcctDebitAmtInCoCodeCrcy,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'CompanyCodeCurrency'
@Consumption.hidden: true
CreditAmountInCoCodeCrcy as GLAcctCreditAmtInCoCodeCrcy,

// Calculation of starting balance (global Currency) is done in the query for performance reasons
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Start. Bal. GlobCrcy'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'GlobalCurrency'
@AnalyticsDetails.query.formula : '$projection.EndingBalanceAmtInGlobalCrcy - $projection.DebitAmountInGlobalCrcy - CreditAmountInGlobalCrcy' 
cast( cast( 1 as abap.dec(23,2)) as fis_start_bal_ksl_ui ) as StartingBalanceAmtInGlobalCrcy,  


// Debit and Credit Balance has an exception aggregation to make sure that LedgerFiscalYear is part of the select list
// this is necessary to push down the aggregation to the lowest level, since LedgerFiscalYear is part of the join condition of the FiscalCalenderDate
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Debit Bal. GlobCrcy'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'GlobalCurrency'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdDebitAmountInGlobalCrcy' cast( cast( 1 as abap.dec(23,2)) as fis_dr_bal_ksl ) as DebitAmountInGlobalCrcy,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Credit Bal. GlobCrcy'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'GlobalCurrency'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdCreditAmountInGlobalCrcy' cast( cast( 1 as abap.dec(23,2)) as fis_cr_bal_ksl ) as CreditAmountInGlobalCrcy,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Ending Bal. GlobCrcy'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'GlobalCurrency'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #LAST, 
                                      exceptionAggregationElements: ['FiscalPeriodDate']
//                                      exceptionAggregationElements: ['PostingDate']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdEndingBalAmtInGlobalCrcy' cast( cast( 1 as abap.dec(23,2)) as fis_end_bal_ksl_ui ) as EndingBalanceAmtInGlobalCrcy,  

// Hidden fields
@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'GlobalCurrency'
@Consumption.hidden: true
EndingBalanceAmtInGlobalCrcy as IntmdEndingBalAmtInGlobalCrcy,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'GlobalCurrency'
@Consumption.hidden: true
DebitAmountInGlobalCrcy as IntmdDebitAmountInGlobalCrcy,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'GlobalCurrency'
@Consumption.hidden: true
CreditAmountInGlobalCrcy as IntmdCreditAmountInGlobalCrcy,


// Calculation of starting balance (Functional Currency) is done in the query for performance reasons
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FunctionalCurrency'
@AnalyticsDetails.query.formula : '$projection.EndingBalanceAmtInFuncnlCrcy - $projection.DebitAmountInFunctionalCrcy - CreditAmountInFunctionalCrcy' 
cast( cast( 1 as abap.dec(23,2)) as fis_start_bal_fcsl_ui ) as StartingBalanceAmtInFuncnlCrcy,  


// Debit and Credit Balance has an exception aggregation to make sure that LedgerFiscalYear is part of the select list
// this is necessary to push down the aggregation to the lowest level, since LedgerFiscalYear is part of the join condition of the FiscalCalenderDate
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Debit Bal. GlobCrcy'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FunctionalCurrency'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdDebitAmountInFuncnlCrcy' cast( cast( 1 as abap.dec(23,2)) as fis_dr_amt_funccrcy ) as DebitAmountInFunctionalCrcy,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FunctionalCurrency'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdCreditAmountInFuncnlCrcy' cast( cast( 1 as abap.dec(23,2)) as fis_cr_amt_funccrcy ) as CreditAmountInFunctionalCrcy,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FunctionalCurrency'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #LAST, 
                                      exceptionAggregationElements: ['FiscalPeriodDate']
//                                      exceptionAggregationElements: ['PostingDate']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdEndingBalAmtInFuncnlCrcy' cast( cast( 1 as abap.dec(23,2)) as fis_end_bal_fcsl_ui ) as EndingBalanceAmtInFuncnlCrcy,  
// Hidden fields
@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FunctionalCurrency'
@Consumption.hidden: true
EndingBalanceAmtInFuncnlCrcy as IntmdEndingBalAmtInFuncnlCrcy,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'GlobalCurrency'
@Consumption.hidden: true
DebitAmountInFunctionalCrcy as IntmdDebitAmountInFuncnlCrcy,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'GlobalCurrency'
@Consumption.hidden: true
CreditAmountInFunctionalCrcy as IntmdCreditAmountInFuncnlCrcy,



//// Calculation of starting balance (FreeDefinedCurrency1) is done in the query for performance reasons
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Start. Bal. Free Dfnd Crcy 1' 
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency1'
@AnalyticsDetails.query.formula : '$projection.EndingBalAmtInFreeDfndCrcy1 - $projection.DebitAmountInFreeDefinedCrcy1 - CreditAmountInFreeDefinedCrcy1' 
cast( cast( 1 as abap.dec(23,2)) as fis_start_bal_osl_ui ) as StartingBalAmtInFreeDfndCrcy1,  


// Debit and Credit Balance has an exception aggregation to make sure that LedgerFiscalYear is part of the select list
// this is necessary to push down the aggregation to the lowest level, since LedgerFiscalYear is part of the join condition of the FiscalCalenderDate
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Debit Bal. Free Dfnd Crcy 1'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency1'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdDebitAmtInFreeDfndCrcy1' cast( cast( 1 as abap.dec(23,2)) as fis_dr_bal_osl ) as DebitAmountInFreeDefinedCrcy1,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Credit Bal. Free Dfnd Crcy 1'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency1'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdCreditAmtInFreeDfndCrcy1' cast( cast( 1 as abap.dec(23,2)) as fis_cr_bal_osl ) as CreditAmountInFreeDefinedCrcy1,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Ending Bal. Free Dfnd Crcy 1'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency1'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #LAST, 
                                      exceptionAggregationElements: ['FiscalPeriodDate']
//                                      exceptionAggregationElements: ['PostingDate']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdEndingBalInFreeDfndCrcy1' cast( cast( 1 as abap.dec(23,2)) as fis_end_bal_osl_ui ) as EndingBalAmtInFreeDfndCrcy1,  

// Hidden fields
@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency1'
@Consumption.hidden: true
EndingBalAmtInFreeDfndCrcy1 as IntmdEndingBalInFreeDfndCrcy1,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency1'
@Consumption.hidden: true
DebitAmountInFreeDefinedCrcy1 as IntmdDebitAmtInFreeDfndCrcy1,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency1'
@Consumption.hidden: true
CreditAmountInFreeDefinedCrcy1 as IntmdCreditAmtInFreeDfndCrcy1,



// Calculation of starting balance (FreeDefinedCurrency2) is done in the query for performance reasons
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Start. Bal. Free Dfnd Crcy 2' 
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency2'
@AnalyticsDetails.query.formula : '$projection.EndingBalAmtInFreeDfndCrcy2 - $projection.DebitAmountInFreeDefinedCrcy2 - CreditAmountInFreeDefinedCrcy2' 
cast( cast( 1 as abap.dec(23,2)) as fis_start_bal_vsl_ui ) as StartingBalAmtInFreeDfndCrcy2,  


// Debit and Credit Balance has an exception aggregation to make sure that LedgerFiscalYear is part of the select list
// this is necessary to push down the aggregation to the lowest level, since LedgerFiscalYear is part of the join condition of the FiscalCalenderDate
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Debit Bal. Free Dfnd Crcy 2'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency2'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdDebitAmtInFreeDfndCrcy2' cast( cast( 1 as abap.dec(23,2)) as fis_dr_bal_vsl ) as DebitAmountInFreeDefinedCrcy2,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Credit Bal. Free Dfnd Crcy 2'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency2'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdCreditAmtInFreeDfndCrcy2' cast( cast( 1 as abap.dec(23,2)) as fis_cr_bal_vsl ) as CreditAmountInFreeDefinedCrcy2,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Ending Bal. Free Dfnd Crcy 2'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency2'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #LAST, 
                                      exceptionAggregationElements: ['FiscalPeriodDate']
//                                      exceptionAggregationElements: ['PostingDate']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdEndingBalInFreeDfndCrcy2' cast( cast( 1 as abap.dec(23,2)) as fis_end_bal_vsl_ui ) as EndingBalAmtInFreeDfndCrcy2,  

// Hidden fields
@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency2'
@Consumption.hidden: true
EndingBalAmtInFreeDfndCrcy2 as IntmdEndingBalInFreeDfndCrcy2,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency2'
@Consumption.hidden: true
DebitAmountInFreeDefinedCrcy2 as IntmdDebitAmtInFreeDfndCrcy2,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency2'
@Consumption.hidden: true
CreditAmountInFreeDefinedCrcy2 as IntmdCreditAmtInFreeDfndCrcy2,



// Calculation of starting balance (FreeDefinedCurrency3) is done in the query for performance reasons
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Start. Bal. Free Dfnd Crcy 3' 
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency3'
@AnalyticsDetails.query.formula : '$projection.EndingBalAmtInFreeDfndCrcy3 - $projection.DebitAmountInFreeDefinedCrcy3 - CreditAmountInFreeDefinedCrcy3' 
cast( cast( 1 as abap.dec(23,2)) as fis_start_bal_bsl_ui ) as StartingBalAmtInFreeDfndCrcy3,  


// Debit and Credit Balance has an exception aggregation to make sure that LedgerFiscalYear is part of the select list
// this is necessary to push down the aggregation to the lowest level, since LedgerFiscalYear is part of the join condition of the FiscalCalenderDate
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Debit Bal. Free Dfnd Crcy 3'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency3'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdDebitAmtInFreeDfndCrcy3' cast( cast( 1 as abap.dec(23,2)) as fis_dr_bal_bsl ) as DebitAmountInFreeDefinedCrcy3,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Credit Bal. Free Dfnd Crcy 3'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency3'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdCreditAmtInFreeDfndCrcy3' cast( cast( 1 as abap.dec(23,2)) as fis_cr_bal_bsl ) as CreditAmountInFreeDefinedCrcy3,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Ending Bal. Free Dfnd Crcy 3'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency3'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #LAST, 
                                      exceptionAggregationElements: ['FiscalPeriodDate']
//                                      exceptionAggregationElements: ['PostingDate']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdEndingBalInFreeDfndCrcy3' cast( cast( 1 as abap.dec(23,2)) as fis_end_bal_bsl_ui ) as EndingBalAmtInFreeDfndCrcy3,  

// Hidden fields
@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency3'
@Consumption.hidden: true
EndingBalAmtInFreeDfndCrcy3 as IntmdEndingBalInFreeDfndCrcy3,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency3'
@Consumption.hidden: true
DebitAmountInFreeDefinedCrcy3 as IntmdDebitAmtInFreeDfndCrcy3,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency3'
@Consumption.hidden: true
CreditAmountInFreeDefinedCrcy3 as IntmdCreditAmtInFreeDfndCrcy3,



// Calculation of starting balance (FreeDefinedCurrency4) is done in the query for performance reasons
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Start. Bal. Free Dfnd Crcy 4' 
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency4'
@AnalyticsDetails.query.formula : '$projection.EndingBalAmtInFreeDfndCrcy4 - $projection.DebitAmountInFreeDefinedCrcy4 - CreditAmountInFreeDefinedCrcy4' 
cast( cast( 1 as abap.dec(23,2)) as fis_start_bal_csl ) as StartingBalAmtInFreeDfndCrcy4,  


// Debit and Credit Balance has an exception aggregation to make sure that LedgerFiscalYear is part of the select list
// this is necessary to push down the aggregation to the lowest level, since LedgerFiscalYear is part of the join condition of the FiscalCalenderDate
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Debit Bal. Free Dfnd Crcy 4'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency4'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdDebitAmtInFreeDfndCrcy4' cast( cast( 1 as abap.dec(23,2)) as fis_dr_bal_csl ) as DebitAmountInFreeDefinedCrcy4,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Credit Bal. Free Dfnd Crcy 4'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency4'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdCreditAmtInFreeDfndCrcy4' cast( cast( 1 as abap.dec(23,2)) as fis_cr_bal_csl ) as CreditAmountInFreeDefinedCrcy4,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Ending Bal. Free Dfnd Crcy 4'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency4'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #LAST, 
                                      exceptionAggregationElements: ['FiscalPeriodDate']
//                                      exceptionAggregationElements: ['PostingDate']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdEndingBalInFreeDfndCrcy4' cast( cast( 1 as abap.dec(23,2)) as fis_end_bal_csl_ui ) as EndingBalAmtInFreeDfndCrcy4,  

// Hidden fields
@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency4'
@Consumption.hidden: true
EndingBalAmtInFreeDfndCrcy4 as IntmdEndingBalInFreeDfndCrcy4,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency4'
@Consumption.hidden: true
DebitAmountInFreeDefinedCrcy4 as IntmdDebitAmtInFreeDfndCrcy4,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency4'
@Consumption.hidden: true
CreditAmountInFreeDefinedCrcy4 as IntmdCreditAmtInFreeDfndCrcy4,



// Calculation of starting balance (FreeDefinedCurrency5) is done in the query for performance reasons
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Start. Bal. Free Dfnd Crcy 5' 
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency5'
@AnalyticsDetails.query.formula : '$projection.EndingBalAmtInFreeDfndCrcy5 - $projection.DebitAmountInFreeDefinedCrcy5 - CreditAmountInFreeDefinedCrcy5' 
cast( cast( 1 as abap.dec(23,2)) as fis_start_bal_dsl ) as StartingBalAmtInFreeDfndCrcy5,  


// Debit and Credit Balance has an exception aggregation to make sure that LedgerFiscalYear is part of the select list
// this is necessary to push down the aggregation to the lowest level, since LedgerFiscalYear is part of the join condition of the FiscalCalenderDate
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Debit Bal. Free Dfnd Crcy 5'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency5'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdDebitAmtInFreeDfndCrcy5' cast( cast( 1 as abap.dec(23,2)) as fis_dr_bal_dsl ) as DebitAmountInFreeDefinedCrcy5,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Credit Bal. Free Dfnd Crcy 5'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency5'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdCreditAmtInFreeDfndCrcy5' cast( cast( 1 as abap.dec(23,2)) as fis_cr_bal_dsl ) as CreditAmountInFreeDefinedCrcy5,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Ending Bal. Free Dfnd Crcy 5'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency5'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #LAST, 
                                      exceptionAggregationElements: ['FiscalPeriodDate']
//                                      exceptionAggregationElements: ['PostingDate']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdEndingBalInFreeDfndCrcy5' cast( cast( 1 as abap.dec(23,2)) as fis_end_bal_dsl_ui ) as EndingBalAmtInFreeDfndCrcy5,  

// Hidden fields
@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency5'
@Consumption.hidden: true
EndingBalAmtInFreeDfndCrcy5 as IntmdEndingBalInFreeDfndCrcy5,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency5'
@Consumption.hidden: true
DebitAmountInFreeDefinedCrcy5 as IntmdDebitAmtInFreeDfndCrcy5,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency5'
@Consumption.hidden: true
CreditAmountInFreeDefinedCrcy5 as IntmdCreditAmtInFreeDfndCrcy5,



// Calculation of starting balance (FreeDefinedCurrency6) is done in the query for performance reasons
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Start. Bal. Free Dfnd Crcy 6' 
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency6'
@AnalyticsDetails.query.formula : '$projection.EndingBalAmtInFreeDfndCrcy6 - $projection.DebitAmountInFreeDefinedCrcy6 - CreditAmountInFreeDefinedCrcy6' 
cast( cast( 1 as abap.dec(23,2)) as fis_start_bal_esl ) as StartingBalAmtInFreeDfndCrcy6,  


// Debit and Credit Balance has an exception aggregation to make sure that LedgerFiscalYear is part of the select list
// this is necessary to push down the aggregation to the lowest level, since LedgerFiscalYear is part of the join condition of the FiscalCalenderDate
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Debit Bal. Free Dfnd Crcy 6'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency6'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdDebitAmtInFreeDfndCrcy6' cast( cast( 1 as abap.dec(23,2)) as fis_dr_bal_esl ) as DebitAmountInFreeDefinedCrcy6,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Credit Bal. Free Dfnd Crcy 6'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency6'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdCreditAmtInFreeDfndCrcy6' cast( cast( 1 as abap.dec(23,2)) as fis_cr_bal_esl ) as CreditAmountInFreeDefinedCrcy6,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Ending Bal. Free Dfnd Crcy 6'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency6'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #LAST, 
                                      exceptionAggregationElements: ['FiscalPeriodDate']
//                                      exceptionAggregationElements: ['PostingDate']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdEndingBalInFreeDfndCrcy6' cast( cast( 1 as abap.dec(23,2)) as fis_end_bal_esl_ui ) as EndingBalAmtInFreeDfndCrcy6, 

// Hidden fields
@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency6'
@Consumption.hidden: true
EndingBalAmtInFreeDfndCrcy6 as IntmdEndingBalInFreeDfndCrcy6,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency6'
@Consumption.hidden: true
DebitAmountInFreeDefinedCrcy6 as IntmdDebitAmtInFreeDfndCrcy6,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency6'
@Consumption.hidden: true
CreditAmountInFreeDefinedCrcy6 as IntmdCreditAmtInFreeDfndCrcy6,



// Calculation of starting balance (FreeDefinedCurrency7) is done in the query for performance reasons
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Start. Bal. Free Dfnd Crcy 7' 
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency7'
@AnalyticsDetails.query.formula : '$projection.EndingBalAmtInFreeDfndCrcy7 - $projection.DebitAmountInFreeDefinedCrcy7 - CreditAmountInFreeDefinedCrcy7' 
cast( cast( 1 as abap.dec(23,2)) as fis_start_bal_fsl_ui ) as StartingBalAmtInFreeDfndCrcy7,  


// Debit and Credit Balance has an exception aggregation to make sure that LedgerFiscalYear is part of the select list
// this is necessary to push down the aggregation to the lowest level, since LedgerFiscalYear is part of the join condition of the FiscalCalenderDate
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Debit Bal. Free Dfnd Crcy 7'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency7'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdDebitAmtInFreeDfndCrcy7' cast( cast( 1 as abap.dec(23,2)) as fis_dr_bal_fsl ) as DebitAmountInFreeDefinedCrcy7,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Credit Bal. Free Dfnd Crcy 7'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency7'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdCreditAmtInFreeDfndCrcy7' cast( cast( 1 as abap.dec(23,2)) as fis_cr_bal_fsl ) as CreditAmountInFreeDefinedCrcy7,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Ending Bal. Free Dfnd Crcy 7'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency7'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #LAST, 
                                      exceptionAggregationElements: ['FiscalPeriodDate']
//                                      exceptionAggregationElements: ['PostingDate']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdEndingBalInFreeDfndCrcy7' cast( cast( 1 as abap.dec(23,2)) as fis_end_bal_fsl_ui ) as EndingBalAmtInFreeDfndCrcy7,  

// Hidden fields
@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency7'
@Consumption.hidden: true
EndingBalAmtInFreeDfndCrcy7 as IntmdEndingBalInFreeDfndCrcy7,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency7'
@Consumption.hidden: true
DebitAmountInFreeDefinedCrcy7 as IntmdDebitAmtInFreeDfndCrcy7,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency7'
@Consumption.hidden: true
CreditAmountInFreeDefinedCrcy7 as IntmdCreditAmtInFreeDfndCrcy7,



// Calculation of starting balance (FreeDefinedCurrency8) is done in the query for performance reasons
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Start. Bal. Free Dfnd Crcy 8' 
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency8'
@AnalyticsDetails.query.formula : '$projection.EndingBalAmtInFreeDfndCrcy8 - $projection.DebitAmountInFreeDefinedCrcy8 - CreditAmountInFreeDefinedCrcy8' 
cast( cast( 1 as abap.dec(23,2)) as fis_start_bal_gsl ) as StartingBalAmtInFreeDfndCrcy8,  


// Debit and Credit Balance has an exception aggregation to make sure that LedgerFiscalYear is part of the select list
// this is necessary to push down the aggregation to the lowest level, since LedgerFiscalYear is part of the join condition of the FiscalCalenderDate
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Debit Bal. Free Dfnd Crcy 8'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency8'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdDebitAmtInFreeDfndCrcy8' cast( cast( 1 as abap.dec(23,2)) as fis_dr_bal_gsl ) as DebitAmountInFreeDefinedCrcy8,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Credit Bal. Free Dfnd Crcy 8'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency8'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdCreditAmtInFreeDfndCrcy8' cast( cast( 1 as abap.dec(23,2)) as fis_cr_bal_gsl ) as CreditAmountInFreeDefinedCrcy8,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Ending Bal. Free Dfnd Crcy 8'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'FreeDefinedCurrency8'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #LAST, 
                                      exceptionAggregationElements: ['FiscalPeriodDate']
//                                      exceptionAggregationElements: ['PostingDate']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdEndingBalInFreeDfndCrcy8' cast( cast( 1 as abap.dec(23,2)) as fis_end_bal_gsl_ui ) as EndingBalAmtInFreeDfndCrcy8,  

// Hidden fields
@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency8'
@Consumption.hidden: true
EndingBalAmtInFreeDfndCrcy8 as IntmdEndingBalInFreeDfndCrcy8,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency8'
@Consumption.hidden: true
DebitAmountInFreeDefinedCrcy8 as IntmdDebitAmtInFreeDfndCrcy8,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'FreeDefinedCurrency8'
@Consumption.hidden: true
CreditAmountInFreeDefinedCrcy8 as IntmdCreditAmtInFreeDfndCrcy8,


// Calculation of starting balance (BalanceTransactionCurrency) is done in the query for performance reasons
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Start. Bal. in Bal Trans Crcy' 
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'BalanceTransactionCurrency'
@AnalyticsDetails.query.formula : '$projection.EndingBalanceAmtInBalTransCrcy - $projection.DebitAmountInBalanceTransCrcy - CreditAmountInBalanceTransCrcy' 
cast( cast( 1 as abap.dec(23,2)) as fis_start_bal_tsl_ui ) as StartingBalAmtInBalTransCrcy,  


// Debit and Credit Balance has an exception aggregation to make sure that LedgerFiscalYear is part of the select list
// this is necessary to push down the aggregation to the lowest level, since LedgerFiscalYear is part of the join condition of the FiscalCalenderDate
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Debit Bal. in Bal Trans Crcy'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'BalanceTransactionCurrency'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdDebitAmtInBalTransCrcy' cast( cast( 1 as abap.dec(23,2)) as fis_dr_bal_tsl ) as DebitAmountInBalanceTransCrcy,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Credit Bal. in Bal Trans Crcy'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'BalanceTransactionCurrency'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdCreditAmtInBalTransCrcy' cast( cast( 1 as abap.dec(23,2)) as fis_cr_bal_tsl ) as CreditAmountInBalanceTransCrcy,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Ending Bal. in Bal Trans Crcy'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'BalanceTransactionCurrency'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #LAST, 
                                      exceptionAggregationElements: ['FiscalPeriodDate']
//                                      exceptionAggregationElements: ['PostingDate']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdEndingBalInBalTransCrcy' cast( cast( 1 as abap.dec(23,2)) as fis_end_bal_tsl_ui ) as EndingBalanceAmtInBalTransCrcy,  

// Hidden fields
@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'BalanceTransactionCurrency'
@Consumption.hidden: true
EndingBalanceAmtInBalTransCrcy as IntmdEndingBalInBalTransCrcy,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'BalanceTransactionCurrency'
@Consumption.hidden: true
DebitAmountInBalanceTransCrcy as IntmdDebitAmtInBalTransCrcy,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'BalanceTransactionCurrency'
@Consumption.hidden: true
CreditAmountInBalanceTransCrcy as IntmdCreditAmtInBalTransCrcy,


// Calculation of starting balance (TransactionCurrency) is done in the query for performance reasons
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Start. Bal. Trans Crcy' 
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'TransactionCurrency'
@AnalyticsDetails.query.formula : '$projection.EndingBalanceAmtInTransCrcy - $projection.DebitAmountInTransCrcy - CreditAmountInTransCrcy' 
cast( cast( 1 as abap.dec(23,2)) as fis_start_bal_wsl_ui ) as StartingBalanceAmtInTransCrcy,  


// Debit and Credit Balance has an exception aggregation to make sure that LedgerFiscalYear is part of the select list
// this is necessary to push down the aggregation to the lowest level, since LedgerFiscalYear is part of the join condition of the FiscalCalenderDate
@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Debit Bal. Trans Crcy'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'TransactionCurrency'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdDebitAmtInTransCrcy' cast( cast( 1 as abap.dec(23,2)) as fis_dr_bal_wsl ) as DebitAmountInTransCrcy,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Credit Bal. Trans Crcy '
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'TransactionCurrency'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #SUM, 
                                      exceptionAggregationElements: ['LedgerFiscalYear']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdCreditAmountInTransCrcy' cast( cast( 1 as abap.dec(23,2)) as fis_cr_bal_wsl ) as CreditAmountInTransCrcy,  

@AnalyticsDetails.query.axis: #COLUMNS
@AnalyticsDetails.query.hidden: true
//@EndUserText.label: 'Ending Bal. Trans Crcy'
@DefaultAggregation: #FORMULA
@Semantics.amount.currencyCode: 'TransactionCurrency'
@AnalyticsDetails: {
        exceptionAggregationSteps: [{ exceptionAggregationBehavior : #LAST, 
                                      exceptionAggregationElements: ['FiscalPeriodDate']
//                                      exceptionAggregationElements: ['PostingDate']
                                   }]
    }
@AnalyticsDetails.query.formula : '$projection.IntmdEndingBalanceInTransCrcy' cast( cast( 1 as abap.dec(23,2)) as fis_end_bal_wsl_ui ) as EndingBalanceAmtInTransCrcy,

// Hidden fields
@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'TransactionCurrency'
@Consumption.hidden: true
EndingBalanceAmtInTransCrcy as IntmdEndingBalanceInTransCrcy,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'TransactionCurrency'
@Consumption.hidden: true
DebitAmountInTransCrcy as IntmdDebitAmtInTransCrcy,

@AnalyticsDetails.query.axis: #COLUMNS
@EndUserText.label: 'Dont look at this column'
@DefaultAggregation: #SUM
@Semantics.amount.currencyCode: 'TransactionCurrency'
@Consumption.hidden: true
CreditAmountInTransCrcy as IntmdCreditAmountInTransCrcy


  
}                                                                                                                                                                                     

 
```