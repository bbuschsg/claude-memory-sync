---
name: feedback-no-long-dashes
description: Em dashes ABSOLUTELY PROHIBITED everywhere, no exceptions; use plain hyphen-space or period instead
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 237fe35a-8aa4-4ba5-9b4b-3ff4d201f81d
---

**ABSOLUTE RULE: NEVER USE EM DASHES. EVER. NOT ONCE. NO EXCEPTIONS.**

Never use em dashes (—) or `&mdash;` ANYWHERE. This means: PHP code, JavaScript, HTML output, portal descriptions, report titles, subtotal labels, dropdown options, CSV exports, SQL comments, code comments, inline chat responses, summary documents — literally everywhere. Zero tolerance.

**Why:** Excel encodes the UTF-8 em dash as `â€"` (mojibake). User called this out multiple times with escalating emphasis. This is a hard absolute rule.

**How to apply:**
- Separator in titles/labels: use plain hyphen with spaces ` - ` (e.g. `AR Aging Report - As of`, `ABC DISPOSAL - Subtotal`)
- In portal description strings: use a period and capitalize the next word
- In prose/chat: use a comma, parentheses, or rewrite the sentence to avoid the construction
- When in doubt: rewrite rather than risk it
