---
name: project-ar-aging-report
description: "AR Aging Report — standalone bare-URL page at Custom/SG/ARAgingReport.php, redesigned to SG standard 2026-07-06"
metadata: 
  node_type: memory
  type: project
  originSessionId: 8df42ef1-aaff-465a-a3c6-67b46a251c10
---

`ARAgingReport.php` lives directly under `Custom\SG\` (not a subfolder) on both environments:
- SG5: `https://portal.screen-graphics.com:5610/Custom/SG/ARAgingReport.php`
- EIP: `https://portal.screen-graphics.com:5601/Custom/SG/ARAgingReport.php`

It is **not in the native Harris menu system (SYPORT/SYURLM)** — it's linked from Bill's custom `sg_portal_landing.php` dashboard (SG Reports › Accounting) and is also directly bookmarkable on its own. Either way, it must NOT depend on portal-navigation session variables ($headInclude/$genericHead/Banner.php/SgReportNav.php/$baseTable/$contentTable/$_sgnHome/$_sgnBv/$_sgnEid) — attempting to wrap it in that scaffolding to match PORR's look caused a same-day blank-page outage (2026-07-06) because those variables weren't reliably populated for this page's invocation path. Fixed by reverting to a fully self-contained `<!DOCTYPE html>` page while keeping the PORR visual styling built directly into it. See [[feedback_bare_url_vs_menu_reports]].

## Data & features
- Query: `SGHDSDATA.HDINVC` (open/partial invoices only, `IVIVAM-IVNPOS <> 0`) LEFT JOIN `SGHDSDATA.HDCUST`.
- Aging buckets (Current/1-30/31-60/61-90/90+) computed entirely client-side in JS from an "Age As Of" date picker, so the split recalculates live without a re-query.
- Detail / Summary view toggle, sortable columns, per-customer subtotals + grand total.
- Credit filter buttons: **All Invoices / Exclude Credits / Credits Only** — "credit" = `invAmt < 0`.
- **Avg Days to Pay** shown on each customer's subtotal line and in the Excel export, sourced from `HDCUST.CMT#DY` (running total days to pay) ÷ `CMT#IV` (running total # payments) — a HarrisData-maintained per-customer average covering ALL of that customer's invoices, not just the open ones this report queries. Guarded by a `SYSCOLUMNS` existence check on `HDCUST` before the main query touches those columns, so a wrong column name can't break the report.
- Export to Excel is a **hand-rolled pure-JS OpenXML/ZIP writer** (no PHP CSV, no libraries) — produces a real `.xlsx` matching the on-screen filtered/sorted view including subtotals. Deliberately kept instead of converting to the standard PHP `?export=csv` pattern used elsewhere.

## Visual standard (as of 2026-07-06)
Redesigned to match [[reference_sg_report_design]] (gradient title bar, blue auto-refresh bar w/ 10-min countdown, gray/blue table palette, purple Refresh + green Export buttons) — but as a **self-contained `<!DOCTYPE html>` page**, not wrapped in the portal's `$baseTable`/`td.content` structure, because it isn't menu-invoked.

[[feedback_bare_url_vs_menu_reports]]
[[feedback_backup_before_execute]]
