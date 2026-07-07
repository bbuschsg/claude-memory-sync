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
- Filters: Emp # (CHAR(LDEMP) LIKE), MO # (RTRIM(LDORD) LIKE), Work Center (RTRIM(LDWC) LIKE) — all contain-match
- Auto-refresh 10 min, M-F 7am-5pm ET; manual Refresh button top-right
- Export CSV preserves current filter params
- Primary/default sort: MO # (col index 3, `state.col = 3` in JS)
- Backup of sg_portal_landing.php: sg_portal_landing_backup_20260619_134843.php
