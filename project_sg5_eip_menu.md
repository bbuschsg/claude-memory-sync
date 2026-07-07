---
name: project-sg5-eip-menu
description: "HarrisData EIP menu system — SYPORT/SYURLM/SYROLD/SYPGMO table structure, URL token patterns, FPID field types"
metadata: 
  node_type: memory
  type: project
  originSessionId: 63989d97-e916-4fc0-a72d-3c2ea14bb089
---

EIP navigation menus are stored in IBM i DB2 tables SYPORT and SYURLM (linked on SYPORT.FPID = SYURLM.FUID).

**Why:** Learned this while registering MORequirements.php in the SGCUSTRPT portal (June 2026).

**Key facts:**
- FPID and FUID are CHAR fields, not INTEGER — always quote them in SQL: `WHERE FPID = '42'`
- @@phpPath resolves to `/` (just a slash), NOT `/Custom/`
- @@homeURL resolves to `https://portal.screen-graphics.com:5601` (Live) or `https://portal.screen-graphics.com:5610` (Test/SG5)
- Correct FUURL pattern for a page under Custom/: `@@homeURL@@phpPathCustom/SG/Planning/MORequirements.php`
- FPPAGE='' makes an entry a top-level nav item (TYPE=1) — avoid this for sub-items
- FPPAGE must mirror the Ship Dash row's FPPAGE value to make a proper sub-item in SGCUSTRPT

**SG Custom Reporting portal:**
- FPPORT = `SGCUSTRPT`
- Portal description field is blank (auto-detection by description will fail)

**How to apply:** When registering any new page in SGCUSTRPT, use quoted FPID/FUID values, hardcode URL as `@@homeURL@@phpPathCustom/<subpath>.php`, and copy FPPAGE from an existing sub-item (e.g. Ship Dash row).

## Three-Table EIP Menu System
- **SYURLM** — URL Master: defines the actual URL/page/report to call
- **SYPORT** — Portal Master: groups and sequences URL IDs into portal menu entries (two visible levels: top nav + flyout sub-menu)
- **SYROLD** — Role Detail: assigns portals to roles, controlling which users see what

EIP natively supports only 2 menu levels. True 3-level nesting requires a custom PHP landing page that acts as the "folder" — it lists links to individual reports within a sub-category.

## SYPGMO Registration (Required for Every Program)
Every program built must be inserted into **SYPGMO**:
- Opt Seq: `1`
- Option Description: `View`

**SYPGMO location:** Lives in the program libraries, NOT the data schemas.
- Test: `SG5STDPGM.SYPGMO`
- Live: `HDSSTDPGM.SYPGMO`
- Never look for it in `S5HDSDATA` or `SGHDSDATA` — it won't be there.

[[project-eip-menu-structure]]
[[project-harrisdata-workflow]]
