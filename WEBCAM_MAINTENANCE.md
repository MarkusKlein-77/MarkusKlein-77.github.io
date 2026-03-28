# Webcam Maintenance Notes

Last updated: 2026-03-27

## Purpose
This file summarizes the current webcam page setup and recent maintenance conclusions, so future Copilot updates can be done quickly and consistently.

## Repository Overview
- Repository type: static GitHub Pages site.
- Primary webcam pages in this repo:
  - mmwebcamsSki.html
  - mmwebcamsCanada.html
  - mmwebcamsWorld.html
  - webcams.html
- Other root files used by site deployment:
  - index.html
  - CNAME
  - robots.txt
  - csi.min.js

## Current State Of mmwebcamsSki.html
- Layout is now card based, one webcam per row, near full width.
- Webcam titles are displayed above each iframe.
- Source link rows were intentionally removed from cards.
- Sections present:
  - Val Thorens
  - Les Menuires
  - Meribel
  - Courchevel

## Recent Command Conclusions
1. Duplicate iframe source check result:
   - NO_DUPLICATE_IFRAME_SRCS
   - Meaning: no iframe src URL appears more than once across sections.

2. Cime Caron embed decision:
   - Skaping candidate URLs for cime caron redirected to about.skaping.com and were not usable as direct camera pages.
   - Selected replacement endpoint: https://app.webcam-hd.com/valthorens/cime-caron
   - This endpoint is used so the page shows a webcam style embed similar to other webcam-hd entries.

3. Open source page links:
   - Removed from webcam cards to keep the page focused on live views.

4. Width and readability update:
   - Cards changed from multi-column grid to single column list.
   - Iframes increased to a large viewport-based height for easier viewing.

## Webcam Source Mapping Used
Use these source pages when checking for additions, removals, and naming updates:
- Val Thorens: https://www.valthorens.com/en/webcams/
- Les Menuires: https://lesmenuires.com/de/webcams
- Meribel: https://www.meribel.net/en/practical-information/webcams/
- Courchevel: https://courchevel.com/de/livecams/webcam/

## Known Caveats
- Some webcam providers show cookie or loading shells to automated fetch tools.
- app.webcam-hd pages may be reachable but not always expose easy freshness text to tooling.
- Prefer endpoints that render a direct webcam view in iframe instead of full marketing pages.

## Camera Blacklist (Do Not Add)
If a camera is listed here, do not add or re-add it in any webcam page, even if it appears in provider feeds.

- Sun Peaks - Village Clock Tower
   - URL: https://www.sunpeaksresort.com/sites/default/files/spr_website_data/webcams/Village%20Clock%20Tower.jpg
   - Rule: blacklisted and excluded from future additions.

## Recommended Future Update Workflow
1. Read source pages for each resort and list current webcam entries.
2. Compare against iframe src entries in mmwebcamsSki.html.
3. Add missing webcams with a clear card title.
4. Replace stale or broken endpoints with direct webcam provider endpoints where possible.
5. Skip any blacklisted cameras from this file, even if newly discovered in source feeds.
6. Remove duplicates across all sections, keeping only the first occurrence.
7. Re-run a duplicate check for iframe src values.

## Useful Validation Commands (PowerShell)
Duplicate iframe src check:

Select-String -Path 'mmwebcamsSki.html' -Pattern '<iframe[^>]*src="([^"]+)"' -AllMatches |
ForEach-Object { $_.Matches } |
ForEach-Object { $_.Groups[1].Value } |
Group-Object |
Where-Object { $_.Count -gt 1 }

If this returns no output, there are no duplicates.

## Editing Guidelines For This Repo
- Keep pages static (no build tooling assumed).
- Preserve simple HTML that works in GitHub Pages.
- Prefer minimal, targeted edits over broad rewrites.
- Keep webcam card titles close to official source naming.
