---
name: reserved-roles-no-sg-menus
description: "Never add SG custom portals/menus to HarrisData system-reserved roles (Rsv='Y' in SYROLM) unless Bill explicitly says to"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: a03caaf8-d31b-4026-bcee-2d979f2091e5
---

Never include SG custom menus (SGINQ, SGDASH, SGDINT, SGRPT, SGSOP) in HarrisData system-defined reserved roles unless Bill explicitly requests it.

**Why:** PushAllMenusLive.php added SG portals to ALL roles including reserved ones (HD_ALL, HD_CUST, HD_EMPL, HD_HRADMIN, HD_MOBILE, HD_SECURE, HD_SLSM, HD_VEND). This put them in whitelist mode and broke their native portal navigation entirely. Reserved roles are HarrisData-managed and must not be touched by SG customizations.

**How to apply:**
- Reserved roles = any role where `RLRESV='Y'` in `SGHDSDATA.SYROLM`
- When writing any push/deploy scripts that loop over roles, always exclude reserved roles by querying SYROLM or explicitly checking against the known reserved list
- Do NOT add SYPORR rows, SYROLD entries, or SYPGMS rows for reserved roles as part of SG custom menu deployments
- If a script already broke reserved roles, use `RevertReservedRoles.php` to fix them

**Confirmed reserved roles (RMRESV='Y', verified 2026-06-24):** HD_ALL, HD_CUST, HD_EMPL, HD_HRADMIN, HD_MOBILE, HD_SECURE, HD_SLSM, HD_VEND. HD_FIX is NOT reserved. HD_ALL_SG is NOT reserved (it is the SG bypass role).

**SYROLM confirmed columns:** RMRESV (reserved flag), role code column unconfirmed but likely RMROLE. Description column exists. To query reserved roles dynamically: `SELECT RTRIM(RMROLE) FROM SGHDSDATA.SYROLM WHERE RTRIM(RMRESV)='Y'` (verify RMROLE with SELECT * first).
