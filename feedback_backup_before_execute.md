---
name: feedback-backup-before-execute
description: "Must back up ALL affected files and DB tables before executing any script or making any change — no exceptions, ever"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 63989d97-e916-4fc0-a72d-3c2ea14bb089
---

Back up EVERY file AND every affected database table BEFORE making any change or executing any script — no exceptions, ever. Do not write the install script and then tell the user to run it — stop first, create the backup, confirm it is in place, then proceed.

**Why:** User explicitly requires this. Repeated incidents of changes being made without backups first. Non-negotiable rule.

**How to apply:**
- **IFS file backups:** Claude creates the backup copy himself with the Write tool BEFORE editing — never delegate to user. Copy to `W:\HarrisData\SG5\Custom\Backup Files` (Test) or `W:\HarrisData\EIP\Custom\Backup Files` (Live).
- **IFS backup filename MUST include both date AND time** — `filename_MMDDHHmm.php`, e.g. `sg_portal_landing_06241435.php`. Date-only names like `sg_portal_landing_0624.php` are NOT acceptable — the user cannot trust which version to restore if multiple backups exist on the same day.
- **DB table backups:** Claude provides `CREATE TABLE ... AS (SELECT * FROM ...) WITH DATA` SQL and user runs it in ACS — BEFORE any INSERT/DELETE/UPDATE.
- **IBM i DB backup library is always SGOBJBACK** — SGOBJ is for working files only, NOT backups. Never back up into S5HDSDATA, SGHDSDATA, or program libraries.
- IBM i table name rules: NO underscores, max 10 characters, must be meaningful.
- Naming convention: source table name + MMDD date suffix, e.g. `SYURLM0615`, `SYPORT0615`. (DB table names are too short to include time — file backups must use the full datetime format.)
- Example: `CREATE TABLE SGOBJBACK.SYURLM0615 AS (SELECT * FROM S5HDSDATA.SYURLM) WITH DATA`
- The sequence is always: **Backup → Verify backup exists → Execute**. Never Execute before Backup.
- This applies even when the change is "safe" or reversible — always back up first without exception.
- **The backup download action MUST be at the very TOP of every PHP script, before any echo/HTML output.** If it comes after HTML output, PHP's `header()` calls silently fail and no file is downloaded.
- Pattern: `if (isset($_GET['action']) && $_GET['action'] === 'backup') { /* query, build lines, header(), echo, exit; */ }` — this block must be line 1 after the require_once calls, before any echo.

[[project-harrisdata-workflow]]
