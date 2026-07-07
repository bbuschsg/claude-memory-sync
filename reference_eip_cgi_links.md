---
name: reference-eip-cgi-links
description: HarrisData EIP CGI program URL patterns for opening orders/invoices/customers/MOs in a new tab
metadata: 
  node_type: memory
  type: reference
  originSessionId: 6a4e2c4a-c237-42e5-9db5-37c985654812
---

CGI programs live at `/harris-CGI/<Program>.d2w/REPORT` (or `/DISPLAY`) and use `baseVar=BaseConfiguration.icl` (`.icl`, not `.php`).

## Open Invoice (AR)
```
https://<host>/harris-CGI/SelectInvoice.d2w/DISPLAY
  ?formatToPrint=Y
  &baseVar=BaseConfiguration.icl
  &portal=CUSTOMER
  &eID=<session eID>
  &customerNumber=<IVBLTO / CMCUST>
  &invoiceDate=<IVIVDT CYMD integer, e.g. 1250820 for 2025-08-20>
  &invoiceNumber=<IVAINV>
```
Note: `invoiceDate` is the raw CYMD integer from HDINVC.IVIVDT (not a date string).

## Open Customer Order (OEORST = 'A' — active)
```
https://<host>/harris-CGI/SelectOrder.d2w/REPORT
  ?baseVar=BaseConfiguration.icl
  &portal=CUSTOMER
  &eID=<session eID>
  &customerName=<CMCNA1 — URL-encoded>
  &customerNumber=<OESHTO / CMCUST>
  &orderNumber=<order number>
```

## Open Order History (OEORST = 'C' — closed)
```
https://<host>/harris-CGI/SelectOrderHistory.d2w/REPORT
  ?baseVar=BaseConfiguration.icl
  &portal=CUSTOMER
  &eID=<session eID>
  &customerName=<CMCNA1 — URL-encoded>
  &customerNumber=<OESHTO / CMCUST>
  &orderNumber=<order number>
  &orderSequence=0
```
Note: `arInvoiceNumber` is NOT required — SelectOrderHistory works without it.
Note: `OEINVN` does NOT exist on OEORHD (SQLCODE=-205 confirmed 2026-06-14).

## Open Customer (ship-to)
```
https://<host>/harris-CGI/CustomerSelect.d2w/REPORT
  ?baseVar=BaseConfiguration.icl
  &portal=CUSTOMER
  &eID=<session eID>
  &customerName=<CMCNA1 — URL-encoded>
  &customerNumber=<OESHTO / CMCUST>
```
Note: program is `CustomerSelect.d2w` — NOT `SelectCustomer.d2w` (does not exist).

## Open Manufacturing Order
```
https://<host>/harris-CGI/SelectMfgOrder.d2w/REPORT
  ?baseVar=BaseConfiguration.icl
  &portal=MFGMGMT
  &eID=<session eID>
  &mfgOrder=<MO number, e.g. C292980>
  &plantNumber=1
```

## Implementation pattern (PHP report page)
```php
$eiBase = 'https://portal.screen-graphics.com:5601';  // always 5601 — CGI programs only exist on live EIP
```
```javascript
var EI_BASE = <?php echo json_encode($eiBase); ?>;
var EI_EID  = <?php echo json_encode($eID); ?>;
var OOLC_ROWS = [];   // populated by buildGrid(); use idx to avoid quote issues in onclick

function openOrder(idx) {
  var r = OOLC_ROWS[idx];
  window.open(EI_BASE + '/harris-CGI/SelectOrder.d2w/REPORT' +
    '?baseVar=BaseConfiguration.icl&portal=CUSTOMER&eID=' + EI_EID +
    '&customerName=' + encodeURIComponent(r.custName) +
    '&customerNumber=' + encodeURIComponent(r.shipTo) +
    '&orderNumber=' + encodeURIComponent(r.ordNum), '_blank');
}

function openMO(idx) {
  var r = OOLC_ROWS[idx];
  if (!r.moNum) return;
  window.open(EI_BASE + '/harris-CGI/SelectMfgOrder.d2w/REPORT' +
    '?baseVar=BaseConfiguration.icl&portal=MFGMGMT&eID=' + EI_EID +
    '&mfgOrder=' + encodeURIComponent(r.moNum) +
    '&plantNumber=1', '_blank');
}
```

**Key: use row-index pattern** — never put customer name directly in onclick string (apostrophes/ampersands break HTML attributes). Store sorted rows in a global array, pass integer index only.
