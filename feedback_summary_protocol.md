---
name: feedback-summary-protocol
description: "When Bill asks for a summary, always produce inline copy-paste summary AND save full summary file to Project Notes folder, using the exact standard template below; append dated updates on later changes"
metadata:
  node_type: memory
  type: feedback
  originSessionId: c39e7754-c5b5-4cbc-82ff-ac33d0da9fa1
---

When the user asks for a summary of a task or project, ALWAYS do ALL THREE of these:

1. **Inline summary** - Produce a complete summary in the chat response, in the exact standard template below, so Bill can copy/paste it into Evernote. Must be fully self-contained with no assumed context.

2. **Project Notes file** - Write (or update) a full summary file at:
   `C:\Users\Bill\.claude\projects\C--Users-Bill\Project Notes\<ProjectName>.md`
   Same template, same content as the inline version.

3. **Append on later changes** - Once a project has a summary file, later changes get a new `## Update <YYYY-MM-DD>` section appended at the bottom (still using bold-label bullets for the specifics) rather than rewriting the whole doc. Only regenerate the whole doc top-to-bottom when the user asks to redo/reformat it.

**Why:** Bill uploads the Project Notes folder to GitHub and pastes summaries into Evernote for personal notes. Confirmed 2026-07-07 (Open PO Variance Report project) that Bill wants every summary in one fixed template, modeled on his existing "AR Aging Report" Evernote note — not a freeform recap.

**How to apply:** Any time the word "summary" appears in a request, or the user says "summarize what we did", produce output in exactly this Markdown structure:

```markdown
# <Project Name> - Project Summary

*Screen Graphics | EIP Portal Development | Completed <Month D, YYYY>*

---

**STATUS:** <one line - e.g. "Live on both SG5 (test) and EIP (production)">

Menu location: <full menu path, e.g. "EIP Portal > SG Reports > Accounting > AR Aging Report", or "N/A - standalone page" if not menu-registered>

---

## What Was Built

<1-3 sentence paragraph: what it is, what table(s)/data it's built from, any notable calculation approach>

**<Optional bold sub-heading for a key mechanic, e.g. "Aging Buckets (from Due Date)">**
- <bullet>
- <bullet>

## Features

- **<Feature label>:** <description>
- **<Feature label>:** <description>
  (cover: data source/row count if known, view modes, filters, date pickers, clickable links and where they go, export format/behavior, sorting, layout notes)

## URLs

- **SG5:** <test url>
- **EIP:** <live url>

**Process note:** <any non-obvious architecture/procedure finding worth remembering - omit line only if genuinely nothing notable>

**Backups made:** <filenames + locations>

**Open items:** <pending work, or "None">
```

Formatting rules:
- Title = "<Project Name> - Project Summary" as an H1
- Subtitle line is italic, always "Screen Graphics | EIP Portal Development | Completed <date>" (use the actual completion date; if not yet complete, adjust STATUS line accordingly, e.g. "In progress" and drop "Completed ..." from the subtitle or say "Started <date>")
- Horizontal rules (`---`) bracket the STATUS/Menu location block
- "What Was Built" and "Features" are H2 headings; "URLs" is also H2
- Feature bullets always lead with a **bold label:**
- Process note / Backups made / Open items are bold-label lines (not a heading), placed after the URLs section
- Keep it copy-paste ready for Evernote - no assumed context from the chat
