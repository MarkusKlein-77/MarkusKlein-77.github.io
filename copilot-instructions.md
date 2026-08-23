# Copilot Instructions for Webcam Maintenance

Last updated: 2026-08-23

## Quick Start

When a task involves mmwebcamsSki.html, mmwebcamsCanada.html, mmwebcamsWorld.html, or webcams.html:
1. Read the relevant sections below as the source of truth for recent context and rules.
2. Respect the blacklist (never re-add blacklisted cameras).
3. Follow the image-vs-iframe handling rules.
4. Keep edits minimal and scoped to the requested section.
5. Validate endpoints (status + content-type) before finalizing updates.

---

## Stuttgart-Ulm (S21) Webcams (webcams.html)

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
- Strabag cam 501, 504
- bautvplus.de cam3
- EIFFAGE/ARGE Tunnel Rastatt iframes

### Additional Webcam Resources
- Google Photos album with community updates: https://photos.google.com/share/AF1QipNRvX_Smv_szpsMb1Q7SuaFwkHXeQikQN3DqiE_p4SlyTEfSbPEg8QxeDcsUeL7SA?key=eE9DV19hRXZyZjJjUWUwSVJCcG1jNmVyOWpaaUV3
- Stuttgart Climate webcams: https://www.stadtklima-stuttgart.de/index.php?info_webcams

---

## World/Ski/Canada Webcams Maintenance Guide

### Scope
- mmwebcamsSki.html
- mmwebcamsCanada.html
- mmwebcamsWorld.html

### 2-Day Receipt (2026-03-27 to 2026-03-28)

#### mmwebcamsCanada.html
- CSS/media behavior aligned to preserve image aspect ratio:
   - images keep natural proportions (`height: auto`, `object-fit: contain`).
- Sunshine Village expanded to full provider set (11 cams) from API data.
- Banff Norquay expanded from 2 to 4 cams (missing Sundance and Tube Park added).
- Existing refresh model retained (cache-busting image reload cycle for `live-image`).

#### mmwebcamsWorld.html
- CSS/media behavior aligned to preserve image aspect ratio for image webcams.
- Source URL lines were added/expanded per section and kept in section headers.
- Meran 2000 Seilbahn Bergstation removed and blacklisted.
- Schnalstal updated:
   - Added missing cams and switched selected Feratel cams to MP4 iframe URLs where requested.
   - Kept Panomax entries as iframe/image depending on provider format.
- Ultental updated:
   - Correct RAS paths found and used (`alm/cam1`, `alm/cam2`, `pro/cam1`, `ult/cam1`).
   - Bergstation Schwemmalmbahn switched to Feratel MP4 iframe URL when requested.
- Feldberg updated:
   - Added source page `feldberg-erlebnis.de/aktuelles/webcam`.
   - Added additional validated cams (Grafenmatt, Panorama iframe, Seebuck Talstation, Schwarzenbach Liftansicht).
   - Altglashuetten removed and blacklisted.
   - Panorama switched to requested event-cam iframe URL and set to full viewport height override.
- Lago di Iseo updated multiple times:
   - Skyline entries moved to iframe approach where required.
   - Temporary meteoblue/windy-derived cams added and later removed on request.
   - Final state after latest request: first three Iseo cams removed.
- Lago di Garda cam 1 replaced with Skyline official embed-style snippet (clickable image link).
- Lago di Caldonazzo changed from image to iframe on request, but source remains HTTP-only.

#### mmwebcamsSki.html
- No structural updates in this 2-day window.
- Existing architecture remains valid:
   - iframe-first layout,
   - source links removed from cards,
   - known Cime Caron replacement retained (`app.webcam-hd.com`).

### Current Webcam Handling Rules

#### Rendering decision tree
1. If provider offers stable direct image (`image/jpeg`, `image/webp`), use:
    - `<img class="live-image" data-base-src="..." src="...">`
2. If provider blocks image hotlinking or requires player context, use:
    - `<iframe loading="lazy" src="...">`
3. If provider gives a stable MP4 live URL and user requests player-style behavior, use iframe with MP4 URL.
4. Prefer official embed URLs/snippets when available.

#### Refresh behavior
- For `<img class="live-image">`, keep `data-base-src` and JS cache-busting refresh cycle.
- For iframe providers, rely on provider/player update behavior (no forced image polling).

#### Validation rules before merge
- Validate endpoint status and content type.
- For Feratel thumbnails, note that HEAD can return 405 while GET works.
- For RAS paths, validate body/content type (some paths return HTML fallback, not camera media).
- Avoid mixed-content regressions:
   - HTTP-only webcam URLs can fail on HTTPS pages.

### Camera Blacklist (Do Not Add)
If a camera appears here, do not add or re-add it even if it appears in provider feeds.

- Sun Peaks - Village Clock Tower
   - URL: https://www.sunpeaksresort.com/sites/default/files/spr_website_data/webcams/Village%20Clock%20Tower.jpg
   - Rule: blacklisted and excluded from future additions.

- Meran - Meran 2000 Seilbahn Bergstation
   - URL: https://webcams.meran2000.com/seilbahn/cam.jpg
   - Rule: blacklisted and excluded from future additions.

- Feldberg - Altglashuetten
   - URL: http://webcam.land-in-sicht.com/feldberg/webcam-altglashuetten1280.jpg
   - Rule: blacklisted and excluded from future additions.

### Known Caveats
- Some providers require cookies/JS and expose only partial data to fetch tools.
- Skyline direct `embed/.../img/...jpg` may fail or be inconsistent without using official embed page/snippet.
- HTTP-only sources may not render on HTTPS GitHub Pages due to browser mixed-content policy.

### Future Update Playbook
1. Read this file first.
2. Read the source URLs listed in each section header of the target HTML page.
3. Compare provider cam list vs. current cards (title + endpoint).
4. Exclude any blacklisted camera immediately.
5. Choose image vs iframe using the rendering rules above.
6. Validate endpoint behavior (status + media type + freshness).
7. Keep card titles close to official naming.
8. Keep edits minimal and section-scoped.

### Useful Validation Commands (PowerShell)

Check duplicate iframe sources in a file:
```powershell
Select-String -Path 'mmwebcamsWorld.html' -Pattern '<iframe[^>]*src="([^"]+)"' -AllMatches |
ForEach-Object { $_.Matches } |
ForEach-Object { $_.Groups[1].Value } |
Group-Object |
Where-Object { $_.Count -gt 1 }
```

Check duplicate image base sources in a file:
```powershell
Select-String -Path 'mmwebcamsWorld.html' -Pattern 'data-base-src="([^"]+)"' -AllMatches |
ForEach-Object { $_.Matches } |
ForEach-Object { $_.Groups[1].Value } |
Group-Object |
Where-Object { $_.Count -gt 1 }
```

### Editing Guidelines
- Keep pages static and GitHub Pages-friendly.
- Preserve existing layout/styling conventions unless a specific visual fix is requested.
- Prefer small, verifiable changes over broad rewrites.
