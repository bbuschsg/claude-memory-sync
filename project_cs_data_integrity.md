---
name: project-cs-data-integrity
description: "CS Data Integrity Dashboard — 6 SEQUEL queries translated to DB2 SQL, SGDINT/OE portal, built 2026-06-16"
metadata: 
  node_type: memory
  type: project
  originSessionId: 84d44276-6cf4-45bd-935e-e320f6a529e1
---

CS Data Integrity Dashboard built 2026-06-16.

**File:** `W:\HarrisData\SG5\Custom\SG\Order Entry\CSDataIntegrityDashboard.php`
**EIP copy:** `W:\HarrisData\EIP\Custom\SG\Order Entry\CSDataIntegrityDashboard.php`
**Portal entry:** SGDINT > OE in `sg_portal_landing.php` (both SG5 and EIP)
**Test URL (SG5):** `https://portal.screen-graphics.com:5610/HarrisData/SG5/custom/SG/Order%20Entry/CSDataIntegrityDashboard.php`

**Why:** Replace 6 SEQUEL View queries in a single auto-loading dashboard for Customer Service data integrity checks.

## 6 Sections (all run at page load)
1. **CO's with Duplicate PO #'s** — open orders in last 7 days grouped by bill-to/ship-to/OEORRF; excludes Q/P/V types, 'DAN OLIVE'
2. **Open Order Taxes** — open orders where OETSTX<>0 and taxes=invoice total OR tax code<>'T'; note "Review and check these orders"
3. **Open Order Credit Card Fees** — open orders with OECTRM IN (8,13); joins HDTRMS; excludes order 351079
4. **Customers With Bad Data** — HDCUST where missing order type / bill-to=0 / wrong loc code / missing zip; excludes CMLOC#=100
5. **Line Items With No Cost** — OEORDT open lines with ODCOST=0, non-Q type orders
6. **QM Product Class on Open Orders** — open OEORDT lines where ODPCLS='QM'; note to notify user when item becomes real

## Key fields used
- OEORHD: OEOSTP (recv timestamp), OETIVA, OETSTX/OETCTX/OETYTX/OETLC1/OETLC2/OETLC3 (tax fields), OECTXC, OETMSG (CC fee on order), OECTRM (terms), OELDTI (last invoice date CYMD), OEOSUS (original user)
- HDCUST: CMORTY (default order type), "CMLOC#" (location — has # needs quoting), CMBLTO (bill-to), CMCCLS (class), CMZIP, CMCTRY, CMSLSM
- HDTRMS: TMCTRM (terms key), TMCTDS (terms description)
- OEORDT: ODPCLS, ODCOST, ODTSTP (timestamp), ODOSUS (original user), ODQSTD

## SQL notes
- Q4 uses a PHP variable `$locCase` for the long location CASE to avoid duplication
- Q4 wrapped in subquery so LOCATION/ZIP/DFTORDTYP aliases can be referenced in WHERE
- OECTRM compared as numeric: `IN (8, 13)` not `IN ('8', '13')`
- CYMD dates handled in PHP with cymdToDate(); compared in SQL as integers using $cutoffCymd

[[project-harrisdata-workflow]]
[[project-sg5-server]]
