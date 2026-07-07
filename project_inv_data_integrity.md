---
name: project-inv-data-integrity
description: "Inventory Data Integrity Dashboard — 2 SEQUEL queries translated to DB2 SQL, SGDINT/INVMGMT portal, built 2026-06-16"
metadata:
  node_type: memory
  type: project
  originSessionId: current
---

Inventory Data Integrity Dashboard built 2026-06-16.

**File (SG5):** `W:\HarrisData\SG5\Custom\SG\Inventory Management\InvDataIntegrityDashboard.php`
**File (EIP):** `W:\HarrisData\EIP\Custom\SG\Inventory Management\InvDataIntegrityDashboard.php`
**Portal entry:** SGDINT > INVMGMT in `sg_portal_landing.php` (both SG5 and EIP)
**Test URL (SG5):** `https://portal.screen-graphics.com:5610/HarrisData/SG5/custom/SG/Inventory%20Management/InvDataIntegrityDashboard.php`

**Why:** Replace 2 SEQUEL View queries in a single auto-loading dashboard for Inventory data integrity checks.

## 2 Sections (all run at page load)

1. **Products W/Incorrect Inventory Type Code** — HDIMST JOIN HDPCLS on IMPCLS=PCPCLS; WHERE IMITC not in valid list OR inactive items not coded ZOS1/ZOS2 OR OS-class items not coded ZOS1/ZOS2; Correction Required column: OS1→ZOS1, OS2→ZOS2, blank IMITC→assign product class, else review
2. **Costing Errors** — HDIMST LEFT JOIN HDIPLT on IMITEM=IPITEM LEFT JOIN HDIWHS on IMITEM=IWITEM; WHERE active items (IMIMAC<>'I'), IPCEFL not blank, IPCSRC<>'Y'; Error Description CASE on IMPCLS (OS1/OS2/COS → add 3 cost records) and IPCEFL codes (K/L/N/P/W/X)

## Key fields
- HDIMST: IMITEM, IMIMDS, IMPCLS, IMITC (inventory type code), IMIMAC (active flag 'I'=inactive), IMTSUS
- HDPCLS: PCPCLS (product class key)
- HDIPLT: IPITEM, IPCEFL (cost error flag), IPCSRC (cost source — 'Y' = skip)
- HDIWHS: IWITEM, IWWHS (warehouse), IWOHQT (on hand qty)

## Correction Required logic (Q1)
- IMPCLS='OS1' → 'Change Inv Type Code to ZOS1'
- IMPCLS='OS2' → 'Change Inv Type Code to ZOS2'
- IMITC='' → 'You must assign a Product Class to this item!'
- else → 'Review Inventory Type Code'

## Error Description logic (Q2)
- IMPCLS IN (OS2, OS1, COS) → 'Add All 3 Cost Records & Set To Not Roll'
- IPCEFL='K' → 'Missing A Code In Routing'
- IPCEFL='L' → 'No Product Structure'
- IPCEFL='N' → 'No Material Cost For Component(s)'
- IPCEFL='P' → 'Prior Error In Component/s'
- IPCEFL='W' → 'Verify All Work Centers Numbers'
- IPCEFL='X' → 'No Costs Exist For This Item'
- else → 'Review COMPLETE Item/Process'

## Backups taken
- Portal landing (SG5 and EIP): `sg_portal_landing_backup_20260616_202706.php`
- EIP Inventory Management directory created new (didn't exist before)

[[project-harrisdata-workflow]]
[[project-sg5-server]]
[[project-cs-data-integrity]]
