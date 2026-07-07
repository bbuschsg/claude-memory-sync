---
name: project-eip-restore
description: "EipRestore.php — fully working as of 2026-06-15; correct field values, SYPORR, HD_ALL_SG bypass rule"
metadata: 
  node_type: memory
  type: project
  originSessionId: 017c9e0e-f327-49e8-a846-cf1bf8c7a884
---

EipRestore.php (`W:\HarrisData\SG5\Custom\SG\EipRestore.php`) is the idempotent restore script for SG5 test after any S5HDSDATA refresh. As of 2026-06-15 it is fully working and confirmed with both bbusch/hd_all_sg and sg5test/custsrvc roles.

**Why:** S5HDSDATA was accidentally refreshed from SGHDSDATA on 2026-06-14, wiping all SG custom EIP portal configuration. The restore script rebuilds it from scratch.

**Run URL:** `https://portal.screen-graphics.com:5610/Custom/SG/EipRestore.php?confirm=RESTORE`

**How to apply:** Run after any S5HDSDATA refresh or Harris upgrade. Step 0 DELETEs all SG portal rows first, so re-running is safe and always produces a clean rebuild.

## What it does (8 steps)

| Step | Table | Rows | Key facts |
|---|---|---|---|
| 0 | SYURLM, SYPORT, SYROLD, SYPORR | DELETE | Clears all SG rows first |
| 1 | SYURLM portal-level | 5 | FUID=PCODE/PORTAL, FUTRGT='', FURESV='', FUTSPT='Y' |
| 2 | SYURLM sub-items | 30 | FUTRGT='_blank', FURESV='Y', FUDESCU='', FUTSPT='' |
| 3 | SYPORT top-level | 5 | FPID=PCODE/PORTAL, FPDESC='', FPRESV='', FPTSPT='Y' |
| 4 | SYPORT sub-items | 30 | FPSEQ=1-6 (NOT 10-60), FPDESC=cdesc, FPRESV='', FPTSPT='' |
| 5 | SYROLD | 210 | 42 roles × 5 portals |
| 6 | SYPGMO | 8 | In SG5STDPGM, not S5HDSDATA |
| 7 | SYPGMS | 392 | 49 users × 8 programs, SPOP01='Y' |
| 8 | SYPORR | 1,435 | 41 roles × 5 portals × 7 rows — **HD_ALL_SG excluded** |

## CRITICAL: HD_ALL_SG bypass rule

HD_ALL_SG is a special bypass role. It must have **zero SYPORR rows**. If any SYPORR rows exist for HD_ALL_SG, the system switches from bypass mode (sees ALL portals) to filtered mode (sees ONLY portals with SYPORR entries). This was confirmed on 2026-06-15 when adding SYPORR for HD_ALL_SG broke it to show only the 5 SG portals.

The `$bypassRoles = ['HD_ALL_SG']` array in Step 8 controls this exclusion.

## SYPORR structure (7 rows per role per portal)

```
Top-level row: PRROLE, PRPORT, PRPAGE='', PRSEQ=1, PRID='ROLE/PCODE', PRSEL='Y'
Sub-item rows: PRROLE, PRPORT, PRPAGE=pcode, PRSEQ=1-6, PRID='ROLE/PCODE/PCODE/N.00', PRSEL='Y'
```

## Confirmed working backups (2026-06-15 18:39)

- IFS: `W:\HarrisData\SG5\Custom\Backup Files\EipRestore_backup_20260615D.php`
- DB: SGOBJBACK.SYURLM615W, SYPORT615W, SYROLD615W, SYPORR615W, SYPGMS615W, SYPGMO615W

## Pending

Promote to live SGHDSDATA/HDSSTDPGM after all SG5 testing is complete.

[[project-sg5-eip-menu]]
[[project-eip-menu-structure]]
[[feedback-backup-before-execute]]
