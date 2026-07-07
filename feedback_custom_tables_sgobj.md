---
name: feedback-custom-tables-sgobj
description: "Brand-new custom tables (created by us, not existing Harris tables) always live in schema SGOBJ, not SGHDSDATA"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 1879e4ce-dca2-47c9-81ee-e22353a55aba
---

Any NEW custom table we create (e.g. an installer address table, or any other net-new table that isn't part of the Harris/HarrisData data model) always lives in the **SGOBJ** schema — on both SG5 and EIP.

**Why:** SGHDSDATA is reserved for HarrisData's own business data tables (OEORDH, HDCUST, HDMOHM, etc. — see [[feedback_always_sghdsdata]]). Custom tables we invent ourselves are a different category and get their own schema, SGOBJ, so they don't get mixed in with vendor-maintained data.

**How to apply:** When writing any `CREATE TABLE` DDL for a new custom object, qualify it as `SGOBJ.<tablename>` — never `SGHDSDATA.<tablename>`. This applies regardless of environment (SG5 test or EIP live) — unlike portal/menu tables, SGOBJ is not environment-dependent per current guidance (confirm with Bill if that ever seems to conflict with actual library-list resolution, same caution as [[feedback_portal_schema_rules]]).
