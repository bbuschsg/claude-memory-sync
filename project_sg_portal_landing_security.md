---
name: sg-portal-landing-security
description: "sg_portal_landing.php is the shared 2nd-level menu file ($reportMap[portal][cat]) - adding a report is a code edit here, no SYPORT/SYURLM rows needed. Per-user SYPGMS security described below was NOT present as of 2026-07-07 - verify before relying on it."
metadata: 
  node_type: memory
  type: project
  originSessionId: a03caaf8-d31b-4026-bcee-2d979f2091e5
---

**UPDATE 2026-07-07:** Checked both `W:\HarrisData\SG5\Custom\SG\sg_portal_landing.php` and the EIP copy (byte-identical to each other) while adding the Open PO Variance Report - neither contains a `sgCanSee()` function, a `'pgm'` key on any `$reportMap` item, or any `SYPGMS` reference. The security layer below may have been reverted or never fully merged. Treat the "Program ID Map" and "Adding a New Item" steps as unconfirmed until re-verified against the live file.

**Confirmed current mechanism for adding a new report to an existing category:** `$reportMap` is keyed `[portal][cat] => array of ['title','desc','file']`. To add a report, add one array entry under the right portal/cat key - no SYPORT/SYURLM/SYPORR/SYROLD/SYPGMO changes needed for items within an already-registered category (the landing page itself was registered once; see [[project-eip-menu-structure]]). Back up the file in both SG5 and EIP Backup Files before editing (naming convention: `sg_portal_landing_MMDDHHMM.php`), edit SG5 first, confirm with Bill, then mirror the identical edit to the EIP copy. See [[open-po-variance-report]] for a worked example.

# sg_portal_landing.php — Per-Item Security via SYPGMS

**Implemented 2026-06-23.** The landing page was showing all items to all users regardless of Program Option Security settings. Fixed by adding SYPGMS check.

## How It Works

- User profile: `$_SERVER['PHP_AUTH_USER']` (HTTP Basic Auth)
- Security table: `SGHDSDATA.SYPGMS` — `SPUSER` + `SPPGID` + `SPOP01` ('Y'/'N')
- Rule: hide item only when `SPOP01='N'` explicitly. Missing row or DB error = allow (show).
- Each item in `$reportMap` has a `'pgm'` key → program ID in SYPGMS

## Program ID Map

| Item | pgm (SYPGMS SPPGID) |
|---|---|
| MO Daily Labor Report | MODLYLBR |
| MO Material Components Issues | MOMATLCMP |
| Customer Service Inquiry | CSSRVINQ |
| Bookings Dashboard | BOOKDASH |
| Shipments Dashboard | SHIPSDASH |
| Sales Dashboard | SALESDASH |
| Inventory Data Integrity Dashboard | INVDATINT |
| CS Data Integrity Dashboard | CSDATINT |
| Manufacturing Order Requirements | MOREQ |
| Open Order Line Item Comments | OPENORDLC |

## Security Tables

- **HDSSTDPGM.SYPGMO** — program option DEFINITIONS: `SOPGID`, `SOMOPT` (1), `SOMDES`, `SORESV`. Registration only.
- **SGHDSDATA.SYPGMS** — per-user access: `SPUSER`, `SPPGID`, `SPOP01`–`SPOP15`. SPOP01='Y' = allow, 'N' = deny.
- Rows seeded by PushAllMenusLive.php Step 8 (all users, all pgms, SPOP01='Y' default).
- Admin controls via HarrisData "Program Option Security Maintenance" screen (MOREQ = the program being secured, not the maintenance screen's own name).

## Adding a New Item

When adding a new report to `$reportMap` in `sg_portal_landing.php`:
1. Add `'pgm' => 'PGMID'` to the item array
2. Register PGMID in HDSSTDPGM.SYPGMO (PushAllMenusLive.php Step 7 pattern)
3. Seed SGHDSDATA.SYPGMS rows for all users (PushAllMenusLive.php Step 8 pattern)
4. Then use Program Option Security Maintenance to restrict specific users

## Key Function

```php
function sgCanSee($pgmId) {
    // Returns false only when SYPGMS.SPOP01='N' for current user + pgmId
    // Missing row or DB error = allow (true)
}
```
