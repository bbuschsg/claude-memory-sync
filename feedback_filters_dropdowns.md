---
name: feedback-filters-dropdowns
description: "All filter bars on SG portal reports must use dropdowns (select), never free-text inputs"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: a8ce3d39-0a43-42c6-9d38-6bf8e034acce
---

All filter controls on SG portal report pages must be `<select>` dropdown elements populated from the actual query result data. Never use `<input type="text">` for filters.

**Why:** User's explicit standing standard — text inputs are wrong every time, dropdowns are always right.

**How to apply:**
1. After fetching `$rows`, collect unique sorted values for each filterable field in PHP
2. Render each filter as a `<select>` with "All" as the first option, then unique values
3. JS filters on the `change` event (not `input`)
4. Still follow the filter bar layout standard: dropdowns + Clear button left, italic count center, green Export button far right

[[feedback-filter-bar-ui]]
