# Scroll push-in film: web engine, shot spec, AE workflow (researched 2026-10-22, mb-researcher/sonnet)

Master Brain has no scroll-scrub method note, so this is fresh research. Tags: PROVEN (source) / PLAUSIBLE / UNKNOWN. Sources older than 90 days are flagged STALE.

## Recommendation
- **Engine:** a canvas image sequence. It is the only option with frame-exact, art-directed scrubbing on iOS. Ship two sequences: desktop 16:9 and mobile 9:16, both WebP.
- **Fallback / v1 prototype:** an all-intra `<video>`.
- **WebCodecs:** optional, not needed for v1.
- **Film structure:** 3-4 segments, joined by one blur whip and one push into a dark object. A true single take through the glass door is hard to shoot; don't plan around it.
- **AI video:** a candidate only for the impossible bridge shot. Not the default, because the restaurant must look authentic.

## Web implementation
1. **Canvas image sequence (Apple-style).**
   - Zajno's OPTIKKA moved from HTML5 video to frame sequences for control. Cost: 1000+ requests and more complex code. PROVEN (Codrops search summary; the page itself returned 403) https://tympanus.net/codrops/?p=103024
   - Use separate sequences per device (more frames on desktop). PNG to WebP gives about 90% size reduction. PROVEN (same source)
   - Starting numbers, UNKNOWN until tested:
     - desktop: 150-240 frames at 1920 wide, WebP quality about 70
     - mobile: 100-150 frames at 720x1280
     - payload: about 4-8 MB on mobile, 10-15 MB on desktop
   - Loading order: first frame, then nearby frames, then the rest, without blocking first paint. PLAUSIBLE (https://tessl.io/registry/skills/github/MengTo/Skills/scroll-scrubbed-visual-sequence)
   - Draw in `requestAnimationFrame`, only when the frame index changes. PLAUSIBLE
2. **`<video>` currentTime scrubbing.**
   - Seeks land on keyframes, so encode all-intra (`-g 1`). PROVEN (https://www.hontran.dev/blog/scroll-scrubbed-video-stutters-fix, 2026-07-09)
     - VP9: `-vf scale=1280:-2 -r 30 -c:v libvpx-vp9 -crf 32 -b:v 0 -g 1`
     - H.264 fallback: `-crf 23 -g 1 -movflags faststart`
   - A 1080p all-intra file can exceed 100 MB. PROVEN (same source)
   - Video attributes: `muted playsInline preload="auto"`; guard seeks with `readyState >= 1`. PROVEN (same source)
   - Firefox struggles with MP4, so serve WebM first; iOS needs MP4. PROVEN but STALE (2022, https://muffinman.io/blog/scrubbing-videos-using-javascript/)
3. **WebCodecs.**
   - Safari 26 support. PLAUSIBLE (verify on caniuse)
   - Needs a demuxer and a frame cache. More work for the same visible result.
4. **GSAP ScrollTrigger + Lenis.**
   - Wiring: `lenis.on('scroll', ScrollTrigger.update); gsap.ticker.add(t => lenis.raf(t*1000)); gsap.ticker.lagSmoothing(0)`. PROVEN (https://github.com/darkroomengineering/lenis)
   - Leave `syncTouch` off on iOS (unstable below 16). PROVEN (same)
   - Pin the section with `scrub: 0.5-1`. Scroll length about 400-600vh, UNKNOWN.
   - Fallback for `prefers-reduced-motion`: a static poster.
5. **Check before calling it working:**
   - With Fast 4G throttling, the first frame shows immediately.
   - Scrub holds 60 fps in the Performance trace.
   - Tested on a real iPhone. Motion feel stays UNVERIFIED until Max tries it on his trackpad.

## Shot spec (on-set checklist)
1. **Camera.** The researcher did not find a model named "FX50"; possibly FX3 or FX30. **Max to confirm.** FX30 dual base ISO 800/2500 is PROVEN (https://en.wikipedia.org/wiki/Sony_FX30).
2. **Frame rate and shutter.** 25p at 1/50 (180-degree rule) gives natural motion blur. Avoid fast shutters, which strobe when scrubbed. PLAUSIBLE
3. **Resolution.** 4K, with headroom to reframe.
4. **Picture profile.** S-Log3, at the correct base ISO. Protect the lit windows; accept deep shadows outside. Exact exposure values are UNKNOWN until the test shoot.
5. **Speed.** One constant walking pace per segment. Tape the start and end marks. Shoot 5 or more takes.
6. **Rig.** Gimbal at steady height on a straight centre line. Slider for the table close-ups.
7. **Framing.** Keep the subject in the centre 9:16 safe zone. Better: shoot a separate vertical pass for mobile.
8. **Joins.**
   - Whip: a fast pan at the end of one segment and the start of the next, covered with directional blur in AE.
   - Dark object: push into a door frame or a dark jacket, and emerge from dark in the next segment.
   - Match cut: line up the door-frame edges across the cut.
   - Keep direction and speed matched across every join.
9. **Plating and beer pour.** 50 or 100 fps for slow motion; the push comes from the slider.
10. **Glass front.** Reflections show the crew. Use a polarizer or shoot at an angle, and clean the glass.
11. **Blue hour.** About 20-30 minutes of usable light (PLAUSIBLE). Get one hero take per evening and shoot the interior beat separately with matched colour temperature.

## AE workflow
1. Grade the log footage with one LUT on an adjustment layer.
2. Even out speed with Time Remapping on linear keyframes; check the speed graph. Frame Blending vs Pixel Motion needs a test (Pixel Motion may artifact on glass).
3. Warp Stabilizer only on handheld shots, at low smoothing.
4. Whip join. PROVEN, STALE:
   - Recipe: adjustment layer over the cut, Motion Tile Center animated over about 10 frames, Directional Blur keyframed 0 to peak to 0.
   - Sources: https://photofocus.com/software/tutorials-software/easy-whip-swish-pan-in-after-effects/ and https://4kshooters.net/2015/08/21/liven-up-your-editing-by-creating-a-whip-pan-in-adobe-after-effects-or-premiere-pro
5. Export a PNG sequence per aspect ratio. Convert to WebP (ffmpeg or `cwebp`, quality 70-80). Retime to a fixed frame count, for example 180.
6. Verify in an HTML test page against the AE preview. Look for speed jumps and visible joins.

## AI option
- **Tools:** Kling 3.0 (about $0.10/s), Veo 3.1 and Runway Gen-4.5. PLAUSIBLE, marketing comparisons only:
  - https://www.frankx.ai/blog/ai-video-generation-2026-sora-runway-kling-veo
  - https://www.cometapi.com/vi/kling-3-0-vs-veo-3-1/
- **Through-glass start/end-frame interpolation:** UNKNOWN. Test it with a real photo of the glass front as the first frame and a real interior photo as the last frame.
- **Risks:** architecture drift, warped signage, flicker, misleading guests about the real place.
- **Use:** at most a 1-2 s bridge hidden in the blur whip, and only with Max's approval.

## Benchmarks
- **Zhar Burger:** pinned chapters, video scrubbed in both directions, lighter media on phones.
  - Live site: https://zhar-burger.onrender.com
  - Description: https://etsy-ai-agents.onrender.com/en/work/zhar-burger
  - Likely an AI-agent demo, so treat it as a UX pattern only.
- **OPTIKKA (Zajno):** a documented frame-sequence case (product, not hospitality). https://www.awwwards.com/balancing-scale-and-humanity-behind-the-scenes-of-optikkas-website.html
- **The Ink Experience:** engine UNKNOWN (only a screen recording was seen). Check the Network tab on the live site.
- **Real hospitality benchmarks with film-quality footage:** UNKNOWN. Search Awwwards and Godly for "restaurant scroll video".

## Open
- Camera model, and whether Max has a gimbal and slider.
- The reference's engine.
- Frame counts and payload. Run a 2-hour test with a 5-second walk-through in both aspect ratios.
- Whether Pixel Motion handles glass.
- Whether AI can produce the through-glass bridge.
- Hospitality benchmarks.

## Transfer to Master Brain (after the first working prototype)
- Reusable: the Lenis+GSAP wiring, the `-g 1` encode, the AE whip recipe, the 180-degree shutter rule.
- Proposed new note: `Methods/Scroll-Scrubbed Film.md`.
