---
name: reference-sg-report-design
description: Complete SG Report design standard — all confirmed settings from PORequirementsReport.php as of 2026-07-01
metadata: 
  node_type: memory
  type: reference
  originSessionId: a8ce3d39-0a43-42c6-9d38-6bf8e034acce
---

Canonical template: `W:\HarrisData\SG5\Custom\SG\Purchasing\PORequirementsReport.php`  
Confirmed correct 2026-07-01. Always use this file as the starting point for new Reports.

## Color Palette (locked)

| Element | Color | Notes |
|---|---|---|
| Title bar gradient | `#111827 → #1F2937 → #374151 → #4B5563 → #6B7280` | left→right |
| Table header bg | `#374151` | dark gray |
| Table header text | `#fff` bold | |
| Odd rows | `#F7F7F7` | |
| Even rows | `#FFFFFF` | |
| Row hover | `#EFF6FF` | light blue |
| Cell text | `#111827` | dark near-black |
| Links | `#2563EB` bold | no underline; underline on hover |
| Negative / alert values | `#CC1F20` bold `!important` | use `.xxx-negative` class |
| Refresh bar bg | `#2563EB` | medium blue |
| Refresh bar border | `#1d4ed8` | |
| Progress fill | `#3B82F6` | |
| Pulse dot (active) | `#16A34A` green | animated pulse |
| Pulse dot (paused) | `#888` | no animation |
| "Last refresh" pill text | `#2563EB` bold | white bg, `#ddd` border |
| "As of" badge | bg `#fff3cd`, border `#f0c060`, text `#856404` | amber |
| Toolbar/filter bg | `#F7F7F7` | |
| Toolbar border-bottom | `2px solid #D1D5DB` | |
| Right panel border-left | `2px solid #D1D5DB` | |
| Back to EIP button | `#06B6D4` cyan, border `#0891B2` | |
| Logout button | `#CC1F20` red, border `#8b1010` | |
| Refresh Now button | `#7B1FA2` purple, border `#4a0f6e` | |
| Export to Excel button | `#1DA032` green | |

## Page Structure

```
[Full-width gradient title bar]
[Outer flex div — border-bottom:2px solid #D1D5DB]
  [Left section flex:1, two bars stacked]
    [Refresh status bar — bg #2563EB]
    [Filter bar — bg #F7F7F7]
  [Right panel — border-left:2px solid #D1D5DB, bg #F7F7F7]
    [↻ Refresh Now button]
    [↓ Export to Excel button]
[overflow-x:auto wrapper]
  [Table — id="xxx-grid"]
```

## Title Bar
- `position:relative; left:-155px; width:calc(100% + 155px); box-sizing:border-box`
- `padding:10px 14px 10px calc(155px + 14px)` — compensates for 155px nav
- `display:flex; align-items:center; gap:10px; margin-bottom:6px`
- `border-bottom:3px solid rgba(0,0,0,0.15)`
- H1: `font-size:22px; color:#fff !important; margin:0; flex:1; font-weight:bold !important; text-shadow:0 1px 3px rgba(0,0,0,0.4)`
- Back to EIP href: `$_sgnHome . '/Welcome.php?baseVar=' . rawurlencode($_sgnBv) . '&eID=' . rawurlencode($_sgnEid) . '&portal=9999999999'`
- Logout href: `https://screen-graphics.com/`
- Button style shared: `padding:4px 14px; font-size:12px; font-weight:700; color:#fff !important; text-decoration:none !important; border-radius:4px; white-space:nowrap; display:inline-block`

## Portal Width Overrides (CSS block 1)
```css
table[summary="banner"] { display:none !important; }
body { box-sizing:border-box !important; }
body > table { width:100% !important; max-width:none !important; table-layout:auto !important; }
td.content { width:calc(100vw - 155px) !important; max-width:none !important; box-sizing:border-box !important; }
#xxx-grid { width:100% !important; min-width:100% !important; }
```

## Table CSS
```css
#xxx-grid thead th { background-color:#374151 !important; color:#fff !important; font-weight:bold !important; }
#xxx-grid tbody .xxx-row:nth-child(odd)  { background:#F7F7F7; }
#xxx-grid tbody .xxx-row:nth-child(even) { background:#FFFFFF; }
#xxx-grid tbody .xxx-row:hover           { background:#EFF6FF !important; }
#xxx-grid tbody td a { color:#2563EB !important; text-decoration:none !important; font-weight:bold !important; }
#xxx-grid tbody td a:hover { text-decoration:underline !important; }
#xxx-grid tbody td { color:#111827 !important; }
#xxx-grid tbody td.xxx-negative { color:#CC1F20 !important; font-weight:bold !important; }
.refresh-fill { background:#3B82F6 !important; }
.refresh-dot  { background:#16A34A !important; }
```

## Refresh Bar CSS (block 2)
```css
.refresh-dot { width:8px; height:8px; border-radius:50%; animation:pulse 2s infinite; flex-shrink:0; }
@keyframes pulse { 0%,100%{opacity:1} 50%{opacity:0.4} }
.refresh-progress { flex:1; max-width:160px; height:4px; background:rgba(255,255,255,0.18); border-radius:2px; overflow:hidden; }
.refresh-fill { height:100%; border-radius:2px; transition:width 1s linear; }
.refresh-pill { background:#fff; border:1px solid #ddd; border-radius:12px;
                padding:2px 10px; font-size:11px; font-weight:700; white-space:nowrap;
                color:#2563EB !important; }
```

## Refresh Bar HTML
```html
<div style="background:#2563EB;border-bottom:1px solid #1d4ed8;padding:4px 14px;
            display:flex;align-items:center;gap:14px;font-size:11px;color:#fff;flex:1;">
  <div class="refresh-dot" id="xxx-dot"></div>
  <span id="xxx-status">Live – auto-refreshes every N min (M–F, 7:00am–6:00pm)</span>
  <div class="refresh-progress"><div class="refresh-fill" id="xxx-prog" style="width:100%"></div></div>
  <span>Next refresh in: <strong id="xxx-cd">N:00</strong></span>
  <span class="refresh-pill">Last refresh: <strong><?php echo date('g:i:s A'); ?></strong></span>
  <span class="refresh-pill" style="background:#fff3cd;border-color:#f0c060;color:#856404;">As of: <?php echo date('D, M j, Y'); ?></span>
</div>
```

## Timezone in Refresh Bar
Timezone abbreviation must come from browser JS — never hardcode "CT":
```js
var tzAbbr = new Date().toLocaleTimeString('en-US', {timeZoneName:'short'}).split(' ').pop();
// Use in status text: '... 7:00am–6:00pm ' + tzAbbr + ')'
```

## Countdown Format
`D days HH:MM:SS` — omit days if 0, omit hours if 0. Minimum: `M:SS`.

## Filter Bar
- All filters are `<select>` dropdowns populated from live query data — never free-text inputs
- Layout: `display:flex; align-items:center; gap:10px; padding:6px 10px; font-size:12px`
- Labels: `font-weight:600`
- Clear button: plain white bg, `border:1px solid #bbb; border-radius:3px`
- Item count: `<b style="margin-left:auto; white-space:nowrap;">N items</b>` — italic, right-aligned via `margin-left:auto`
- Export button (`margin-left:auto`): `background:#1DA032; color:#fff`

## Right Panel
- `display:flex; flex-direction:column; align-items:stretch; justify-content:center; gap:4px; padding:6px 10px`
- Refresh Now: `background:#7B1FA2; color:#fff; border:1px solid #4a0f6e; font-weight:bold; font-size:12px; padding:3px 14px`
- Export: `background:#1DA032; color:#fff; font-weight:bold; font-size:12px; padding:3px 14px; display:block; text-align:center`

## Table Headers
- All `<th>` must be white (`#fff`) and bold, with `!important`
- Add sort indicators via CSS `::after`: `▲` asc, `▼` desc
- `cursor:pointer; user-select:none; white-space:nowrap`

## Auto-Refresh JS Pattern
- `AUTO_SECS`: report-specific (600 = 10 min, 900 = 15 min)
- Window check: M–F (day 1–5), hours 7–17 (Chicago time via `toLocaleString('en-US', {timeZone:'America/Chicago'})`)
- Active: dot green + pulse, progress bar shrinks, countdown ticks
- Paused: dot grey, bar at 0, countdown resets, message updates
- `updateBar()` called immediately on load and every 1s via `setInterval`

## Negative Values
- Add PHP class conditionally: `class="xxx-row-cell<?php echo $val < 0 ? ' xxx-negative' : ''; ?>"`
- CSS rule: `#xxx-grid tbody td.xxx-negative { color:#CC1F20 !important; font-weight:bold !important; }`
- Do NOT rely on inline style — the `tbody td { color:#111827 !important }` rule will override it

## Date Fields (IBM i)
- IBM i DATE type returns `YYYY-MM-DD` string
- Numeric CYMD: 7-digit integer → convert via helper function
- Sort `data-val`: return `YYYYMMDD` int for JS numeric sort
- Never cast a DATE field with `(int)` — silently returns 0

## CSV Export
- Triggered by `?export=csv` GET param at top of script (before any HTML output)
- Filename: `ReportName_YYYYMMDD_HHiiss.csv`
