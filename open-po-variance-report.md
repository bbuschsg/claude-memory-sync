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

Status 2026-07-07:
1. DONE - Bill confirmed the SG5 test URL runs cleanly with real data; DB2 column names (PODTEN, PDRQDT, PDPOL#, POPO, etc.) are confirmed correct.
2. DONE - copied to `W:\HarrisData\EIP\Custom\SG\Purchasing\OpenPOVarianceReport.php`. Live URL: https://portal.screen-graphics.com:5601/Custom/SG/Purchasing/OpenPOVarianceReport.php
3. DONE - Menu registration: no raw SYURLM/SYPORT/SYROLD/SYPGMO work needed. Navigation for this portal (SGRPT/Purchasing) goes through the shared landing-page file `Custom/SG/sg_portal_landing.php` and its `$reportMap['SGRPT']['PUR']` array - see [[sg-portal-landing-security]]. Entry added and confirmed by Bill on SG5, then mirrored into the EIP copy 2026-07-07 (backed up first as `sg_portal_landing_07071808.php` in both environments' Backup Files; SG5/EIP copies confirmed byte-identical after the edit).
4. DONE - Fixed a variance color bug found after go-live: colors were inverted (favorable/unfavorable logic instead of sign). Corrected to negative=red/positive=green in both environments (backed up first as `OpenPOVarianceReport_07071811.php`). See [[feedback-variance-color-convention]].

PROJECT COMPLETE as of 2026-07-07. Live at https://portal.screen-graphics.com:5601/Custom/SG/Purchasing/OpenPOVarianceReport.php, reachable via EIP menu SG Reports > Purchasing.
