---
name: feedback-never-assume
description: Never assume root cause or state — always diagnose with actual data first
metadata: 
  node_type: memory
  type: feedback
  originSessionId: a03caaf8-d31b-4026-bcee-2d979f2091e5
---

Never assume. Always diagnose with actual data before drawing conclusions or writing fixes.

**Why:** Repeated incorrect assumptions about why ENAPOLES couldn't see portals (PRTSPT theory, session cache theory, SYURLM theory, etc.) wasted significant time and frustrated the user. Every assumption turned out to require revision when actual data came back.

**How to apply:** Before proposing any fix or explanation:
1. Write a diagnostic script that queries the actual DB tables
2. Get the user to run it and share results
3. Read the results
4. THEN write the fix

Never say "I believe the cause is X" or "this is probably Y" or "the screenshot was likely from..." without data to back it up. If you don't know, say "I don't know — run this diagnostic first."
