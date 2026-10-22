# Food 3D capture pipeline: camera, capture spec, processing, web budgets (researched 2026-10-22, mb-researcher/sonnet)

No existing Master Brain note on food photogrammetry. Tags: PROVEN (source) / PLAUSIBLE / UNKNOWN. STALE = older than 90 days.

## Answer
- **Camera:** "FX50" was not found by search. The nearby Cinema Line bodies are FX30, FX3, FX2 and the new FX5. **Max to confirm the body.**
  - Stills ranking for dish capture: FX2 first (33MP, mechanical shutter), then FX30 (26MP).
- **Mesh vs splat:** a photogrammetry mesh with baked textures is the safest web deliverable. A splat is an option for glossy items when the viewing angle is limited. Both are PLAUSIBLE until tested on one real dish.

## Camera facts
- **FX2** (announced 2025-05-28, STALE). PROVEN, sources: https://petapixel.com/2025/05/28/the-sony-fx2-aims-to-be-more-than-a-video-focused-a7-iv-or-fx3-junior and https://www.provideocoalition.com/sony-announces-the-fx2-a-full-frame-cinema-camera-with-the-perks-and-limitations-of-the-a7-iv-sensor/
  - 33MP full frame (7008x4672)
  - mechanical shutter, 10 fps, 1/160 flash sync
  - 8-bit JPEG or 10-bit HEIF stills; RAW UNKNOWN
- **FX30, FX3 and FX5.** PROVEN from search snippets (https://cameradecision.com/... and https://www.the-digital-picture.com/News/Just-Announced-Sony-FX5-Cinema-Camera):
  - FX30: APS-C, 26MP (6192x4128)
  - FX3: full frame, 12MP (4240x2832)
  - FX5: 16.6MP full frame stacked, announced 2026-07-22
- **Mechanical shutter on FX30/FX3/FX5:** UNKNOWN. Check the Sony spec pages.
- **Stills vs 4K video frames:** 4K frames are about 8MP, below the stills sensors, so use stills. PLAUSIBLE

## Capture spec (on-set checklist)
1. **Settings.** Manual exposure, RAW if the body has it, otherwise the best JPEG/HEIF. Set white balance once with a grey card.
2. **Aperture.** Tripod, stopped down for depth of field. Pix-pro used f/16 to f/22. PROVEN for their tests.
   - Sources: https://www.pix-pro.com/blog/food-photogrammetry (2025-12-11, STALE) and https://www.pix-pro.com/blog/cross-polarization
3. **ISO and shutter.** Low ISO, shutter fast enough for the rig. Pix-pro reference values:

   | Subject | Aperture | Shutter | ISO |
   |---|---|---|---|
   | Cake | f/16 | 1/125 | 400 |
   | Broccoli | f/22 | 1/80 | 1600 |
   | Cross-polarized, dark room | f/22 | 2-7 s | 100 |

4. **Mesh vs splat rig.**
   - Mesh: a turntable works. Use a plain, evenly lit white base so the background has no features that confuse alignment. PROVEN (pix-pro)
   - Splat: no turntable; orbit the camera around the still subject. PROVEN (https://www.kiriengine.app/blog/how-to-capture-3d-gaussian-splats-kiri-engine)
5. **Photo count.**
   - Pix-pro: 56 photos per ring (PROVEN). Minimum 24 per spin at about 15 degree steps (PROVEN).
   - KabaQ: 96-114 photos per dish (PROVEN, https://www.capturingreality.com/Article-KabaQ-Future-Of-Food-Ordering).
   - **Plan:** 3 rings (about 20, 45 and 70 degrees) x 56 = about 170 photos. PLAUSIBLE
6. **Overlap.** 70-80% between neighbours. UNKNOWN, set by the test scan.
7. **Lens.** Prime or 24-70 (KabaQ used a 24-70). Longer focal lengths reduce distortion. Exact focal length UNKNOWN.
8. **Focus stacking.** Not needed if stopped down. UNKNOWN for food.
9. **Lighting.**
   - Soft, even light; KabaQ called lighting the key factor. PROVEN (https://sketchfab.com/blogs/community/using-real-life-objects-virtual-augmented-reality-kabaq, STALE)
   - Glossy sauces: cross-polarize. Polarizer on the light, CPL on the lens, rotate the CPL until highlights vanish. PROVEN (pix-pro). It costs a lot of light, hence the long shutter.
10. **Scale and colour.** Shoot a test frame with a scale marker and colour checker.
11. **Timing.** Food degrades (steam, melt, sauce sheen).
    - Shoot one ring, check it, then continue.
    - Have a hero plate plus a backup plate.
    - Aim for under 5 minutes per dish. PLAUSIBLE
12. **Turntable trigger.** Motorised turntable with an external intervalometer at a 2 s interval. PROVEN (pix-pro)
13. **Plate bottom.** For plated food, a top-only model is likely enough. PLAUSIBLE

## Processing pipeline
- **Photogrammetry:** RealityScan, Metashape or Pixpro. KabaQ ran Agisoft: align, dense cloud, mesh, texture. PROVEN
- **Splat:**
  - KIRI: crop the scene, then export PLY or a mesh. PROVEN
  - Postshot / Brush / Polycam: UNKNOWN (not verified)
- **Splats on glossy and transparent surfaces:** splats handle them better because they need no feature matching. PROVEN, snippet: https://bookstack.hku.nl/books/3d-scan/page/gaussian-splatting-online-and-local
- **Which looks less "waxy" (mesh or splat):** UNKNOWN. Build the same dish both ways and compare.
- **Blender cleanup:** decimate or retopo, bake textures. Start at about 20-50k triangles per dish (hypothesis).
- **Optimize:** `gltf-transform optimize in.glb out.glb --compress meshopt --texture-compress webp`; run `gltf-transform inspect` first. PROVEN, sources: https://gltf-transform.dev/cli and https://app.cinevva.com/guides/optimize-glb-for-web

## Web budgets
- **Textures:** cap at 1024-2048. Textures make up over 80% of file size. KTX2 cuts that by 70-90% and stays compressed on the GPU. PROVEN (cinevva)
- **Splat formats:**
  - SPZ is about 10x smaller than PLY; SOG is 15-20x smaller. PROVEN (https://developer.playcanvas.com/user-manual/gaussian-splatting/formats/, https://nianticspatial.com/blog/spz4)
  - Setting the SH bound to 0 plus compressed PLY took one file from 884.7 MB to under 60 MB and made it load in Safari. PROVEN (PlayCanvas forum)
- **Spark (three.js splat renderer):** reads PLY, SPZ, SPLAT, KSPLAT and SOG. PROVEN (https://github.com/sparkjsdev/spark)
- **Size per dish:** UNKNOWN until one real dish is exported and measured.

## Failure modes
- **Transparent or refractive (glass, beer, soup surface):** photogrammetry fails on these. PROVEN (https://isprs-archives.copernicus.org/articles/XLIII-B2-2022/695/2022/)
  - Plan: leave them out of the scan, or composite a modelled stand-in. PLAUSIBLE
- **Shiny sauces:** cross-polarize, or use a splat.
- **Thin items (herbs, fries) and dark sauces:** UNKNOWN. Test early.
- **Motion blur and moving shadows:** the main killers of splats. PROVEN (KIRI)

## Examples
- **KabaQ:** 96-114 photos per dish; processing time cut from over 3 h to about 1 h; quality rose with more photos and a DSLR (43 / 109 / 211 / 311 photo test). PROVEN, STALE (2016-17)
- **QReal x Denny's:** a QR code on the printed menu opens AR dishes. PROVEN (snippet, https://arinsider.co/2023/04/05/how-can-restaurants-use-ar/). Their quality is UNKNOWN.
- **Snapchat AR menus, Bentobox:** UNKNOWN

## Open
1. Max confirms the camera body. Then check RAW support and shutter type.
2. Overlap, focal length, ring angles: set by the test scan.
3. Mesh vs splat look, MB per dish, polygon target: build one dish both ways.
4. **Gate:** pilot one real Swabian dish before the section is promised to the client.
