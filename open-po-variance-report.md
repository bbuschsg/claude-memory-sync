---
name: open-po-variance-report
description: "Open PO Variance Report - new SG5 PHP report, status and pending steps"
metadata: 
  node_type: memory
  type: project
  originSessionId: c87360fd-e191-444e-8f66-d89e9a8be952
---

Built 2026-07-07 from a SEQUEL query Bill supplied (open PO cost variance: purchase cost/ea vs HDIPLT standard cost, extended by qty). Delivered as a PHP report per CLAUDE.md sect.5 (SEQUEL is being phased out), off the canonical template `PORequirementsReport.php`.

- File: `W:\HarrisData\SG5\Custom\SG\Purchasing\OpenPOVarianceReport.php`
- Test URL: https://portal.screen-graphics.com:5610/Custom/SG/Purchasing/OpenPOVarianceReport.php
- SG5 ONLY so far, at Bill's request ("SG5 only first").

DB2 translation of the SEQUEL: prefix map PO*=POPOMS(header), PD*=POPOMD(detail), IP*=HDIPLT(cost); all in SGHDSDATA. Joins: PDPO=POPO, PDITEM=IPITEM AND PDPLT=IPPLT. Filters PDSTAT='O', PDPOEC='S', PDPOLT<>'B', VarTtl<>0. Derived PCEA/VarEa/VarTtl in an inner select; NULLIF(PDPCPB,0) guards div-by-zero.

PENDING / not yet done:
1. Verify inferred DB2 column names actually exist (esp. PODTEN, PDRQDT, PDPOL#, POPO) - inferred from SEQUEL field names, not yet confirmed against SYSCOLUMNS or a live run.
2. Promote to EIP (copy to `W:\HarrisData\EIP\Custom\SG\Purchasing\`, provide :5601 URL) - per sect.5 copy-to-both, deferred until Bill confirms SG5 works.
3. Menu registration (SYURLM/SYPORT/SYROLD + SYPGMO) so it appears in the portal - not done; needs backups first per sect.5.
