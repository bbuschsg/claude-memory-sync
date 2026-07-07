---
name: project-mo-open-balance
description: MO Open Balance Report — Manufacturing orders with received qty but not closed; Action badges per status/balance
metadata: 
  node_type: memory
  type: project
  originSessionId: 5a606553-42ca-4228-8daa-4adbfc327f20
---

MO Open Balance Report built 2026-06-30.

**File:** `W:\HarrisData\SG5\Custom\SG\Manufacturing\MOOpenBalanceReport.php`
**EIP copy needed** at: `W:\HarrisData\EIP\Custom\SG\Manufacturing\MOOpenBalanceReport.php`

**Source table:** `SGHDSDATA.HDMOHM`

**SQL logic (CTE):** Base query computes STATUS (Active/Tagged/N/A from OHSTC) and BALANCE (OHCQTY - OHQTYR). Outer query computes ACTION from STATUS+BALANCE aliases. ORDER BY ACTION DESC, LRDATE ASC, STATUS ASC, ORDNO ASC.

**Filters:** Status dropdown, Action dropdown. Both populated from live query data.

**Action badge colors:**
- "Check & Verify" = amber (#fff3e0 / #8b4000 / #E86200 border)
- "Close" = blue (#e3f0ff / #1840A8 border)
- "Final Tag & Close" = green (#e8f5e9 / #1DA032 border)

**Balance Due:** Red + bold if != 0.

**Clickable links:**
- Order # -> `SelectMfgOrder.d2w` with mfgOrder=$ord, plantNumber=1, portal=MFGMGMT
- Item # -> `ItemSelect.d2w` with itemNumber=$item, portal=ITEM

**Auto-refresh:** 10 min, M-F 7am-6pm CT.

**Why:** Track open MO balances and determine action (verify, close, or final tag & close) for orders that have received qty but aren't fully closed.

**How to apply:** When user asks about this report, check the SG5 file above. Remember to copy to EIP when user is ready to deploy.
