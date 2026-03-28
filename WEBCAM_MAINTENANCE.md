# Webcam Maintenance Notes

Last updated: 2026-03-28

## Purpose
This file is the maintenance receipt and operating guide for webcam updates.
Use it as the source of truth before editing any webcam page.

## Scope
- Primary webcam pages:
   - mmwebcamsSki.html
   - mmwebcamsCanada.html
   - mmwebcamsWorld.html
- Related index page:
   - webcams.html

## 2-Day Receipt (2026-03-27 to 2026-03-28)

### mmwebcamsCanada.html
- CSS/media behavior aligned to preserve image aspect ratio:
   - images keep natural proportions (`height: auto`, `object-fit: contain`).
- Sunshine Village expanded to full provider set (11 cams) from API data.
- Banff Norquay expanded from 2 to 4 cams (missing Sundance and Tube Park added).
- Existing refresh model retained (cache-busting image reload cycle for `live-image`).

### mmwebcamsWorld.html
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

### mmwebcamsSki.html
- No structural updates in this 2-day window.
- Existing architecture remains valid:
   - iframe-first layout,
   - source links removed from cards,
   - known Cime Caron replacement retained (`app.webcam-hd.com`).

## Current Webcam Handling Rules

### Rendering decision tree
1. If provider offers stable direct image (`image/jpeg`, `image/webp`), use:
    - `<img class="live-image" data-base-src="..." src="...">`
2. If provider blocks image hotlinking or requires player context, use:
    - `<iframe loading="lazy" src="...">`
3. If provider gives a stable MP4 live URL and user requests player-style behavior, use iframe with MP4 URL.
4. Prefer official embed URLs/snippets when available.

### Refresh behavior
- For `<img class="live-image">`, keep `data-base-src` and JS cache-busting refresh cycle.
- For iframe providers, rely on provider/player update behavior (no forced image polling).

### Validation rules before merge
- Validate endpoint status and content type.
- For Feratel thumbnails, note that HEAD can return 405 while GET works.
- For RAS paths, validate body/content type (some paths return HTML fallback, not camera media).
- Avoid mixed-content regressions:
   - HTTP-only webcam URLs can fail on HTTPS pages.

## Camera Blacklist (Do Not Add)
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

## Known Caveats
- Some providers require cookies/JS and expose only partial data to fetch tools.
- Skyline direct `embed/.../img/...jpg` may fail or be inconsistent without using official embed page/snippet.
- HTTP-only sources may not render on HTTPS GitHub Pages due to browser mixed-content policy.

## Future Update Playbook
1. Read this file first.
2. Read the source URLs listed in each section header of the target HTML page.
3. Compare provider cam list vs. current cards (title + endpoint).
4. Exclude any blacklisted camera immediately.
5. Choose image vs iframe using the rendering rules above.
6. Validate endpoint behavior (status + media type + freshness).
7. Keep card titles close to official naming.
8. Keep edits minimal and section-scoped.

## Useful Validation Commands (PowerShell)

Check duplicate iframe sources in a file:

Select-String -Path 'mmwebcamsWorld.html' -Pattern '<iframe[^>]*src="([^"]+)"' -AllMatches |
ForEach-Object { $_.Matches } |
ForEach-Object { $_.Groups[1].Value } |
Group-Object |
Where-Object { $_.Count -gt 1 }

Check duplicate image base sources in a file:

Select-String -Path 'mmwebcamsWorld.html' -Pattern 'data-base-src="([^"]+)"' -AllMatches |
ForEach-Object { $_.Matches } |
ForEach-Object { $_.Groups[1].Value } |
Group-Object |
Where-Object { $_.Count -gt 1 }

## Editing Guidelines
- Keep pages static and GitHub Pages-friendly.
- Preserve existing layout/styling conventions unless a specific visual fix is requested.
- Prefer small, verifiable changes over broad rewrites.
