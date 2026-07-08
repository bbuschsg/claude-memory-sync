---
name: feedback-variance-color-convention
description: "Variance/differential columns use standard sign convention (negative=red, positive=green), not favorable/unfavorable business logic"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 86b611e8-40e3-459b-82a2-9f5dad77333e
---

For any report column showing a variance, difference, or delta, color strictly by sign: negative = red (`#CC1F20`), positive = green (`#1DA032`), zero = no color. Do not invert this based on business favorability (e.g. "paying under standard is good so make it red as a warning" or similar reasoning) - Bill expects the plain accounting convention every time.

**Why:** On [[open-po-variance-report]], the first pass colored by "favorable/unfavorable" (over-standard cost = red, under-standard = green), which put negative variances in green and positive in red - backwards from what Bill expected on sight.

**How to apply:** When building any new report with variance/delta columns, default to negative=red/positive=green without asking, unless Bill explicitly specifies a different (e.g. favorable/unfavorable) convention for that report.
