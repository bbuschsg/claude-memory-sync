---
name: feedback-menu-addition-syporr
description: Every new menu item addition must include SYPORR updates for all whitelist-mode roles — missing this breaks portal visibility silently
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 07557327-1d41-45d1-b3a9-c2069da23d8d
---

Any time a new menu item is added to SYPORT + SYURLM, SYPORR must also be updated for every role that (a) has that portal in SYROLD and (b) is in whitelist mode (SYPORR count > 0). Skipping SYPORR causes GetMenu's inner join to fail silently — the portal disappears from affected roles with no error message.

**Why:** GetMenu in whitelist mode joins SYPORT to SYPORR on FPPORT=PRPORT AND FPPAGE=PRPAGE AND FPSEQ=PRSEQ. If SYPORR doesn't have a matching row for the new SYPORT entry, that item is invisible to whitelist-mode roles. This has broken role visibility repeatedly.

**How to apply:**

Before writing any menu addition script, run this query first (correct schema for environment):
```sql
SELECT RTRIM(d.RDROLE) AS ROLE, COUNT(p.PRROLE) AS SYPORR_ROWS
FROM {SCHEMA}.SYROLD d
LEFT JOIN {SCHEMA}.SYPORR p ON RTRIM(p.PRROLE) = RTRIM(d.RDROLE)
WHERE RTRIM(d.RDPORT) = 'PORTALNAME'
GROUP BY RTRIM(d.RDROLE)
ORDER BY RTRIM(d.RDROLE)
```

Any role with SYPORR_ROWS > 0 needs a SYPORR insert in the same script.

Every menu addition script must have four parts in order:
1. SYPORT insert (new page definition)
2. SYURLM insert (URL mapping, FUID=FPID)
3. SYPORR inserts for every whitelist-mode role — PRPORT=FPPORT, PRPAGE=FPPAGE, PRSEQ=FPSEQ, PRSEL='Y'
4. GetMenu simulation as verification — show what each affected role would see after the change

All inserts use WHERE NOT EXISTS. Script is not complete until GetMenu simulation confirms the new item appears for every expected role.

Bypass-mode roles (0 SYPORR rows) require no SYPORR maintenance — new items show automatically. When fixing a broken whitelist-mode role, raise whether converting it to bypass mode is appropriate.

See [[feedback-portal-schema-rules]] for schema selection rules.
