---
name: feedback-cl-formatting
description: "CL program formatting preferences — compact style, 80-char max line width"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: ab57b7d1-001d-4075-a650-7cd9a8406a42
---

Write CL programs in compact style with a maximum line width of 80 characters.

**Why:** User prefers condensed, readable CL source that fits traditional SEU/ACS source width without excessive whitespace or parameter spreading.

**How to apply:**
- Max 80 characters per line
- Do not spread parameters across many lines just for alignment
- Keep related parameters on the same line where they fit
- Use ` +` continuation only when the line genuinely won't fit
- Indent continued lines to align with the opening of the parameter value, not excessively deep
- Comments should be brief, single-line `/* ... */` style
- Never embed SQL strings inside RUNSQL parameter values — use RUNSQLSTM with a source member instead
- Do not use RUNSQL for multi-line SQL; the CL quote doubling and line continuation cause syntax errors
- RUNSQLSTM is one compiled CL program — the SQL source member is not a second program

**Why:** Embedding SQL in RUNSQL string parameters requires quote doubling and CL line continuation which mangles the SQL and causes cryptic SQL9010 errors. User wants clean, maintainable CL with no embedded SQL strings.
