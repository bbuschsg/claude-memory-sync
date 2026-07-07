---
name: project-eip-menu-structure
description: "Custom EIP menu structure — 5 top-level SG menus, 6 submenus each, placement after Event Calendar"
metadata: 
  node_type: memory
  type: project
  originSessionId: 63989d97-e916-4fc0-a72d-3c2ea14bb089
---

Five custom top-level menus must exist in EIP, each with six identical submenus. Add them **after Event Calendar** in the portal navigation order.

## Menu Structure

| Top-Level Menu | Submenus (same for all 5) |
|---|---|
| SG Inquiries | Accounting, Inventory Management, Manufacturing, Order Entry, Planning, Purchasing |
| SG Dashboards | Accounting, Inventory Management, Manufacturing, Order Entry, Planning, Purchasing |
| SG Data Integrity | Accounting, Inventory Management, Manufacturing, Order Entry, Planning, Purchasing |
| SG Reports | Accounting, Inventory Management, Manufacturing, Order Entry, Planning, Purchasing |
| SG SOP's | Accounting, Inventory Management, Manufacturing, Order Entry, Planning, Purchasing |

## EIP Menu Implementation
- Top-level items → SYPORT rows (portal nav entries)
- Sub-menu items → SYPORT rows pointing to SYURLM URLs
- Since EIP natively supports only 2 levels (top nav + flyout sub-menu), a sub-category like "Planning" uses a custom PHP landing page as the "folder" — the landing page lists the individual report links
- Landing pages live in the appropriate Custom/SG/ subdirectory on the IFS
- Role assignment → SYROLD table controls which users see each portal item

## How to Apply
When adding a new report/inquiry/dashboard:
1. Determine which top-level menu and subcategory it belongs to
2. Add SYURLM row for the URL (use `@@homeURL@@phpPathCustom/SG/<category>/<filename>.php`)
3. Add SYPORT sub-item row linking to that URL under the correct parent menu
4. Register in SYPGMO (Opt Seq 1, Option Description: View)
5. Do SG5 first, then EIP — identical structure in both

[[project-harrisdata-workflow]]
[[project-sg5-eip-menu]]
