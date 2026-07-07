---
name: syporr-prsel-fix
description: "SYPORR PRSEL field must be 'Y' for portals to appear in EIP left nav; blank PRSEL silently hides portals in whitelist mode"
metadata: 
  node_type: memory
  type: project
  originSessionId: a03caaf8-d31b-4026-bcee-2d979f2091e5
---

# SYPORR PRSEL='Y' Required for Portal Visibility

**Fix confirmed 2026-06-23.** When PushAllMenusLive.php added SYPORR rows for the 5 SG portals (with PRSEL='Y'), all roles entered whitelist mode. FixMissingSyporr.php then added SYPORR rows for native portals but set PRSEL='' (blank). Menu.php/GetMenu.php filter `AND PRSEL='Y'` — so native portals were invisible even though PRSEQ matched and SYURLM existed.

**Why:** FixMissingSyporr.php INSERT statement had `PRSEL=''` instead of `PRSEL='Y'` for the native portal rows it created.

**How to apply:** Any time a portal is invisible in whitelist mode but has a matching SYPORR row, check PRSEL first. The fix is a simple UPDATE:
```sql
UPDATE SGHDSDATA.SYPORR SET PRSEL='Y'
WHERE PRROLE='role' AND RTRIM(PRPAGE)='' AND RTRIM(PRSEL)<>'Y'
```

## Key EIP Menu System Facts (confirmed by reading Menu.php source)

- **Bypass mode:** `COUNT(*) FROM SYPORR WHERE PRROLE=role = 0` → shows ALL SYROLD portals, no SYPORR join
- **Whitelist mode:** ANY SYPORR rows exist → inner join on `PRPORT=FPPORT AND PRPAGE=FPPAGE AND PRSEQ=FPSEQ` AND `PRSEL='Y'`
- **The visibility field is PRSEL, NOT PRTSPT** — PRTSPT is irrelevant to menu rendering
- **SYURLM join uses FUID=FPID** (SYPORT.FPID directly) — NOT a '/PORTAL' suffix
- **Never modify SYURLM** to add /PORTAL entries — that table is HDS-managed; breaks upgrades
- **SYPORR top-level rows** (PRPAGE='') control portal tab visibility in left nav
- **SYPORR sub-item rows** (PRPAGE=portal code) control sub-category visibility within a portal

## Diagnostic Tool

`DiagGetMenu.php` — runs the exact GetMenu SQL and shows FPSEQ vs PRSEQ vs PRSEL for every SYROLD portal. Use this whenever portal visibility issues arise.
URL: `https://portal.screen-graphics.com:5601/Custom/SG/DiagGetMenu.php?role=ROLENAME`

## Fix Tool

`FixEnapolesPrsel.php` — sets PRSEL='Y' on top-level SYPORR rows where blank. Written for ENAPOLES but the UPDATE pattern applies to any role.
URL: `https://portal.screen-graphics.com:5601/Custom/SG/FixEnapolesPrsel.php`
