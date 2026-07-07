---
name: feedback-always-sghdsdata
description: "SGHDSDATA rule applies to business data tables only — portal/menu tables use S5HDSDATA on SG5, SGHDSDATA on EIP Live"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 07557327-1d41-45d1-b3a9-c2069da23d8d
---

**Business data tables** (OEORDH, HDMLDM, HDMOHM, HDMWCM, HREMPL, HDIMST, HDPCLS, HDIPLT, HDIWHS, OEOUDT, and all report/inquiry data): always use `SGHDSDATA`, even in SG5. Live data only lives there.

**Portal and menu tables** (SYPORR, SYROLD, SYPORT, SYURLM, SYROLM, SYPGMO, SYPGMS, SYROLU): schema depends on target environment:
- SG5 portal fixes → `S5HDSDATA`
- EIP Live portal fixes → `SGHDSDATA`

**Why:** GetMenu.php uses unqualified table names resolved by the IBM i library list. SG5's library list resolves portal tables to S5HDSDATA; EIP Live's resolves to SGHDSDATA. A fix script using explicit SGHDSDATA for portal tables modifies EIP Live data even when the script runs from the SG5 URL (5610). This caused an accidental deletion of 478 live SYPORR rows during the QC01 fix (2026-06-29).

**How to apply:** Before writing any portal/menu table fix, state the target environment and the correct schema at the top of the script as a comment. Never use SGHDSDATA for portal table changes labeled "SG5 only." See full procedure in [[feedback-portal-schema-rules]].
