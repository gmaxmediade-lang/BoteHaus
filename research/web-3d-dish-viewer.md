# Web 3D dish viewer: tech, UX, AR, evidence (researched 2026-10-22, mb-researcher/sonnet)

No existing Master Brain or project note covers this topic (searched). Claims tagged PROVEN (source) / PLAUSIBLE / UNKNOWN.

## Recommendation
- **Viewer:** three.js with `GLTFLoader` (Draco/meshopt, KTX2 textures) and a custom table scene. Use React Three Fiber only if the page is already React. The page needs several dishes on one table, picking and camera moves, which a single-model viewer can't do. PLAUSIBLE
- **Dish format:** photogrammetry meshes (GLB), not splats. Splats can't be handed to AR, and several splat scenes on iOS Safari is risky. A splat is acceptable only as an optional backdrop (Spark). PLAUSIBLE
- **AR:** `<model-viewer>` for a per-dish "on your table" AR button, or call Quick Look and Scene Viewer directly.
- **Fallbacks:** every dish needs a poster image and a no-WebGL fallback.
- **Biggest risk:** scan quality of real food (glossy, translucent, changes over time) is UNKNOWN until a test scan exists.

## Tech comparison
| Option | Pros | Cons | Tag |
|---|---|---|---|
| three.js / R3F | Full control: multi-dish table, picking, camera moves, post-processing | Most code; manual memory management | PLAUSIBLE |
| `<model-viewer>` (Google, Apache-2.0) | One-line AR (Quick Look, Scene Viewer, WebXR), poster/reveal, reduced-motion support | One model per element; limited composition | PLAUSIBLE, per https://modelviewer.dev/docs/index.html (features listed, not tested) |
| Spark (World Labs, v2.0) | Splat renderer for three.js; reads PLY/SOGS/SPZ/SPLAT/KSPLAT; LOD, on-demand rendering; splats coexist with meshes | No AR path; mobile performance figures not published | PROVEN per https://sparkjs.dev/ (as fetched) |
| PlayCanvas / GaussianSplats3D | Mature splat viewers | Same splat limits as above | PLAUSIBLE |

## Budgets
- **iOS Safari and splats:**
  - Memory is tight; large splat files crash the page. Spherical harmonics at degree 0 plus compressed formats help (PlayCanvas forum, search snippet only, PLAUSIBLE).
  - A claimed budget of 2-4M Gaussians at 60 fps on A15+ comes from a third-party page. UNKNOWN.
- **Table scene:** draw-call, MB and texture-memory budgets are UNKNOWN (no authoritative source). Working targets are assumptions, to be profiled on a real iPhone:
  - a few dozen draw calls
  - one 1-2k texture per dish
  - about 1-3 MB GLB per dish
  - under about 10 MB initial load
- **Loading strategy (PLAUSIBLE):**
  - poster images first
  - lazy-load the canvas near the viewport
  - load only the selected dishes
  - KTX2 plus Draco/meshopt
  - dispose geometry and textures when a dish is removed
- **iOS pitfalls:** cap `devicePixelRatio` at 2, expect canvas memory limits, handle WebGL context loss. PLAUSIBLE

## AR handoff
- **Android Scene Viewer** (PROVEN, https://developers.google.com/ar/develop/scene-viewer):
  - GLB/glTF 2.0 over HTTPS
  - recommended maximum 10 MB (15 MB tolerated)
  - 30-50k triangles recommended, 100k maximum
  - textures up to 2048x2048
  - PBR materials, 10 materials maximum
  - needs an ARCore device on Android 7.0+
  - launched with an intent URL (`file`, `mode=ar_preferred`, `title`)
- **iOS Quick Look** (https://developer.apple.com/augmented-reality/quick-look/): USDZ on iOS 12+, opened by linking to a `.usdz` file. The `<a rel="ar">` markup is PLAUSIBLE.
- **Conversion:** GLB to USDZ via Reality Converter, `usdzconvert` or Blender (PLAUSIBLE). Whether model-viewer auto-generates USDZ is UNKNOWN.
- **Scale:** models must be true scale (a plate is about 26 cm). PLAUSIBLE

## Benchmarks
- **Awwwards pages found (not opened, so their quality is UNKNOWN):**
  - Les Ignobles interactive menu
  - FoodFest 1.0
  - Sgrappa
  - Zoi Ice Tea
- **No confirmed award-winning 3D/AR restaurant site found.**
- **Mobile configurator pattern:** canvas in the top half, bottom-sheet dish list, sticky CTA. PLAUSIBLE, no source.

## Conversion evidence (weak)
- **Oxford/Qreal study** (2019, reported Aug 2020, older than 90 days, so flag as stale): with an AR menu 41.2% ordered dessert vs 18% without; dessert spend $2.93 vs $1.38. Sample size not stated; the study is vendor-linked. https://thespoon.tech/as-menus-move-to-mobile-phones-research-shows-ar-could-drive-more-sales/
- **Academic study** (858 participants): AR menus affect over-ordering intent via portion perception. Found only in a search summary. PLAUSIBLE
- **Vendor claim** of "2.4x uplift": UNKNOWN, no traceable source.
- **No data on reservations.** Treat any conversion lift as a hypothesis and measure reservation clicks.

## Fallbacks and GDPR
- **Reduced motion and no WebGL:**
  - `prefers-reduced-motion`: no autorotate and no camera moves; show a poster per dish.
  - No WebGL: photo grid plus text menu and the same reservation CTA.
  - Dish chips are keyboard-operable; alt text throughout.
- **GDPR:** LG Muenchen, 20 Jan 2022, 3 O 17493/20, awarded EUR 100 for loading Google Fonts from Google servers (IP transfer). https://www.dr-datenschutz.de/schadensersatz-urteil-google-fonts-und-die-dsgvo/ (PROVEN as reported; not legal advice). Rule: self-host three.js, model-viewer, fonts and models, with no third-party CDN.

## Open (UNKNOWN)
- real mobile budgets (profile on device)
- food scan quality (test scan of one dish)
- what the award sites actually do
- reservation conversion data
- whether model-viewer auto-generates USDZ

## Sources
- https://sparkjs.dev/
- https://modelviewer.dev/docs/index.html
- https://developers.google.com/ar/develop/scene-viewer
- https://developer.apple.com/augmented-reality/quick-look/
- https://thespoon.tech/as-menus-move-to-mobile-phones-research-shows-ar-could-drive-more-sales/
- https://www.dr-datenschutz.de/schadensersatz-urteil-google-fonts-und-die-dsgvo/
- https://forum.playcanvas.com/t/solved-large-3d-gaussian-splatting-file-doesnt-load-on-mobile/38758
- https://www.awwwards.com/inspiration/interactive-menu-les-ignobles
