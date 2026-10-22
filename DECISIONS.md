# DECISIONS: Bootshaus Boeblingen 3D homepage

| Date | Decision | Rationale | Source | Tag |
|---|---|---|---|---|
| 2026-10-22 | Reference = "The Ink Experience" reel (scroll push-in film + interactive 3D try-on) | Max supplied it as the target | Max, reel mp4 | DIRECTIVE |
| 2026-10-22 | Interactive section = option b "See it on your table" (scanned dishes in 3D), not the 3D building/area picker | Max: "ok lets go option b" | Max | DIRECTIVE (hero/layout: UNKNOWN, no conversion data yet, see research/web-3d-dish-viewer.md) |
| 2026-10-22 | Reference is not a Gaussian splat; it is a scroll-scrubbed film + WebGL model | Frame analysis of the reel | research/reference-teardown-ink-experience.md | SUPPORTED |
| 2026-10-22 | Reference media stays local (gitignored), only the written teardown is pushed | Third-party Instagram content | Claude | routine |
| 2026-10-22 | Dishes = photogrammetry GLB in three.js; splat only as a fallback after the pilot | Web/AR constraints, splats can't go to AR | research/web-3d-dish-viewer.md, food-3d-capture-pipeline.md | SUPPORTED |
| 2026-10-22 | Hero film = canvas WebP sequence, 3-5 segments joined by whip / dark push | Frame-exact scrub on iOS | research/scroll-pushin-film.md | SUPPORTED |
| 2026-10-22 | Day 0 pilot (1 dish + 5 s push) before the full shoot | Scan quality and budgets UNKNOWN | research/food-3d-capture-pipeline.md | routine |
| 2026-10-22 | Scan list: Maultaschen, Schnitzel, Flammkuchen, Kaesespaetzle, Rostbraten, cake, chocolate cake; no glass/soup/salad | Reviewer-praised + scannable | research/bootshaus-dossier.md | SUPPORTED (aggregator reviews) |
