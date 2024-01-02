---
{"dg-publish":true,"permalink":"/ag-work-flow/profit-center-actuals/"}
---

# Profit Center - Actuals

|   |   |
|---|---|
|Technical Name|C_PROFITCENTERQ2701|
|View Type|Consumption|
|Release Status|API State Visibility: Released|
## Purpose
This CDS view provides the prerequisites for answering the following business questions:
- What are the actual amounts per profit center and G/L account in transaction currency?
- What are the actual amounts per profit center and G/L account in company code currency?
- What are the actual amounts per profit center and G/L account in global currency?
For all three currency types, you can drill down for further relevant characteristics.
## Prerequisites
- This view is based on the CDS Cube I_JournalEntryItemCube.
- You need the following authorizations:
    - Ledger: F_FAGL_LDR (GLRLDNR)
    - Company Code: F_BKPF_BUK (BUKRS)
    - Financial Account Type: F_BKPF_KOA (KOART)
    - Business Area: F_BKPF_GSB (GSBER)
    - Segment: F_FAGL_SEG (SEGMENT)
- You must have specified your default controlling area (GET/SET parameter CAC).
## Structure
Business Objects
This view is built on the following business object:
- CDS Cube I_JournalEntryItemCube
Main input parameters
The main input parameters are:
- Ledger Fiscal Year
- Fiscal Period
- Company Code
- Segment
- Profit Center
- GL Account Hierarchy
- GL Account
Measures and attributes
Some important measures and attributes are:
- Measures
    - Actual Amount in Transaction Currency
    - Actual Amount in Company Code Currency
    - Actual Amount in Global Currency
    - Quantity
- Attributes
    - GL Account
    - Profit Center
    - Other relevant dimension are available as free characteristics.