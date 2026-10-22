# PROJECT CONTRACT: Bootshaus homepage (v1, 2026-10-22)

Authority: Max's directives (DECISIONS.md) > reference teardown > research files. Values marked PLAUSIBLE are fixed by the Day 0 pilot, then written here as KNOWN.

## Structure (from research/reference-teardown-ink-experience.md)
1. **Hero:** a scroll-scrubbed push-in film, lake to plate (SHOT-LIST.md S1-S5), ending dark on one word.
2. **"See it on your table":** a 3D table plus dish chips (menu items). Picking a chip places the scanned dish and moves the camera to it. Side panel: name, short description, price, and "Diesen Tisch reservieren" (CTA wording TBD with Max). Mobile: canvas on top, bottom sheet below, sticky CTA.
3. **The rest, normal site:** menus as HTML (not PDF), boat rental, hours, events, Resmio reservation, contact.

## Tech (KNOWN unless tagged)
- **Film:** canvas WebP image sequence, separate desktop 16:9 and mobile 9:16, GSAP ScrollTrigger + Lenis (`syncTouch` off). An all-intra `-g 1` video is the fallback.
- **3D:** three.js + GLTFLoader with meshopt/Draco + KTX2 dishes from photogrammetry. A splat only if the pilot shows a mesh looks waxy (Spark).
- **AR button per dish:** model-viewer (Scene Viewer GLB at or under 10 MB, 30-50k triangles, 2k textures, PROVEN; iOS USDZ, PLAUSIBLE).
- **Budgets (PLAUSIBLE, set by the pilot):** 1-3 MB per dish GLB, under 10 MB initial load, film 4-8 MB mobile / 10-15 MB desktop.
- **Self-host everything** (GDPR, LG Muenchen 2022). No Google Fonts CDN.
- **Fallbacks:** reduced motion = static posters; no WebGL = photo grid plus text menu. The CTA is always present.

## Acceptance target (mb-task property)
A visitor feels they are arriving at the lake at dusk and walking in to a table at Bootshaus, then sees real Bootshaus dishes in 3D on their own table and wants to book.

## Known risks
- **Scan quality:** food scan quality is UNKNOWN (gated by the pilot).
- **Weak conversion evidence:** the only study is Oxford/Qreal 2019, vendor-linked. Track reservation clicks.
- **Reviews:** service complaints (slow, unfriendly) show up in reviews. Copy must not overpromise service.
- **Boat rental:** the page shows a blue-algae closure banner (date UNKNOWN).
