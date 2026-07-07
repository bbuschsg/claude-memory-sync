---
name: feedback-never-assume-timezone
description: "Never hardcode/assume a timezone for user-facing time displays - always derive from the viewer's own PC/browser clock"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: d840f1da-16cb-4866-8358-4ce59052affc
---

Never set or assume a timezone for any time value a user will see (refresh timestamps, "last updated" badges, countdowns, "as of" dates). Always derive these from the viewer's own PC/browser clock via plain JS `new Date()` — with no `timeZone` option and no `toLocaleString`/`toLocaleTimeString` conversion to a hardcoded IANA zone. Whatever the viewer's OS reports IS the correct time for them.

**Why:** Built the Installs & Removals Report with `date_default_timezone_set('America/Chicago')` on the PHP side (copying the codebase-wide convention) and a hardcoded `America/Chicago`-then-`America/New_York` conversion in the JS refresh-countdown logic. Both were wrong: Bill is in Eastern time, and hardcoding *any* specific zone (even after "fixing" it to Eastern) was still the wrong approach per Bill's explicit correction — the fix isn't "pick the right zone," it's "don't pick a zone at all, read the PC's clock." This caused multiple rounds of visibly wrong "Last refresh" times and a wrong refresh-target countdown before Bill called it out directly.

**Confirmed fact (2026-07-07, not an assumption — stated directly by Bill):** the IBM i server itself physically resides in the Eastern time zone and will not move. This matters only for *server-side* date math where there's no browser to ask (e.g. computing "today" for a default month-to-date filter range, or a CSV export filename timestamp) — for those specific server-side cases, `date_default_timezone_set('America/New_York')` is now a correct, confirmed setting, not a guess. See [[project_sg5_server]] — the codebase-wide standard header actually uses `America/Chicago`, which appears to be a longstanding inaccuracy across every existing custom report, not a deliberate convention to imitate.

**How to apply:**
- Any timestamp/countdown/date badge rendered for the user to look at → JS `new Date()`, no timezone conversion, ever.
- Any date used only in server-side logic where "what day is it right now" matters for a default/filename and there's no browser involved → `America/New_York` is factually correct for this server, safe to use.
- If asked to fix a timezone-display bug, don't just swap one hardcoded zone for another — check whether the value should come from the browser instead.
