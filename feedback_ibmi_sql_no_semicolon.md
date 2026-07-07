---
name: feedback-ibmi-sql-no-semicolon
description: IBM i DB2 SQL statements must NOT end with a semicolon — IBM i rejects them
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 37aad720-f0e9-403c-b538-e26604be63ae
---

Never end IBM i DB2 SQL statements with a semicolon (`;`).

**Why:** IBM i's SQL interface does not accept semicolons as statement terminators — they cause a syntax error. The user had to explicitly correct this.

**How to apply:** Any SQL written for the IBM i / AS400 environment (INSERTs, SELECTs, UPDATEs, DELETEs, CREATE, etc.) must omit the trailing semicolon. This applies to both inline SQL in PHP (db2_exec strings) and standalone SQL scripts run on the IBM i.
