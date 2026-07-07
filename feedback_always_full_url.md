---
name: feedback-always-full-url
description: Always provide the complete URL every time a script is mentioned — never just filename
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 63989d97-e916-4fc0-a72d-3c2ea14bb089
---

Always give the complete URL when referencing any script the user needs to open in a browser.

**Why:** User explicitly requested this after I repeatedly gave only filenames.

**How to apply:** Every single time a PHP script is mentioned for the user to run, include the full URL:
- SG5 Test scripts: `https://portal.screen-graphics.com:5610/HarrisData/SG5/custom/FILENAME.php`
- SG5 Test SG subfolder: `https://portal.screen-graphics.com:5610/HarrisData/SG5/custom/SG/FILENAME.php`
- Never say "run sg_foo.php" — always say "run https://portal.screen-graphics.com:5610/HarrisData/SG5/custom/sg_foo.php"
