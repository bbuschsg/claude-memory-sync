---
name: feedback-summary-protocol
description: "When Bill asks for a summary, always produce inline copy-paste summary AND save full summary file to Project Notes folder; append on changes"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: c39e7754-c5b5-4cbc-82ff-ac33d0da9fa1
---

When the user asks for a summary of a task or project, ALWAYS do ALL THREE of these:

1. **Inline summary** - Produce a complete summary in the chat response so Bill can copy/paste it into Evernote. Must be fully self-contained with no assumed context.

2. **Project Notes file** - Write (or update) a full summary file at:
   `C:\Users\Bill\.claude\projects\C--Users-Bill\Project Notes\<ProjectName>.md`
   Use a descriptive filename based on the project/task name.

3. **Append on changes** - Whenever changes or updates are made to a task/project, APPEND the new information to the existing summary file. Never overwrite prior summary content. Add a dated section header when appending (e.g. `## Update 2026-06-30`).

**Why:** Bill uploads the Project Notes folder to GitHub and uses Evernote for personal notes. Both need to stay current. Summaries that only exist in chat are lost.

**How to apply:** Any time the word "summary" appears in a request, or the user says "summarize what we did", trigger this full protocol automatically. The inline and file versions should contain the same core content.
