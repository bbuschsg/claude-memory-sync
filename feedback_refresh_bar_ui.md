---
name: refresh-bar-ui
description: "Standard visual design for auto-refresh status bars — light blue bar, animated dot, pill badges. Source of truth: BookingsDashboard.php"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 4bd8870a-eaf7-4613-9343-e6bc1952dd72
---

Every page or dashboard with auto-refresh must display a status bar **exactly matching BookingsDashboard.php**. Source of truth: `W:\HarrisData\EIP\Custom\SG\Order Entry\BookingsDashboard.php`.

**CSS to copy verbatim into every page:**
```css
.refresh-bar { background: #e8f0fb; border-bottom: 1px solid #bdd0ee; padding: 4px 14px; display: flex; align-items: center; gap: 14px; font-size: 11px; color: #5a6478; flex-shrink: 0; }
.refresh-dot { width: 8px; height: 8px; border-radius: 50%; background: #1a7a3c; animation: pulse 2s infinite; flex-shrink: 0; }
.refresh-dot--off { background: #94a3b8; animation: none; }
@keyframes pulse { 0%,100%{opacity:1} 50%{opacity:0.4} }
.refresh-progress { flex: 1; max-width: 160px; height: 4px; background: #d0dced; border-radius: 2px; overflow: hidden; }
.refresh-fill { height: 100%; background: #0055b3; border-radius: 2px; transition: width 1s linear; }
.refresh-pill { background: #fff; border: 1px solid #c8d0de; border-radius: 12px; padding: 2px 10px; font-size: 11px; font-weight: 600; white-space: nowrap; }
```

**HTML when auto-refresh is ON (PHP):**
```html
<div class="refresh-bar">
  <div class="refresh-dot"></div>
  <span>Live &ndash; auto-refreshes every N min (M&ndash;F, 7:00am&ndash;5:00pm ET)</span>
  <div class="refresh-progress"><div class="refresh-fill" id="PAGE-prog" style="width:100%"></div></div>
  <span>Next refresh in: <strong id="PAGE-cd">Nm 0s</strong></span>
  <span class="refresh-pill">Last refresh: <strong><?php echo date('g:i:s A'); ?></strong></span>
  <span class="refresh-pill" style="background:#fff0d0;border-color:#f0c060;color:#885500;">As of: <?php echo date('D, M j, Y'); ?></span>
</div>
```

**HTML when auto-refresh is OFF:**
```html
<div class="refresh-bar">
  <div class="refresh-dot refresh-dot--off"></div>
  <span>Auto-refresh off (outside M&ndash;F 7:00am&ndash;5:00pm ET)</span>
  <span style="flex:1"></span>
  <span class="refresh-pill">Last refresh: <strong><?php echo date('g:i:s A'); ?></strong></span>
  <span class="refresh-pill" style="background:#fff0d0;border-color:#f0c060;color:#885500;">As of: <?php echo date('D, M j, Y'); ?></span>
</div>
```

**JS countdown (replace PAGE with page prefix, e.g. molr, momc):**
```javascript
(function () {
    var total = REFRESH_SECS;
    var secs  = total;
    var cd    = document.getElementById('PAGE-cd');
    var prog  = document.getElementById('PAGE-prog');
    function fmt(s) { return Math.floor(s / 60) + 'm ' + (s % 60) + 's'; }
    function tick() {
        if (secs <= 0) { location.reload(); return; }
        if (cd)   cd.textContent   = fmt(secs);
        if (prog) prog.style.width = (secs / total * 100).toFixed(1) + '%';
        secs--;
        setTimeout(tick, 1000);
    }
    tick();
}());
```

**Key visual details:**
- Background: `#e8f0fb` (light blue) — NOT white
- Dot: 8×8px circle with green `#1a7a3c` pulse animation — NOT a text character
- Progress bar: `max-width: 160px; height: 4px` — capped width, thin
- Fill color: `#0055b3`
- "Last refresh" = white pill badge (rounded, bordered)
- "As of" = amber pill (`#fff0d0` bg, `#f0c060` border, `#885500` text)

**Why:** User confirmed BookingsDashboard.php is the exact standard for all refresh bars (2026-06-24). Earlier implementation used white background — incorrect.

**How to apply:** Copy the CSS block and HTML template above into every PHP page that has auto-refresh. Replace PAGE with a short page-specific prefix. Always apply to both SG5 and EIP.
