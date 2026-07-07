---
name: filter-bar-ui
description: "Filter bar layout standard for SG Reports — filters+Clear left, bold count right, Refresh+Export in separate right panel"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 5a606553-42ca-4228-8daa-4adbfc327f20
---

## For REPORTS (confirmed 2026-06-30 against PORequirementsReport.php)

Two-section layout separated by `border-left:2px solid #E86200`:

**Left section** (`flex:1`, two bars stacked):
- Bar 1: Refresh status bar (see [[feedback_refresh_bar_ui]])
- Bar 2: Filter bar — `[Filter label + dropdown] [Filter label + dropdown] [Clear button]` on the left; `<b id="...-fcount-text" style="margin-left:auto;">N items</b>` on the right. Dropdowns are ordered LEFT TO RIGHT to match their column order in the table.

**Right panel** (`border-left:2px solid #E86200`, flex column, centered):
- Top: `↻ Refresh` button — purple (#7B1FA2), bold, 12px
- Bottom: `↓ Export to Excel` button/link — green (#1DA032), bold, 12px

Count is `<b>` (bold), NOT `<em>` (italic). Count uses `margin-left:auto` to push it to the right of the filter bar.
Refresh and Export are NEVER inside the filter bar — always in the right panel.

## For DASHBOARDS
Dashboards do not have a separate right panel. Export (if present) goes far right in the filter bar with `margin-left:auto`.

**Why:** Bill confirmed the PORR (PORequirementsReport.php) layout is the definitive standard for all Reports. Previous memory had Export in the filter bar — that was wrong for Reports.

**How to apply:** Any time a new Report is built, use PORR as the template. Never put Refresh or Export inside the filter bar for Reports.

See [[feedback_refresh_bar_ui]] for the refresh bar standard.
