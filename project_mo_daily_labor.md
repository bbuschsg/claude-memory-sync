---
name: mo-daily-labor-report
description: MO Daily Labor Report — SGINQ/MFG portal; CTE SQL joining HDMLDM/HDMOHM/HDMWCM/HREMPL; built 2026-06-19
metadata: 
  node_type: memory
  type: project
  originSessionId: c5d10f50-ad15-4b08-a57b-a821bb97692a
---

MO Daily Labor Report built 2026-06-19 in SG5 first.

**Why:** Replace SEQUEL modlylbrf report with interactive web dashboard.

**How to apply:** When user asks to update or extend this report, edit `W:\HarrisData\SG5\Custom\SG\Manufacturing\MODailyLaborReport.php`. EIP copy goes to the matching path under `W:\HarrisData\EIP\Custom\SG\Manufacturing\`.

- File: `W:\HarrisData\SG5\Custom\SG\Manufacturing\MODailyLaborReport.php`
- Portal entry: SGINQ → MFG (first entry in sg_portal_landing.php)
- SQL: CTE (WITH BASE AS) joining SGHDSDATA.HDMLDM T01, HDMOHM T02, HDMWCM T03, HREMPL T04 (LEFT JOIN on LDEMP=EMEMPL)
- EMRNAM comes from SGHDSDATA.HREMPL
- WHERE T01.LDDATE = CURRENT DATE
- VARIANCE and VARCOST computed in outer SELECT referencing STDHRS/LDSUHR from BASE CTE
- MO# link uses same openMO() pattern as MOMaterialComponents.php (port 5601 for SG5)
- Auto-refresh 10 min, M-F 7am-5pm ET; manual Refresh button top-right
- Export CSV preserves current filter params
- Primary/default sort: MO # (col index 3, `state.col = 3` in JS)
- Backup of sg_portal_landing.php: sg_portal_landing_backup_20260619_134843.php

## Update 2026-07-13
- Added two columns after MO # and before Work Ctr #: **DEPT** (`HDMWCM.WCDEPT`) and **Dept Name** (`PREXAC.EANAME`), via new `LEFT JOIN SGHDSDATA.PREXAC T05 ON TRIM(T03.WCDEPT)=TRIM(T05.EADEPT)`. Table is now 22 columns.
- **PREXAC is the HarrisData department-name master** (standard table, not custom): `EADEPT` CHAR(5) = dept #, `EANAME` CHAR(20) = dept name. Dept 209 has no PREXAC row so the join must be LEFT. Reusable anywhere WCDEPT/LDDEPT needs a readable name.
- Filters are now all dropdowns (per [[feedback-filters-dropdowns]]): Emp # and Emp Name (both from HDMLDM+HREMPL distinct), Dept # and Dept Name (from HDMWCM+PREXAC distinct), plus existing Work Center. Removed the old free-text Emp # input; Emp # filter is now exact `T01.LDEMP = <int>`. MO # left as free-text (unchanged, not requested).
- Verified query against live DB (07/13/2026 data) before deploying; DEPT/Dept Name populate correctly.
- Note: editing files on the `W:` network share via the Edit tool intermittently fails "string not found" on a byte-identical match; simply retry the same edit and it applies.
- Toolbar restyled to the locked SG standard (matches PORequirementsReport.php): gray-gradient title bar with cyan "Back to EIP" + red "Logout", solid `#2563EB` refresh bar, purple Refresh stacked above green Export in a right column. Back-to-EIP link = `$eiBase/Welcome.php?baseVar=BaseConfiguration.icl&eID=...&portal=9999999999`. Kept it a STANDALONE page (NOT wrapped in Banner.php/SgReportNav.php scaffolding) per [[feedback-bare-url-vs-menu-reports]] — it filters server-side so it needs the full-page form.
- Auto-filters on any control change (form submit); Apply button removed. Refresh interval now 15 min (`$refreshSecs=900`).
- Filters now: Date, Emp Name, MO # (free-text), Dept #, Dept Name, Work Ctr. Employee # filter was added then removed per Bill (2026-07-13) — Emp Name dropdown kept.
- Confirmed good by Bill 2026-07-13.
