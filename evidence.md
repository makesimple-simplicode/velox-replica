# Velox Themes replica — evidence

## Capture record
| Field | Record |
| --- | --- |
| Source | https://veloxthemes.com/ (final: same), captured 2026-09-29 |
| Environment | ref screenshots: thum.io real Chrome, width 1440 fullpage (1440x18460) + 768x1024 + 390x844; DOM/CSS probe: Lightpanda 1920x1080 DPR1 |
| State | top route `/`, no menu/modal open, above-the-fold + full scroll (fullpage render) |
| Loading | fonts Geist (gstatic, loaded), images lazy-loaded (naturalWidth 0 in probe — URLs captured instead) |
| Evidence | ref_desktop_full.png + full_00..11.png chunks, ref_tablet.png, ref_mobile.png, framer.css (302KB inline), dom_outline.json, page_text.txt, assets.json |
| Coverage | all 17 sections + footer inspected; quiz popup modal, nav dropdown open state, video playback NOT observed (blocked in probe engine) |

## Measured values (from live CSS/DOM)
- Colors: ink `#0a0a0a`, black `#000`, muted `#a0a0a0`, red accent `#de362a` (rgb 222,54,42), light `#f1f1f1`, white bg `#fff`, dark red `#961e15`.
- Font: Geist (primary), Inter (fallback), Fragment Mono (mono labels). Sizes seen: 58/64/48/40/24/16/14/12px.
- Breakpoints: `max-width:1199px` (tablet), `max-width:809px` (mobile), `min-width:810px`.
- Transitions: `color .4s cubic-bezier(.44,0,.56,1)`; `opacity .4s ease-out`.
- position:fixed present (nav + banners, 5 els). No @keyframes in static CSS. will-change:transform on 38 els (Framer effect layers).

## Motion record (estimates flagged — JS runtime unobservable in probe engine)
| Target | Trigger | States | Timing | Evidence |
| --- | --- | --- | --- | --- |
| Section content reveal | scroll into view | opacity 0→1, translateY ~24px→0 | ~0.6s ease-out (ESTIMATE, Framer appear) | estimate: no getAnimations in probe |
| Link/button color hover | hover | color→ muted/red | .4s cubic-bezier(.44,0,.56,1) | MEASURED (CSS) |
| Top red banner ticker | always | infinite horizontal marquee | constant speed (ESTIMATE ~30s/loop) | OBSERVED full_00 |
| 3D carousel (component card) | always | infinite 3D-perspective horizontal loop | ESTIMATE ~20s | OBSERVED full_01/02 |
| Video marquee (black section) | always | infinite horizontal scroll, cards w/ play btn | ESTIMATE | OBSERVED full_05 |
| Card hover | hover | image scale ~1.04 + lift | ESTIMATE | typical Framer; not observable |
| Hero red line + red period | load | draw-in / static accent | ESTIMATE | OBSERVED static full_00 |
| Nav dropdown | hover/click | menu appears, arrow rotate | ESTIMATE | not observed (state blocked) |
| FAQ accordion | click | row expand, red + ↔ − | height/opacity ~0.3s ESTIMATE | structure OBSERVED full_07 |
| Fixed nav | scroll | stays top, white bg | — | MEASURED position:fixed |

## Discrepancy log (post-build)
| Component/state | Mismatch | Priority | Fix | Recheck evidence |
| --- | --- | --- | --- | --- |
| Section reveal on fullpage capture | thum capture raced 0.7s transition → faded text | high | resolved: transition 0.4s + `?static=1` capture stabilization (logged) | band2_components: text dark ✓ |
| Footer social icons | placeholder glyphs ≠ X/Threads/IG/LinkedIn | low | resolved: 𝕏 @ ◎ in + ©2026 | band2_tail ✓ |
| Hours section | heading wrap + row tint + strip color | med | resolved: max-width 520, rgba red tint rows/strip | band4/band2 ✓ |
| Framer Components heading | missing entirely | high | resolved: heading + subtext added | band4_carousel ✓ |
| 3D carousel content | landscape UI shots vs ref people portraits | med | resolved (approx): 4 people portraits cycled; frame density smaller than ref | band5_carousel ✓ |
| Framer chip icon | block glyph | low | resolved (approx): Framer-blocks SVG, not official glyph | band5 ✓ |
| CTA red arc | missing | low | resolved: static SVG arc (ref motion unobserved → static) | band4_cta ✓ |
| True mobile viewport (390 CSS px) | thum `width/390` renders desktop layout scaled (both sides) | med | approximate: media queries 810/1199 present in source; true-mobile render unverified | — |
| Quiz 20% OFF popup | auto/exit-intent trigger unknown | low | approximate: manual modal, no auto-show | — |
| Motion timing (hover/entrance) | Lightpanda tanpa getAnimations; thum noanimate | low | approximate: measured CSS .4s only; JS timings labeled estimates | motion record |
| Framer editor demo video | plays via hotlinked mp4 | low | implemented; playback unverified in static captures | — |
