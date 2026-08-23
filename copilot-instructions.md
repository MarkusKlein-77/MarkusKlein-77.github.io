# Copilot Instructions For Webcam Updates

Always read WEBCAM_MAINTENANCE.md before making webcam-related changes.

When a task involves mmwebcamsSki.html, mmwebcamsCanada.html, mmwebcamsWorld.html, or webcams.html:
1. Use WEBCAM_MAINTENANCE.md as the source of truth for recent context and rules.
2. Respect the blacklist in WEBCAM_MAINTENANCE.md (never re-add blacklisted cameras).
3. Follow the image-vs-iframe handling rules documented in WEBCAM_MAINTENANCE.md.
4. Keep edits minimal and scoped to the requested section.
5. Validate endpoints (status + content-type) before finalizing updates.

## Checking Stuttgart-Ulm (S21) Webcams for Updates

For webcams.html, which contains Stuttgart-Ulm construction webcams:

### Official Source
- Official page: https://www.bahnprojekt-stuttgart-ulm.de/projekt/baufortschritt/webcams
- This page uses a cimage wrapper that processes the same base URLs used in webcams.html

### How to Check for New Webcams
1. Open the official page in a browser using `open_browser_page`
2. Use `run_playwright_code` to extract webcam image sources:
   ```javascript
   const imgs = await page.evaluate(() => {
     const images = Array.from(document.querySelectorAll('img'));
     return images
       .filter(img => img.src && (img.src.includes('webcam') || img.src.includes('cam')))
       .map(img => ({
         src: img.src,
         path: img.src.match(/src=([^&]+)/)?.[1] || img.src
       }));
   });
   return imgs;
   ```
3. Compare extracted paths with webcams.html entries

### URL Format Matching
- **Official page format**: `https://webcam-bahnprojekt-stuttgart-ulm.de/cimage/webroot/img.php?src=/PATH/FILE.jpg&w=1140&sharpen&save-as=jpg&nu&q=70&fki=.jpg`
- **webcams.html format**: `https://webcam-bahnprojekt-stuttgart-ulm.de/PATH/FILE.jpg`
- **Match by**: Extract the `/PATH/FILE.jpg` portion from the cimage `src=` parameter

### HTML Comments in webcams.html
Each webcam entry has a comment above it showing:
- `<!-- Official: [cimage URL] -->` - Webcam exists on official page with cimage wrapper
- `<!-- Official: Same URL ([description]) -->` - Webcam uses identical URL on both pages
- `<!-- NOT on official page -->` - Webcam only exists in webcams.html (extras)

### Known Extra Webcams (not on official page)
- S21-Gitterschale-Turm
- Strabag cam 501
- bautvplus.de cam3
- EIFFAGE/ARGE Tunnel Rastatt iframes
