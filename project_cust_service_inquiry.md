---
name: project-cust-service-inquiry
description: "Customer Service Inquiry screen — single search field, SGHDSDATA order files + OEOUDT, working as of 2026-06-16"
metadata: 
  node_type: memory
  type: project
  originSessionId: a69757d0-3c2c-4ba2-8447-d1614d176f36
---

Customer Service Inquiry screen built 2026-06-16, working confirmed same day.

**File:** `W:\HarrisData\SG5\Custom\SG\Order Entry\CustServiceInquiry.php`
**Test URL:** `https://portal.screen-graphics.com:5610/HarrisData/SG5/custom/SG/Order%20Entry/CustServiceInquiry.php`
**Portal entry:** SGINQ > OE in `sg_portal_landing.php`

**Why:** SEQUEL query replacement — CS reps needed to look up orders by phone, DOT, asset, and other user-defined fields without SEQUEL.

## OEOUDT table structure (confirmed)
- `OUORD` = order number (join key to OEORDT.ODORD#)
- `OULINE` = line number (join key to OEORDT.ODORL#)
- `OUFLDR` = numeric field (shows as .000000 when zero; strip decimals in PHP with `(int)` cast — do NOT use SQL `INT()` as it breaks on NULLs from LEFT JOIN)
- `OUFLDV` = character value field (phone numbers, DOT numbers, asset numbers, names, etc. — this is the main search field)

## JOIN pattern
```sql
LEFT JOIN SGHDSDATA.OEOUDT u ON u.OUORD = d."ODORD#" AND u.OULINE = d."ODORL#"
```
Must be LEFT JOIN — INNER JOIN excludes orders with no OEOUDT records.

## Search logic
Single field searches OR across: OUFLDV, OUFLDR (CHAR), ODIMDS, ODITEM, OEORRF, CMCNA1.
Do NOT use AND between fields — no results come back.

## Key lessons
- `INT(u.OUFLDR)` in SQL breaks query when OUFLDR is NULL from LEFT JOIN — strip decimals in PHP instead
- `sequeldbf.OEOUDT` is wrong library — use `SGHDSDATA.OEOUDT`
- OUFLDR is NOT the join key to ODORD# — that's OUORD

[[project-sg5-server]]
[[project-harrisdata-workflow]]
