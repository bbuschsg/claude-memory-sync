---
name: project-management-screens
description: "SG Management portal screens — RevenueVsGoal, NewAccountsRevenue, BottomHalfRevenue, CustClassSales5Yr — built 2026-06-25/26"
metadata: 
  node_type: memory
  type: project
  originSessionId: 3cd56d4c-3b32-425c-a44c-da218de638f8
---

Four management screens built 2026-06-25/26, all deployed to SG5 and EIP.

**Portal:** SGMGMT (SG Management)
**SG5 path:** `W:\HarrisData\SG5\Custom\SG\Management\`
**EIP path:** `W:\HarrisData\EIP\Custom\SG\Management\`

## Screens

### RevenueVsGoal.php
- **Purpose:** YTD invoiced revenue vs. annual company goal, broken down by customer class
- **Goal:** Hardcoded `$GOAL = 18300000.00` in PHP (not user-editable)
- **Data:** SGHDSDATA.OEORDH + OEORHD + HDCUST + HDCCLS
- **Amount formula:** `DHSLPR * DHQSTC / DHORUF` (same as Sales/Shipments dashboards)
- **Filter:** `DHSEQ# <> 0`, `DHQSTC <> 0`, `DHDTLI` in YTD range
- **Test URL:** `https://portal.screen-graphics.com:5610/Custom/SG/Management/RevenueVsGoal.php`
- **Live URL:** `https://portal.screen-graphics.com:5601/Custom/SG/Management/RevenueVsGoal.php`

### NewAccountsRevenue.php
- **Purpose:** YTD revenue from new accounts vs. a user-configurable goal
- **Goal:** Persisted in `nagoal.json` (primary) or `/tmp/nagoal_sg.json` (fallback); default $4,000,000; editable via on-screen UI (AJAX POST)
- **New account definition:** Based on HDCUST.CMDFES (customer first-established date) falling within current year
- **Data:** SGHDSDATA.OEORDH + OEORHD + HDCUST + HDSALM (salesperson)
- **Test URL:** `https://portal.screen-graphics.com:5610/Custom/SG/Management/NewAccountsRevenue.php`
- **Live URL:** `https://portal.screen-graphics.com:5601/Custom/SG/Management/NewAccountsRevenue.php`

### BottomHalfRevenue.php
- **Purpose:** Identifies lowest-revenue customers — ranks all customers by prior full-year revenue, shows bottom half with current YTD vs. prior YTD comparison
- **Ranking:** `ROW_NUMBER() OVER (ORDER BY REV_LY_FULL ASC)` on prior full year; bottom half = RNK <= TOTAL/2
- **Columns:** Customer, Class, Salesperson, Prior Full Year, Prior YTD, Current YTD
- **Data:** SGHDSDATA.OEORDH + OEORHD + HDCUST + HDSALM
- **Test URL:** `https://portal.screen-graphics.com:5610/Custom/SG/Management/BottomHalfRevenue.php`
- **Live URL:** `https://portal.screen-graphics.com:5601/Custom/SG/Management/BottomHalfRevenue.php`

### CustClassSales5Yr.php
- **Purpose:** Revenue by customer class across a rolling 5-year period (YTD through today's calendar date each year), plus MTD and Today columns; year-over-year % change columns
- **Years:** `$yr-4` through `$yr` (current) — always 5 years, auto-rolls each year
- **Columns:** Class, Class Desc, YR-4, YR-3, YR-2, YR-1, YR (current YTD), MTD, Today, plus % change between each adjacent year pair
- **Data:** SGHDSDATA.OEORDH + OEORHD + HDCUST + HDCCLS
- **Test URL:** `https://portal.screen-graphics.com:5610/Custom/SG/Management/CustClassSales5Yr.php`
- **Live URL:** `https://portal.screen-graphics.com:5601/Custom/SG/Management/CustClassSales5Yr.php`

## Common SQL pattern
All four use `DHSLPR * DHQSTC / DHORUF` for invoice amount (guard `DHORUF <> 0`), filter `DHSEQ# <> 0` and `DHQSTC <> 0`, and use CYMD date arithmetic for range comparisons.

## Backups
Timestamped backups exist in both SG5 and EIP Backup Files directories from 2026-06-25 and 2026-06-26.

[[project-harrisdata-workflow]]
[[project-sg5-server]]
