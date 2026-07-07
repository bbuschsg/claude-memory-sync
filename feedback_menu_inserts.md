---
name: feedback-menu-inserts
description: "Adding EIP menus/submenus — write PHP script following exact SYPORT/SYURLM CSV structure, backup both tables both DBs, one script per schema"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 2e03e5b8-baa6-4c1e-8f90-b463c7a45e7b
---

When Bill asks to add menus or submenus to EIP, write a PHP script — but it MUST follow the exact column structure from the SYPORT and SYURLM CSV exports Bill provides.

**Why:** On 2026-06-24, Claude ignored the actual table structure from Bill's CSV data and invented its own column formats/approach, causing broken inserts and a 500 error on both environments.

**How to apply:**
1. Use the SYPORT and SYURLM CSV exports as the authoritative column structure — match every column exactly
2. The PHP script must back up SYPORT and SYURLM in BOTH S5HDSDATA and SGHDSDATA before making any changes
3. Write ONE combined script per schema — test (S5HDSDATA) first, live (SGHDSDATA) second — so Bill can always run test first and verify before touching Live
4. Use WHERE NOT EXISTS on every INSERT so the script is safe to re-run
5. Use CURRENT_TIMESTAMP for timestamp fields rather than hardcoding values from the CSV

[[project-sg5-eip-menu]]
[[project-eip-menu-structure]]
[[feedback-backup-before-execute]]
