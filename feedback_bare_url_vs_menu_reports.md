---
name: feedback-bare-url-vs-menu-reports
description: "Not every custom SG report page can safely depend on portal-navigation scaffolding ($headInclude, Banner.php, etc.) — check invocation path first"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 8df42ef1-aaff-465a-a3c6-67b46a251c10
---

Before wrapping a custom SG report page in the [[reference_sg_report_design]] portal-embedded structure (`require_once $headInclude`/`$genericHead`/`Banner.php`/`SgReportNav.php`, `$baseTable`/`$contentTable` table wrapper, `$_sgnHome`/`$_sgnBv`/`$_sgnEid` for the Back-to-EIP link), verify the page is actually reachable ONLY through Harris' native menu-click flow (SYPORT/SYURLM), which is what populates those variables via `GenericDirectCallVariables.php` reading the request's `baseVar`/`eID`/`portal` query params.

Some reports (e.g. [[project_ar_aging_report]]) are instead linked from Bill's custom `sg_portal_landing.php` dashboard and/or directly bookmarked — if the query params aren't guaranteed present, `require_once($headInclude)` silently fails (fatal error with output suppressed) and the page goes **completely blank with no visible error**, which is very hard to diagnose without server log access.

**Why:** Applying the PORR standard blindly to `ARAgingReport.php` on 2026-07-06 broke it in exactly this way — it had always been a self-contained `<!DOCTYPE html>` page, and adding the portal scaffolding introduced an undefined-variable dependency it never had before.

**How to apply:**
- Get the full PORR-style visual design (gradient title bar, refresh bar, table palette, button styling) by building it directly into a self-contained `<!DOCTYPE html>`/`<body>` page — none of it actually requires the portal scaffolding, just the CSS/HTML/JS.
- Only add the `$headInclude`/`Banner.php`/`$baseTable` dependency chain for pages confirmed to be registered in SYPORT/SYURLM and always opened via that menu click path.
- If a "blank page, no error" report comes in after a redesign, suspect a fatal on an undefined portal variable first — check what got added that wasn't there before, rather than guessing at unrelated causes.
