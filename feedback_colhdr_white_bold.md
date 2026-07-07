---
name: feedback-colhdr-white-bold
description: Column header text on all SG report tables must always be bright white and bold
metadata: 
  node_type: memory
  type: feedback
  originSessionId: a8ce3d39-0a43-42c6-9d38-6bf8e034acce
---

All `<th>` column headers in SG portal report tables must always render with **bright white, bold text** — no exceptions.

**Why:** User explicitly requested this as a standing standard for all report tables (2026-06-27).

**How to apply:** Add the following CSS to every report page's `<style>` block, using the table's scoped ID prefix to target only that report's headers and avoid colliding with portal globals:

```css
#PREFIX-grid thead th {
    color: #ffffff !important;
    font-weight: bold !important;
}
```

The `!important` is required to override the HarrisData portal's global `colhdr` class styles. If a custom background color is also set on the header (e.g. `background-color:#03a9fc`), white text is still the rule — verify the background provides enough contrast (dark/medium colors work; avoid very light backgrounds).

[[feedback-refresh-bar-ui]]
