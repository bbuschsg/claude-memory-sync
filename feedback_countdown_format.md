---
name: feedback-countdown-format
description: "Refresh countdown format: D days HH:MM:SS — omit days/hours if zero; applies to all SG dashboard screens"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 9da49563-11c3-4ffd-93c0-43af61e6dd6c
---

All SG dashboard "Next refresh" countdowns must display as `D days HH:MM:SS`, omitting leading components that are zero:
- ≥ 1 day: `2 days 03:45:22`
- ≥ 1 hour (no days): `3:45:22`
- < 1 hour: `45:22`

**Why:** Bill observed "1391m 48s" on the refresh bar and found it unreadable. Wants days/hours/minutes/seconds in standard clock format.

**How to apply:** Any new or updated SG PHP report with an auto-refresh countdown must use this formatter. For seconds-based functions (`fmtCountdown`, `fmt`):
```javascript
function fmtCountdown(secs) {
  const tot = Math.max(0, secs);
  const d = Math.floor(tot / 86400);
  const h = Math.floor((tot % 86400) / 3600);
  const m = Math.floor((tot % 3600) / 60);
  const s = tot % 60;
  const mm = String(m).padStart(2, '0');
  const ss = String(s).padStart(2, '0');
  if (d > 0) return d + (d === 1 ? ' day ' : ' days ') + String(h).padStart(2, '0') + ':' + mm + ':' + ss;
  if (h > 0) return h + ':' + mm + ':' + ss;
  return m + ':' + ss;
}
```
For ms-based functions (`fmtCd`), divide by 1000 first before applying the same logic.
