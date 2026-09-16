# Work History - JS Halftone Animation Project

> **FOR FUTURE AGENT: Read this file first on every new session. Update it at end of every session. Do not ask user to re-explain. This is our persistent memory.**

## 1. Project Goal
- **Phase 1 (current):** Recreate `JS ANIMATION OG.mp4` - claimed "Every frame drawn with JavaScript". Portrait halftone / print-style story of a paper boat.
- **Phase 2 (next):** Personal custom animation with same technique, same halftone JS style, but user's own story. Do NOT waste time re-learning technique at that point - reuse engine from Phase 1.

## 2. How We Work (User Preferences - LEARNED)
- User shares visuals as video + extracted PNG frames in same folder. Agent must look at frames directly with Read tool.
- User wants direct code work in `C:\Users\acer\JS animation`, not just theory.
- User wants short, factual answers. No emojis unless asked.
- User closes sessions - memory is lost. So KEEP WRITING in this file: what we tried, what worked, what failed, how user likes to iterate.
- When referencing code, use `file:line` pattern.
- Verify by running / opening HTML, not by guessing.

## 3. Source Video - NOW ANALYZED (was missed before, fixed 2026-09-16)
- File: `C:\Users\acer\JS animation\JS ANIMATION OG.mp4`
- Specs: 1080x1080, 26.048s, 24fps, h264, 12MB, square.
- Cannot play video directly in Read tool. Workflow: use ffmpeg to extract frames, then Read JPGs.
- Extracted: `og_frames\og_%03d.jpg` = 52 frames at 2fps, scaled to 540px. Command used:
  `ffmpeg -y -i "JS ANIMATION OG.mp4" -vf "fps=2,scale=540:-1" "og_frames\og_%03d.jpg"`
- Old manual screenshots: `frame1.png` - `frame6.png`, `frame 7.png` = first 0-6s only. `orignal tweet.png` = X post by Kevin Ngo "Every frame drawn with JavaScript by Claude Opus 5."

### Full Story Timeline (from og_ frames):
- `og_001 (0.5s)`: Desk - teal patterned bg, blank white paper large center, yellow pencil bottom-right, faint red C top-left, small page icon top-right.
- `og_004 (2s)`: Same desk, paper shrinks, wavy scribble lines appear (code/writing placeholder).
- `og_007-010 (3.5-5s)`: TRANSITION to Day Lake. Sky white + blue halftone dots, yellow sun top-right + 3 yellow clouds, light blue mountains, olive hill with black reeds + red tips, deep blue lake with white sparkles + dark lily blobs foreground. White origami boat center with dashed fold line.
- `og_014 (~7s)`: Mountain Lake variant. Smaller yellow sun top-left, darker blue mountains, pine-tree silhouettes on shore.
- `og_018 (~9s)`: Town + Bridge. Yellow houses red triangle roofs, black bridge arches with reflections, birds.
- `og_022 (~11s)`: Lighthouse Night. Starry dark sky, red-white lighthouse left, large cream light beam to right, dark blue sea.
- `og_028 (~14s)`: Line Ocean. Top-down: dark blue with white squiggle waves + thick horizontal black lines (notebook lines become waves), ladder top-right. Boat crosses lines.
- `og_038 (~19s)`: Night House. Starry sky + pale moon, black cliff right with house + lit yellow window, white wake behind boat.
- `og_045-052 (22.5-26s)`: Finale Unfold. Split night sky top, wood-desk orange bottom, paper unfolds back to flat showing fold creases + scribbles. Text fades in: `opus 5 / claude`. Ends dark.

Key insight: SAME white boat persists through ALL scenes. Only background changes. Boat always: white hull + triangle sail + vertical dashed center line + slight bob/tilt.

## 4. Existing Files Inventory
- `halftone-boat-beautiful.html` (current best, 60fps fix 2026-09-16): Square 540. Statics pre-rendered to `bgCanvas`/`sunCanvas`/cloud sprites, per-frame only drawImage + 110 reeds + 55 sparkles + boat. Creamy clouds `#f0d67c`+`#a68a1a`. CSS breath, FPS meter. See Section 6.
- `halftone-boat.html` (169 lines): Original stiff loop, kept as reference. Has `dots()`, `dotsIn()`, `sunPath()`, `rand()`, `drawBoat()`, `loop()`.
- `stickman.html` (22 lines): Learning base for `requestAnimationFrame` + `sin` swing. Keep as reference.
- `stickfigure.png`: Simple stickman reference, unrelated to boat but shows user's learning path.
- `frame*.png`, `orignal tweet.png`: Early refs, superseded by `og_frames/`.
- `JS ANIMATION OG.mp4`: Ground truth.

## 5. Technical Approach (Agreed)
- Pure Canvas 2D, no libraries. Portrait canvas (480x720 in dev, final 1080x1080 square to match OG).
- Halftone = `dots(x,y,w,h,gap,color,radiusFn)` staggered rows + `dotsIn(shapeFn,bounds,...)` with `ctx.clip()`. Vary radius for shading.
- Motion = `Math.sin(t*speed+i)*amp` for bob/sway + `((offset+t*speed)%range)-margin` for drift + seeded `rand(i)=fract(sin(i*127.1)*43758.5)` for stable sparkles.
- Architecture needed: `timeline(t)` state machine switching scene draw functions + crossfades. Do NOT simulate real 3D origami - cheat with 2D polygon lerp between paper keys + dashed fold lines.
- Perf: few thousand `arc()` per frame OK at 60fps. If slow, pre-render static dot layers to offscreen canvas.

## 6. Learnings Log (APPEND ONLY - newest at bottom)
- 2026-09-16: Started from stickman loop to halftone lake. Learned user prefers frames over theory. Missed OG.mp4 initially - fixed by ffmpeg fps=2 extraction. Discovered video is 7-scene journey, not single lake. Next: build scene manager, not just polish lake.
- 2026-09-16 (beauty pass): Rebuilt lake for soul - gust(t) envelope, wave-coupled boat + wake + reflection, blobby clouds, 2-layer soft mountains, bird pass, camera breath. User: huge improvement vs stiff oval/dorito version.
- 2026-09-16 (perf fix): Beautiful v1 ran ~5fps + slowed whole PC. Cause: ~6-7k arc()/frame + noise1() per sky dot + createRadialGradient/frame + cloudPath rebuild 6x/frame + ctx.scale breath. Fix: pre-render bg/sun/clouds to offscreen once, per-frame only drawImage + reeds/sparkles/boat. Clouds -> creamy `#f0d67c`/`#a68a1a` per user. Sparkles 85->55. Breath moved to CSS transform. Added FPS meter. User decision: stay on lake polish next, story (lake->night vs full 7-scene) after.
- 2026-09-16 (missed prompts backfill): Session started from user feedback that `halftone-boat.html` was good but stiff/soulless - oval clouds, spherical drifting sun, dorito mountains, plain sin loop with no intent. User asked for beauty-focused session (not timeline yet). Mid-session network drop - user asked "is opencode working", confirmed working, resumed. Then user reported: (a) cloud color too mustard/saturated, wants creamy/suitable tone; (b) whole PC slowed + animation ~5fps jitter vs old smooth one. Entered plan mode (read-only): diagnosed per-frame dots/gradient/scale causes, offered palette (cream/warm-white/peach), perf (60fps-locked vs max-detail), story (lake-only vs lake->night vs full OG 7-scene). User picked: creamy, lake-polish-only, fix-first-then-polish in separate session. Then asked to commit important files to GitHub SSH `ATHARVA98711/Beautiful-JS-Animations-.git` for other-device work. Repo contains code only; large media (`JS ANIMATION OG.mp4`, `frame*.png`, `og_frames/`, `orignal tweet.png`) git-ignored, re-extract on new device via ffmpeg command in Section 3.
- 2026-09-16 (day->night): User confirmed 60fps fix works fabulously. Noted clouds still read yellow. Chose: calm starry night (no lighthouse yet), loop length undecided -> set 20s (day 0-8, dusk 8-12, night 12-18, dawn 18-20), clouds warm-white `#f5edd2` day -> grey `#5a6274` night via crossfaded sprites, sun fades + moon `#f2ead0` takes same spot, lilies green `#3a6b35` day -> dark night (were grey-black). Frog hop (3s lily->water) explicitly deferred, hook left in code comments. Impl: `bgNight`+`moonCanvas`+`cloudNight` pre-rendered once, per-frame 2x drawImage blend via nightK, 130 twinkle stars, reeds/tips/lilies lerped. [ADD NEW ENTRIES HERE with date + what worked/failed + user feedback]

## 7. Next Steps for Future Agent
1. Read this file, then open `halftone-boat.html` + view `og_frames\og_001.jpg`, `og_010.jpg`, `og_018.jpg`, `og_022.jpg`, `og_028.jpg`, `og_038.jpg`, `og_052.jpg` to re-ground.
2. Continue Phase 1: refactor lake code into `drawDayLake(t)`, then add `drawDesk(t)`, `drawFold(t,k)`, other scenes one by one.
3. After Phase 1 approved, ask user for Phase 2 custom story beats, reuse same boat + dots engine.
4. At end of session, update Section 6 with progress + Section 4 if files added.

## 8. How to View / Run
- View video frames: check `og_frames/` - no need to re-extract unless video changes.
- Run: open `halftone-boat.html` directly in browser. No build step.
- If new video shared: `ffprobe` for specs, `ffmpeg fps=2` to frames, Read 6-8 spread frames.
