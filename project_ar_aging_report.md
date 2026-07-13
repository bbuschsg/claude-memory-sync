---
name: project-ar-aging-report
description: "AR Aging Report — standalone bare-URL page at Custom/SG/ARAgingReport.php; TRUE point-in-time aging via ARYPTD payment history (2026-07-13)"
metadata: 
  node_type: memory
  type: project
  originSessionId: 8df42ef1-aaff-465a-a3c6-67b46a251c10
---

`ARAgingReport.php` lives directly under `Custom\SG\` (not a subfolder) on both environments:
- SG5: `https://portal.screen-graphics.com:5610/Custom/SG/ARAgingReport.php`
- EIP: `https://portal.screen-graphics.com:5601/Custom/SG/ARAgingReport.php`

It is **not in the native Harris menu system (SYPORT/SYURLM)** — it's linked from Bill's custom `sg_portal_landing.php` dashboard (SG Reports › Accounting) and is also directly bookmarkable on its own. Either way, it must NOT depend on portal-navigation session variables ($headInclude/$genericHead/Banner.php/SgReportNav.php/$baseTable/$contentTable/$_sgnHome/$_sgnBv/$_sgnEid) — attempting to wrap it in that scaffolding to match PORR's look caused a same-day blank-page outage (2026-07-06) because those variables weren't reliably populated for this page's invocation path. Fixed by reverting to a fully self-contained `<!DOCTYPE html>` page while keeping the PORR visual styling built directly into it. See [[feedback_bare_url_vs_menu_reports]].

## TRUE point-in-time aging (2026-07-13)
Reworked from a "re-bucket today's balances" model to a genuine historical snapshot. The old "Age As Of" picker only re-bucketed a fixed set of *current* balances client-side, so a past date still showed future-dated invoices (Bill's report: "age as of June 1 shows July invoices") and used today's balance, not the balance as it stood then.
- **Balance as of D** = `IVIVAM - SUM(ARYPTD.YPAMT where YPBDAT <= D)`, joined `HDINVC.IVISEQ = ARYPTD.YPISEQ`; include only invoices with `IVIVDT <= D`.
- **`SGHDSDATA.ARYPTD`** = A/R Payment History (YP prefix). `YPBDAT` = Date Paid (CYMD), `YPAMT` = signed transaction amount (reversals negative, corrections included), `YPPMID` = type ( =Pmt/R=Rev/C=Cor). Verified live: `SUM(YPAMT)` per invoice reproduces `HDINVC.IVNPOS` for 100% of 74,215 invoices — so YPAMT is authoritative, don't use YPIAMT+YPDAMT (99.1% only).
- Picker now **reloads with `?asof=YYYY-MM-DD`** (server recomputes) instead of pure client re-bucket — Bill approved the reload. Default = today, where output equals the old current-open query exactly (validated: today 1,564 rows/$1,992,040.44 == old; June 1 1,621 rows/$2,217,699, 0 July leaks).
- `$asOfCymd` is server-validated from `$_GET['asof']` then inlined into SQL (safe). `IVPDFL` = Date Paid In Full also exists on HDINVC if ever needed.
- ODBC schema discovery done as profile `bill` from Windows (192.168.120.40) — that's how the data model above was confirmed.

## Data & features
- Query: `SGHDSDATA.HDINVC` LEFT JOIN `SGHDSDATA.HDCUST`, LEFT JOIN dated-payment subquery on `ARYPTD` (see point-in-time section); filter = reconstructed balance `<> 0` AND `IVIVDT <= asof`.
- Aging buckets (Current/1-30/31-60/61-90/90+) computed client-side in JS from the "Age As Of" date (which now also drives the server balance reconstruction), split against each invoice's due date.
- Detail / Summary view toggle, sortable columns, per-customer subtotals + grand total.
- Credit filter buttons: **All Invoices / Exclude Credits / Credits Only** — "credit" = `invAmt < 0`.
- **Avg Days to Pay** shown on each customer's subtotal line and in the Excel export, sourced from `HDCUST.CMT#DY` (running total days to pay) ÷ `CMT#IV` (running total # payments) — a HarrisData-maintained per-customer average covering ALL of that customer's invoices, not just the open ones this report queries. Guarded by a `SYSCOLUMNS` existence check on `HDCUST` before the main query touches those columns, so a wrong column name can't break the report.
- Export to Excel is a **hand-rolled pure-JS OpenXML/ZIP writer** (no PHP CSV, no libraries) — produces a real `.xlsx` matching the on-screen filtered/sorted view including subtotals. Deliberately kept instead of converting to the standard PHP `?export=csv` pattern used elsewhere.

## Visual standard (as of 2026-07-06)
Redesigned to match [[reference_sg_report_design]] (gradient title bar, blue auto-refresh bar w/ 10-min countdown, gray/blue table palette, purple Refresh + green Export buttons) — but as a **self-contained `<!DOCTYPE html>` page**, not wrapped in the portal's `$baseTable`/`td.content` structure, because it isn't menu-invoked.

[[feedback_bare_url_vs_menu_reports]]
[[feedback_backup_before_execute]]
