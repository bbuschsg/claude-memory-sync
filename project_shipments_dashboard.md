---
name: project-shipments-dashboard
description: Shipments Dashboard built 2026-06-14 — inserted between Bookings and Sales in SG Dashboards/OE portal
metadata: 
  node_type: memory
  type: project
  originSessionId: b547e46f-7207-48d7-95bf-2137b067323c
---

Shipments Dashboard completed 2026-06-14. Ready to test on Monday 2026-06-16.

**Why:** User wanted a third dashboard between Bookings and Sales showing daily shipment activity and invoice totals.

**How to apply:** Dashboard is live at `https://portal.screen-graphics.com:5610/Custom/SG/Order%20Entry/ShipmentsDashboard.php`. If issues arise during testing, backups are at `W:\HarrisData\SG5\Custom\SG\Order Entry\*_backup_20260614_181215.php`.

## Files

| File | Action |
|------|--------|
| `W:\HarrisData\SG5\Custom\SG\Order Entry\ShipmentsDashboard.php` | Created |
| `W:\HarrisData\SG5\Custom\SG\sg_portal_landing.php` | Updated — added Shipments entry |
| `W:\HarrisData\SG5\Custom\SG\Order Entry\BookingsDashboard.php` | Nav only — added Shipments + Sales links |
| `W:\HarrisData\SG5\Custom\SG\Order Entry\SalesDashboard.php` | Nav only — added Shipments link |

## Data Sources

- **Shipped Today:** `SGHDSDATA.OEORHD` + `OEORDT` — filter `OESHDT = todayCymd`, `ODORST = 'O'`, `ODQSTC <> 0`; amount = `ODQSTC × ODSLPR`
- **Invoice Totals:** `SGHDSDATA.OEORDH` + `OEORHD` — filter `DHDTLI` in range; amount = `DHSLPR × DHQSTC ÷ DHORUF` (same formula as SalesDashboard)

## Layout

1. **Top 2-column:** Orders Shipped Today detail table (left, order# clickable via SelectOrder.d2w) + Shipped Today LCD + chart (right)
2. **2×2 quad-cells:** Day/Week/Month/Year Invoice LCDs each paired with bar chart by salesperson
3. **Detail table:** Invoice totals by salesperson with drilldown to SalesDrilldown.php
4. **Auto-refresh:** M–F 7am–6pm ET, every 15 min
