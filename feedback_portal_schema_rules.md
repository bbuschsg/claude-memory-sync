---
name: feedback-portal-schema-rules
description: Hard rules for portal/menu table schema selection and fix procedure to prevent breaking EIP Live or SG5 visibility
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 07557327-1d41-45d1-b3a9-c2069da23d8d
---

Never use SGHDSDATA for portal table fixes when working on SG5. S5HDSDATA is the correct schema for SG5 portal tables; SGHDSDATA is EIP Live. GetMenu.php uses unqualified table names resolved by library list — scripts using explicit SGHDSDATA affect live data even when run from the SG5 URL (5610).

**Why:** In the QC01 fix session, a script labeled "SG5 only" used SGHDSDATA.SYPORR and deleted 478 live rows. SG5 was unaffected (it reads S5HDSDATA). EIP Live was accidentally modified without approval.

**How to apply:**

Portal table schema selection:
- SG5 portal tables (SYPORR, SYROLD, SYPORT, SYURLM, SYROLM, SYPGMO, SYPGMS): use S5HDSDATA
- EIP Live portal tables: use SGHDSDATA
- Business data tables (OEORDH, HDMLDM, HDMOHM, etc.): always SGHDSDATA in both environments
- Write the target schema as a comment at the top of every fix script before any code

Fix procedure (in order, every time):
1. Identify environment and correct portal schema before writing any code
2. Run read-only diagnostic first — compare broken role to a working reference role
3. Check SYPORR count → 0 rows = bypass mode, any rows = whitelist mode
4. If reference role is bypass, fix = delete rows. Never add SYPORR rows to a bypass-mode role.
5. Confirm which portal type the role should have (native-only vs SG custom vs combined)
6. Write SG5 fix only. No EIP Live script until explicitly asked.
7. Preview before execute — always. Show row counts before/after, which rows affected.
8. After fix: run GetMenu simulation and confirm it matches what the portal actually shows.
9. Never declare success until the simulation and the portal agree.

**Mandatory pre-push exclusion check:** Before any bulk SYPORR push script inserts SG custom portal rows, it must first identify and exclude all native-only roles. Native-only roles have NO SG portals in SYROLD at all. Run this query and remove every returned role from the INSERT target list:

```sql
SELECT RTRIM(RDROLE) AS ROLE
FROM {SCHEMA}.SYROLD
WHERE RTRIM(RDROLE) NOT IN (
  SELECT RTRIM(RDROLE) FROM {SCHEMA}.SYROLD
  WHERE RTRIM(RDPORT) IN ('SGINQ','SGDASH','SGDINT','SGRPT','SGSOP')
)
GROUP BY RTRIM(RDROLE)
```

If any returned role ends up in the INSERT, the script is wrong. Stop. Fix the exclusion before executing.

This rule exists because a previous push script included QC01 (a native-only role) in an SG portal SYPORR push. QC01 had 0 SG portals in SYROLD. The push placed it in whitelist mode with 478 rows for portals it couldn't use, hiding all its native portals. The entire QC01 fix session on 2026-06-29 was caused by this one omission.

Also: never add anything to reserved roles (SYROLM RLRSV='Y'). Query SYROLM before any bulk push. See [[feedback-reserved-roles]].
