---
name: project-harrisdata-workflow
description: "HarrisData EIP development rules — SG5 test first, backup before changes, SEQUEL replacement goal"
metadata: 
  node_type: memory
  type: project
  originSessionId: 63989d97-e916-4fc0-a72d-3c2ea14bb089
---

**Always work in SG5 (Test) FIRST, then promote to EIP (Live).** Never start in Live.

**Why:** User explicitly requires this order to prevent untested code from hitting production data.

**How to apply:** Every file, SQL change, and menu entry goes to SG5 first. Only after confirmed working do you duplicate to EIP. Live and Test must always have identical menu structure and items.

## Copy-to-Both Rule — NON-NEGOTIABLE
After every change to any SG5 custom PHP file, **immediately copy it to the matching EIP path** — every single time, without being asked.
- SG5 path: `W:\HarrisData\SG5\Custom\SG\`
- EIP path:  `W:\HarrisData\EIP\Custom\SG\`
- Then **always provide both test URLs** in the response (no exceptions):
  - SG5 Test:  `https://portal.screen-graphics.com:5610/Custom/SG/<relative-path>.php`
  - EIP Live:  `https://portal.screen-graphics.com:5601/Custom/SG/<relative-path>.php`

**Why:** User was frustrated that files were sometimes only deployed to SG5 and not EIP — this was inconsistent and required manual follow-up every time.

## Backup Rule — NON-NEGOTIABLE
Back up EVERY file AND every affected database table BEFORE making any change or executing any script — no exceptions, ever.
- Test backups → `W:\HarrisData\SG5\Custom\Backup Files`
- Live backups → `W:\HarrisData\EIP\Custom\Backup Files`
- Sequence is always: **Backup → Verify backup exists → Execute**
- For DB changes: dump affected tables as INSERT statements before running any INSERT/DELETE/UPDATE
- For file changes: copy the existing file to Backup Files before editing
- Do NOT tell the user to run a script until the backup is already created and confirmed

[[feedback-backup-before-execute]]

## SEQUEL Viewpoint Replacement
The long-term goal is to eliminate Fortra SEQUEL Viewpoint entirely. All new reports and dashboards are built as PHP pages in HarrisData EIP. When asked to convert a SEQUEL report, replace it completely — do not wrap or call SEQUEL from PHP.

## New Window Rule
Every EIP menu item, when clicked, must open in a new browser window/tab.

## Program Registration (SYPGMO)
Every program built must be inserted into the Harris program table **SYPGMO** with:
- Opt Seq: `1`
- Option Description: `View`

[[project-eip-menu-structure]]
[[project-sg5-server]]
