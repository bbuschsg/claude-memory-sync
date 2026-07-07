---
name: project-wp-program-comparison
description: "Waste Pro Program Sheet Pricing & Sales report — build history, DB fields, portal location, known issues fixed"
metadata: 
  node_type: memory
  type: project
  originSessionId: 590b129d-d841-4e4b-80f5-cb5b21c14f11
---

# Waste Pro — Program Sheet Pricing & Sales

**Status:** Live on EIP as of 2026-07-01

## Files

| File | Location |
|------|----------|
| PHP (SG5 test) | `W:\HarrisData\SG5\Custom\SG\Management\WpProgramComparison.php` |
| PHP (EIP live) | `W:\HarrisData\EIP\Custom\SG\Management\WpProgramComparison.php` |
| Backup (SG5) | `WpProgramComparison_bak_20260701.php` |
| Backup (EIP portal) | `sg_portal_landing_bak_20260701.php` |
| Artifact HTML source | `C:\Users\Bill\AppData\Local\Temp\claude\C--Users-Bill\590b129d...\scratchpad\wp-comparison.html` |

## Portal Entry

- **Portal:** SGMGMT
- **Category:** CUSTOMER
- **Title:** Waste Pro — Program Sheet Pricing & Sales
- **Opens:** new window (`'target' => '_blank'`)
- **Both servers:** SG5 and EIP `sg_portal_landing.php` updated

## What It Does

Two-tab report:
- **Tab 1 — Program Sheet Pricing:** Side-by-side 2024 vs 2026 program sheet prices per item/qty break, color-coded by % change, with actual sales data (# orders, avg units/order, total units, avg $/pc, year total) per year
- **Tab 2 — All WP Items Sales Drill-Down:** Every WP customer class item invoiced 2024/2025/2026, same column structure, sortable, searchable, export to Excel

## DB Query — Correct Field Names

**Tables:** `SGHDSDATA.OEORDH d` (detail) JOIN `SGHDSDATA.OEORHD h` (header) LEFT JOIN `SGHDSDATA.HDCUST cust`

**Key fields:**
- Item #: `d.DHITEM`
- Description: `d.DHIMDS`
- Invoice date: `d.DHDTLI` (CYMD format — e.g. 1240101 = 2024-01-01)
- Qty shipped: `d.DHQSTC`
- Sales $: `d.DHSLPR * d.DHQSTC / d.DHORUF` (never use DTUPRC * DTQSHP)
- Order #: `d."DHORD#"` / `h."OEORD#"`
- Exclude header rows: `d."DHSEQ#" <> 0`
- Customer class: `cust.CMCCLS` via `h.OESHTO = cust.CMCUST`

**CYMD year boundaries:**
- 2024: `DHDTLI >= 1240101 AND DHDTLI < 1250101`
- 2025: `DHDTLI >= 1250101 AND DHDTLI < 1260101`
- 2026: `DHDTLI >= 1260101 AND DHDTLI < 1270101`

**Order counts:** Run as a SEPARATE query (`COUNT(DISTINCT d."DHORD#")` grouped by item + year bucket). Do NOT use `COUNT(DISTINCT CASE WHEN...)` — IBM i DB2 may reject it.

## RAW Array Format (JavaScript)

11-element array per item: `[item, desc, qty24, sal24, ord24, qty25, sal25, ord25, qty26, sal26, ord26]`

Indices: 0=item, 1=desc, 2=qty24, 3=sal24, 4=ord24, 5=qty25, 6=sal25, 7=ord25, 8=qty26, 9=sal26, 10=ord26

## Bugs Fixed During Build

1. **Wrong tables** — Old files used `OEORHH`/`OEORDT` (fields like HHINDT, HHCLAS, DTQSHP). Correct tables are `OEORDH`/`OEORHD` matching every other SG report.
2. **Wrong date field** — `YEAR(H.HHINDT)` does not exist. Use CYMD range on `d.DHDTLI`.
3. **Wrong sales formula** — `DTUPRC * DTQSHP` is wrong. Use `DHSLPR * DHQSTC / DHORUF`.
4. **COUNT(DISTINCT expr)** — IBM i may reject `COUNT(DISTINCT CASE WHEN...)`. Split into a separate query using `COUNT(DISTINCT d."DHORD#")`.

## UI Standards Applied

- Gray gradient title bar, #2563EB tab bar, #374151 column headers (white bold)
- Year columns color-coded: purple (2024), amber (2025), green (2026)
- Price change color key: green < 3%, yellow 3-10%, orange 10-20%, red > 20%
- Amber callout box for † pricing note
- Drill-down: alternating gray rows, blue hover, sortable headers
- Tab 1: `table-layout:fixed`, wrapped header text, 220px description column
- Filter bar: search left, italic count center, green Export button right
- Columns per year: # Ord | Avg Units/Ord | Total Units | Avg $/pc | Year Total Sales
