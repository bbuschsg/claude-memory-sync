---
name: project-commissions-bug
description: June 2026 commissions bug — slsm 64 got 4% on 4 invoices; must investigate before August run
metadata: 
  node_type: memory
  type: project
  originSessionId: d958112e-29c0-4b89-97de-29e4bfe20042
---

June 2026 commission run (2026-06-30): salesperson #64 received 4% commission on 4 invoices instead of the correct 3%.

**Key facts:**
- The script process_commissions.py caused this — the source file did NOT have 4% pre-set on those rows.
- Bill manually corrected those 4 rows directly in the Excel file (COM035p.XLSX).
- Script was left unchanged per Bill's instruction. Do NOT edit the script until August.
- The June archive copy COM035p (June)2026.XLSX was saved BEFORE the run, so original data is preserved.
- The timestamped backup is COM035p_backup_20260630_202231.XLSX.

**Why:** Unknown — the COMMISSION_RATES dict has 64: 3.00, so the script should never produce 4.0 for slsm 64. Bill is certain the script did it, not the source data. The root cause needs to be investigated during the August run.

**How to apply:** When Bill asks to run August commissions, first re-examine why slsm 64 got 4% in June. Compare the backup against the processed file to identify those 4 rows. Do NOT run the script blindly — diagnose first, then run, then verify slsm 64 rates carefully before closing.
