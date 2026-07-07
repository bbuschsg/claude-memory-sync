---
name: project-sg5-server
description: "IBM i SG5/EIP server environment — SeidenPHP (not ZendSvr6), Live vs Test schemas, paths, library lists"
metadata: 
  node_type: memory
  type: project
  originSessionId: 63989d97-e916-4fc0-a72d-3c2ea14bb089
---

Both environments run SeidenPHP (from Seiden Group). **Never use ZendSvr6** even though its toolkit files exist at `/usr/local/zendsvr6/` — HarrisData framework files reference those paths but the PHP runtime is SeidenPHP.

**Why:** User explicitly requires SeidenPHP. ZendSvr6 is present but must not be used.

## TEST Environment (SG5) — always work here first
- Schema: `S5HDSDATA` (program library only — see note below)
- **Data schema: ALWAYS `SGHDSDATA`** — even in SG5 test, all SQL queries point to SGHDSDATA so data is accurate
- Program library: `SG5STDPGM`
- Custom PHP files: `W:\HarrisData\SG5\Custom\`
- SG-specific files: `W:\HarrisData\SG5\Custom\SG\`
- Web root on IBM i: `/www/sg5eip/`
- **Portal URL: `https://portal.screen-graphics.com:5610`** ← TEST is port 5610
- **Custom script URL**: `https://portal.screen-graphics.com:5610/Custom/SG/<relative-path>.php`
  - Example: `https://portal.screen-graphics.com:5610/Custom/SG/Manufacturing/MODailyLaborReport.php`
- **ALWAYS provide the full URL** every time a script is referenced — never just a filename
- Apache error log: `/www/sg5eip/logs/error_log.Q1YYMMDD00`

## LIVE Environment (EIP) — promote to here after SG5 confirmed working
- Schema: `SGHDSDATA`
- Program library: `HDSSTDPGM`
- Custom PHP files: `W:\HarrisData\EIP\Custom\`
- SG-specific files: `W:\HarrisData\EIP\Custom\SG\`
- **Portal URL: `https://portal.screen-graphics.com:5601`** ← LIVE is port 5601
- **Custom script URL**: `https://portal.screen-graphics.com:5601/Custom/SG/<relative-path>.php`

## Library Lists (both environments share same sequence, different libraries at positions 10/20/30)

| Seq | TEST Library | LIVE Library |
|-----|-------------|-------------|
| 10 | S5HDSDATA | SGHDSDATA |
| 20 | SG5MODPGM | HDSMODPGM |
| 30 | SG5STDPGM | HDSSTDPGM |
| 40 | AVATAXR2 | AVATAXR2 |
| 50 | SGPGM | SGPGM |
| 60 | SG | SG |
| 70 | QTEMP | QTEMP |
| 80 | DRVSFLEX | DRVSFLEX |
| 90 | QGPL | QGPL |
| 100 | INFOPRINT | INFOPRINT |
| 110 | HSSF | HSSF |
| 120 | RPGMAIL | RPGMAIL |
| 130 | PROITRG | PROITRG |
| 140 | C3 | C3 |
| 150 | SEQUEL | SEQUEL |
| 160 | ROBOTLIB | ROBOTLIB |
| 170 | ZENDSVR6 | ZENDSVR6 |
| 180 | CGIDEV2 | CGIDEV2 |

## Standard PHP File Header
Every custom PHP page requires these includes:
```php
<?php
require_once 'GetURLParm.php';
require_once 'GenericDirectCallVariables.php';
require_once 'SetLibraryList.php';
date_default_timezone_set('America/Chicago');

function dateToCYMD(DateTime $d): int {
    return ((int)$d->format('Y') - 1900) * 10000
         + (int)$d->format('n') * 100
         + (int)$d->format('j');
}
```
**Correction (confirmed by Bill 2026-07-07): the IBM i server physically resides in the Eastern time zone, not Central, and will not move.** The `date_default_timezone_set('America/Chicago')` line above is what's actually written in every existing custom report's header — this looks like a longstanding codebase-wide inaccuracy (Eastern server, Central assumed), not a deliberate choice. Don't propagate `America/Chicago` into new scripts. See [[feedback_never_assume_timezone]] for the full rule on how to handle time/timezone going forward.

## CYMD Date Format
IBM i packed date: `(Year - 1900) * 10000 + Month * 100 + Day`
Example: 2026-06-08 → `1260608`

## DB2 Connection
`$i5Connect->getConnection()` — HarrisData framework object (used inside PHP running on the IBM i itself).

## External ODBC Access (Windows workstation → IBM i)
- **Correct host/IP: `192.168.120.40`** — confirmed working via `System.Data.Odbc` from this Windows machine.
- `10.10.0.5` (seen in some PHP `db2_connect()` calls in Management-folder scripts) is the IBM i's own internal/loopback address — it is NOT reachable from Windows and will time out (`HYT00 Connection login timed out`). Do not reuse it for a Windows-side ODBC connection string.
- Connection string: `DRIVER={IBM i Access ODBC Driver};SYSTEM=192.168.120.40;UID=<profile>;PWD=<password>;`
- No DSN is pre-registered on this Windows machine — connect DSN-less via `System.Data.Odbc.OdbcConnection` in PowerShell.
- AS/400 user profile used: `bill` (password provided ad hoc by Bill when needed — not stored).

## BaseConfiguration.icl Key Values
From the live `BaseConfiguration.icl` (confirmed 2026-06-27):
- `homeURL` = `https://portal.screen-graphics.com:5601`
- `signonURL` = `$(homeURL)/eip` = `https://portal.screen-graphics.com:5601/eip`
- `phpPath` = `/`
- `homePath` = `/HarrisData/EIP/`
- `cGIPath` = `/harris-CGI/`
- `SGhelpPath` = `$(homePath)custom/SG/` = `/HarrisData/EIP/custom/SG/`
- `altBaseVar` = `BaseConfiguration.php`
- `dataBaseID` = `SG`
- `anonymousUser` = `EIPDEFAULT`
- `pgmLibrary` = `HDSSTDPGM.`

Logout button in SgReportNav.php uses `signonURL`: `https://portal.screen-graphics.com:5601/eip`
Back to EIP button uses `homeURL`: `https://portal.screen-graphics.com:5601`

## Technology Stack
- IBM i / AS400 with SeidenPHP
- Apache 2.4.53 + FastCGI
- Chart.js 4.x for charts
- IBM i ODBC via "IBM i Access ODBC Driver" from Windows PowerShell

[[project-harrisdata-workflow]]
[[project-wmyshipdash]]
