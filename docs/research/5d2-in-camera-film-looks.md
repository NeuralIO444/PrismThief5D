# Canon 5D Mark II: In-Camera Film Looks — Research Report

*Prepared 2026-10-16 for PrismThief5D. Status: research only, nothing built. Items marked **[unverified]** were not confirmed from a primary source; items marked **[proposed]** are design suggestions, not sourced facts.*

## 1. Context

- **Repo today** (`NeuralIO444/PrismThief5D` @ `57d3e61`): spec-stage only (`README.md`, `docs/spec-2026-10-16.md`, `docs/references.md`). It is a *film-negative scanning workroom* (tethered 5D2 on a copy stand → gphoto2 → LibRaw → negadoctor-style inversion → contact sheet/print). No code, no issues.
- **Tension:** the spec's "Lois pass" kills "film look" presets. This report covers a different goal, so it should live in a separate doc/repo or come with an explicit spec amendment.
- **Goal of this work:** film looks **baked into the JPEG in-camera**, like a film camera. Shoot, get the look, no post.

## 2. What actually bakes into a 5D2 JPEG

Only the camera's own pipeline: **Picture Style** + WB/WB shift + HTP + ALO + (via Magic Lantern) a few DIGIC effects and vignette. External hardware can *steer* settings but cannot process the camera's JPEG. Picture Styles never touch RAW (CR2/MLV).

### 2.1 Picture Style (.pf2 / PF3)
- **PSE controls:** sharpness, contrast, saturation, colour tone, RGB gamma curve (Basic); Six Color-Axes; Specific Colors (HSL/Lab/RGB targets, hue range 30–180°, sat/lum 30–100, luminance curve up to 10 points). Do the curve first. [C1][C2]
- **PF2 drops:** Basic-pane tone curve, Six Color-Axes, Fine Detail sharpness (Fineness/Threshold). These are PF3-only. [C3][C4]
- **Contradiction:** the ML reverse-engineering thread says PF2 *does* hold separate R/G/B curves (no master curve) [M1], which conflicts with Canon's "tone curve not saved". **Unresolved.**
- **PF3 on 5D2: [unverified].**
- **Limits:** 3 user slots (User Def 1–3), loaded via EOS Utility (macOS/Windows; no Linux route documented). Styles can't be read back off the camera. sRGB only. Fine Detail-based styles won't register. [C5][C6][P1]
- **Existing packs:** free community PF2 film pack (Agfa RSX II, Astia, FP-100C, Pro 160C/S, 400H, Reala, Velvia 100F, Kodachrome, Portra 160NC) [P2]; the "154 VW Collection" [P3]; paid camera-profiles.com (the vendor itself says no halation/bloom/grain) [P1]; Canon official looks [C7]. Flat styles (Cinestyle, Marvels, VisionColor) are meant for grading, not no-post. Quality of the community packs: **[unverified]**.

### 2.2 Step-0 test (do first)
1. In PSE, save PF2 files with: (a) exaggerated per-channel curves, (b) Specific Colors only, (c) Six Color-Axes only. Also save one PF3.
2. Load each via EOS Utility, shoot a ColorChecker JPEG, and compare against Neutral.
3. Record what survives. This decides whether the LUT→PF3 tools (§8) apply to the 5D2.

## 3. Magic Lantern (5D2)
- **Build:** official dev `magiclantern.2025-06-20.5D2.212.zip`, needs Canon FW 2.1.2. There are also legacy lua_fix (2020) and nightly (2018) builds. [ML1] New DIGIC-era builds come with random-hang warnings (battery pull). [ML2]
- **JPEG-affecting:** Image Effects (Desaturate, Negative, Swap U-V, Cartoon; the wiki marks them "Obsolete?") and Vignetting (negative values add vignette). Whether either reaches **stills** JPEGs: **[unverified]**. [ML3][ML4][ML5]
- **Stills tools:** Kelvin/push-button WB, zebras, false color, spotmeter, RGB histogram, waveform/vectorscope, cropmarks, ghost image, focus peaking, Magic Zoom, intervalometer, bulb timer, bracketing (exp/flash/DOF), motion detect, trap/stack/rack/follow focus, Auto ETTR, Dual ISO (RAW, needs cr2hdr), silent pictures / FRSP (long exposures only). [ML6][ML3][ML7]
- **Raw video:** 14-bit only, ~1856×1044 continuous at ~75 MB/s on Lexar 1000x/1066x CF. crop_rec 3K/3.5K/4K-anamorphic builds are experimental and freeze-prone (2019–2020). No SD slot. [V1][V2][V3][V4]
- **Not possible:** in-camera multiple-exposure merging [ML8]; an in-camera LUT or grain module (none exist). A JPEG decode/re-encode module is theoretically possible via ImagePlayDriver/JPCORE [ML9][ML10], but it would run on an ARM946E at 200 MHz. Estimated seconds-to-tens-of-seconds per frame **[unverified estimate]**. Feasibility: low.

## 4. The 12 presets (shooting cards)

Card values are **[proposed]** starting points to tune in step-0/derivation. HTP = Highlight Tone Priority (C.Fn II-3, min ISO 200, smoother highlight shoulder, slightly more shadow noise) [C8][PL1]. ALO = Auto Lighting Optimizer (C.Fn II-4; inactive in M/Bulb) [C9]. PF2 feasibility is per §2.

| # | Preset | Look notes | ISO | WB / shift | HTP | ALO | PF2 fit |
|---|---|---|---|---|---|---|---|
| 1 | Tri-X 400 | Monochrome base, punchy mids, deep blacks; noise acts as grain | 400–1600 | Daylight; mono filter Y/O | On (daylight) | Off | High |
| 2 | Portra 400 | Soft shoulder, warm, low sat, protected skin (Specific Colors) | 200–800 | Daylight, slight A/M | On | Off | High |
| 3 | Ektar / Velvia | High sat and contrast, pushed blue/green | 100–200 | Daylight | Off | Off | High |
| 4 | Kodachrome | Dense reds/yellows, deep blue skies | 100–200 | Daylight | Off | Off | Medium |
| 5 | CineStill 800T (colour only) | Tungsten-balanced, cool shadows; **no halation in-camera** | 800 | ~3200K, slight B | Off | Off | Medium |
| 6 | Night Tungsten 400 | Cool, high contrast, controlled oranges, moody streets | 400 (push 800) | ~3200K, B shift | Off | Off | Medium |
| 7 | Push 1600 | High contrast, lifted blacks, coarse noise | 1600 | Daylight | Off | Off | Medium |
| 8 | Cine 200 Daylight | Soft, low-contrast curve, muted | 200 | Daylight | On | Off | High |
| 9 | Consumer 200 Flash | Warm, saturated, for on-camera flash | 200 | Flash/Daylight, A shift | Off | Off | High |
| 10 | Pastel 400 | Lifted blacks, low sat, airy highlights | 400 | Daylight, slight G/M | On | Off | Medium-High |
| 11 | Expired Stock | Colour cast (magenta/cyan), faded blacks, low contrast | 400–800 | Shifted WB | On | Off | Medium |
| 12 | High-Contrast Mono 3200 | Monochrome, hard curve, crushed blacks, heavy noise | 3200 | Daylight; red filter | Off | Off | High |

Only 3 can be loaded at once. Swap them by roll (see §7). Night Tungsten / Push / Cine / Consumer / Pastel / Expired come from the "themed stocks" idea in §5.

### 4.1 Derivation method [proposed]
- Shoot a ColorChecker in daylight and tungsten on the 5D2 in RAW.
- Shoot the same chart on the real stock and develop it through PrismThief5D's develop stage (or use published scans as a fallback).
- Fit per-channel curves to the grey patches, then Specific Colors to the colour patches.
- Verify in DPP, then on the camera. Iterate.

## 5. Reference film shop: "themed stocks"
A reference film shop (Melbourne) sells house-branded 35mm stocks with mood names (push 1600, flash 200, cine 200, tungsten 400T, 800, mystery). It sells **no** presets or digital sims. Its 400T is tungsten-balanced: cool in daylight, ISO 400 (800 usable), strong contrast, remjet so no halation. [R1]
**Equivalent:** original-named in-camera "stocks" = Picture Style + shooting card (ISO/WB/shift). These became presets 6–11 above.

## 6. Hardware push

| Item | Facts | Film-like usefulness | Risk |
|---|---|---|---|
| Split-prism / Eg-S screen | Canon offers Eg-A/D/S; third-party split screens set as "Eg-S" [C10][S1] | High (manual focus) | Low |
| M42 / Contax C/Y adapters | Some lenses hit the mirror at infinity; C/Y ~1.425 mm adapter; LiveView workaround [A1][A2][A3] | High | Low–Med |
| Anamorphic (Iscorama) | ≥50 mm primes, spacers, vignetting [A4] | Low–Med | Low |
| Black Pro-Mist | Closest in-camera halation-like glow; no true red halation [F1][F2] | High | Low |
| Mono (CFA removal) | 5D2 listed; limited availability; Canon ceramic coating is hard [H1][H2][H3] | Very high (Tri-X body) | High, irreversible |
| IR / full spectrum / HR | MaxMax converts 5D2 [H1][H4] | Medium | Med–High |
| Bare sensor (anuejn/2d5) | Not usable yet [G1] | None now | — |
| CPU/DIGIC swap | Not feasible (no source of any such mod; DIGIC is an SoC) **[unverified]** | — | — |
| Battery-grip UART, cooling, overclock | No working sources found | — | — |

## 7. Microcontroller / roll controller

| Interface | Facts | Feasibility | Bakes into JPEG? |
|---|---|---|---|
| N3 remote | GND/focus/shutter shorts; use an optocoupler [N1][N2][N3] | Very high | No (trigger) |
| USB PTP | felis Arduino libs: capture, bulb, LV frames 0x9153, MoveFocus, SetProperty incl. **PictureStyle 0xD110**, WB 0xD109, Unix time; gphoto2 sets style/WB/ISO [U1][U2][U3][U4] | High | **Yes, indirectly** (switches settings) |
| PTP/IP | Documented for wireless EOS [U5]; 5D2 has no built-in Wi-Fi (WFT-E4 **[unverified]**) | Medium (Pi bridge) | Same as PTP |
| .pf2 upload via PTP | No public gphoto2 command or opcode; EOS Utility only [C5][U4] | Low | — |
| Hot shoe E-TTL | 5-pin, camera-clocked ~50–104 kHz, sniffed with Arduino+LM339; X-sync = exposure detect [E1][E2][E3][E4] | High (sniff), Med-Low (emulate) | No |
| EF lens | SPI mode 3 ~80 kHz, lens busy-ack; focus 0x44, aperture 0x12/13 [L1][L2][L3][L4] | Medium | Optically only |
| CF + FlashAir | Lua on write, Wi-Fi upload; SD→CF ~18 MB/s; FS-corruption risk on write-back [W1][W2][V4] | High (off-camera copy) | No |
| ML ↔ external | No working 5D2 UART; LED/audio side channels need custom code [U6][U7][U8] | Low | — |

**Recommended roll controller [proposed]:** Pi Zero 2 W or AVR+MAX3421E. Load styles once with EOS Utility, then per "roll" set User Def slot + WB + shift + ISO over PTP, show a frame counter (36), trigger via N3, and optionally pull JPEGs for an off-camera grain/halation copy. RP2040 PTP host stack: **[unverified]**.

## 8. Reverse-engineering avenues
- PF2/PF3 format partly documented; styles likely hold ICC/3D-LUT data (PF3 ≈ 33³ tables). Tool work stalled → room for an open-source PF2 writer. [M1][M2][M3]
- LUT→style tools exist but **export PF3 only**: Canon Style Studio (open source, Windows, needs PSE, alpha) [T1][T2]; Film Recipe Hub (browser, LUT/XMP/image → PF3; PF2/PF3 → .cube) [T3].
- DIGIC register poking for tints (experimental) [ML11]; ImagePlayDriver JPEG paths [ML9].

## 9. Easiest path: macOS preset manager
- **[proposed]** A small macOS app holding the library of 12 `.pf2` + shooting cards. It shows which 3 are loaded, then hands off to EOS Utility to register them (macOS/Windows only; no Linux upload path documented [C5]).
- Later: automate slot switching over PTP (§7). Styles can't be read back, so the app is the source of truth [P1].

## 10. Cool extras
- HTP for a filmic highlight shoulder [C8][PL1]; two custom-function setups (HTP on/off) [PL1]
- Black Pro-Mist [F1][F2]; split-prism screen [S1]
- ML Ghost Image for aligning double exposures; Exposure Fusion / Compare Images previews [ML7]
- Image review off = no chimping [ML7]
- Canon "film recipes" community gap: an opening for shareable cards [Q1]
- Not sourced this pass: pinhole cap, Helios swirl, Lensbaby, freelensing, tilt-shift, soft release, EXIF stock tagging.

## 11. Open questions & test plan
1. Step-0 PF2/PF3 test (§2.2).
2. Do ML Image Effects and Vignetting apply to stills JPEGs on the 2025 build?
3. 5D2 support in gphoto2/felis, and how User Def slots map in 0xD110.
4. Mono/IR converter availability and price.
5. Per-stock curve fits, and judging against real film scans.
6. Where this lives: a PrismThief5D spec amendment vs a new repo.
7. Risks: ML firmware/hangs, mirror strikes, irreversible sensor mods, FlashAir write-back corruption.

## Sources
- C1 https://cam.start.canon/en/S004/manual/html/UG-03_ImageChara_0020.html
- C2 https://cam.start.canon/en/S004/manual/html/UG-03_ImageChara_0040.html
- C3 https://cam.start.canon/tc/S004/manual/html/UG-04_Saving_0020.html
- C4 https://gdlp01.c-wss.com/gds/4/0300023444/13/Picture_Style_Editor_1.24_Mac_EN.pdf
- C5 https://support.usa.canon.com/kb/s/article/ART168657
- C6 https://cam.start.canon/en/S003/manual/html/UG-02_CameraSetting_0020.html
- C7 https://global.canon/en/imaging/picturestyle/file/download.html
- C8 https://support.usa.canon.com/kb/s/article/ART104083
- C9 https://manualsdump.com/en/manuals/canon-eos_5d/208469/177
- C10 https://support.usa.canon.com/kb/s/article/ART108671
- P1 https://www.camera-profiles.com/faq
- P2 http://www.slrclub.com/bbs/vx2.php?id=canon_d30_forum&no=4530898
- P3 https://www.photo4u.it/viewtopic.php?t=520089
- PL1 https://prolost.com/blog/2009/8/3/flatten-your-5d.html
- M1 https://www.magiclantern.fm/forum/index.php?topic=16299.275
- M2 https://www.magiclantern.fm/forum/index.php?topic=16299.225
- M3 https://www.magiclantern.fm/forum/index.php?topic=20991.0
- ML1 https://builds.magiclantern.fm/5D2.html
- ML2 https://www.magiclantern.fm/forum/index.php?topic=27355.0
- ML3 https://wiki.magiclantern.fm/camera_help
- ML4 https://builds.magiclantern.fm/old/features.html
- ML5 https://www.magiclantern.fm/forum/index.php?topic=18419.0
- ML6 https://direct.magiclantern.fm/
- ML7 https://wiki.magiclantern.fm/userguide
- ML8 https://www.magiclantern.fm/forum/index.php?topic=10037.0
- ML9 https://www.magiclantern.fm/forum/index.php?topic=15382.0
- ML10 https://fahrplan.events.ccc.de/congress/2013/Fahrplan/system/attachments/2246/original/Magic_Lantern_30C3.pdf
- ML11 https://magiclantern.fandom.com/wiki/Register_Map/Brute_Force
- V1 https://www.eoshd.com/raw-video/magic-lantern-raw-video-current-camera-capabilities-updated-2017/
- V2 https://www.magiclantern.fm/forum/index.php?topic=22301.0
- V3 https://www.magiclantern.fm/forum/index.php?topic=19336.0
- V4 https://www.outsidetheshot.com/canon-5d-mark-ii-memory-cards/
- R1 Reference film shop (Melbourne online film retailer; link omitted)
- S1 https://www.focusingscreen.com/work/5d2en.htm
- A1 https://www.photo.net/forums/topic/428019-m42-on-5d-mk-ii-as-opposed-to-any-other-eos/
- A2 https://www.pebbleplace.com/databases/contax_database.html
- A3 https://forum.mflenses.com/contax-c-y-28mm-f2-mirror-problems-with-5dmkii-t32371.html
- A4 https://cinematography.com/index.php?%2Fforums%2Ftopic%2F37554-anamorphic-for-canon-5d-mark-ii%2F=
- F1 https://tiffen.com/products/black-pro-mist-filter
- F2 https://tobiasdeml.com/cinematography/halation-film-stock-digitally-imitating-color-grading/
- H1 https://maxmax.com/IRCameraConversions.htm
- H2 https://www.cloudynights.com/forums/topic/418903-monochrome-conversion-of-most-digital-cameras-yes/
- H3 https://monochromeimaging.com/odering/
- H4 https://www.maxmax.com/maincamerapage/monochrome-cameras/olpf-study
- G1 https://github.com/anuejn/2d5
- N1 https://pinoutguide.com/DigitalCameras/canon_n3_pinout.shtml
- N2 https://camerahacker.com/Canon/Canon%20N3%20Connector%20Pin-Out.php
- N3 https://www.martyncurrey.com/activating-the-shutter-release/
- U1 https://github.com/felis/PTP_2.0/blob/master/canoneos.h
- U2 https://github.com/felis/Arduino_Camera_Control/tree/master
- U3 https://github.com/felis/USB_Host_Shield_2.0
- U4 https://gphoto.github.io/doc/remote/
- U5 https://julianschroden.com/post/2023-08-19-remote-live-view-using-ptp-ip-on-canon-eos-cameras/
- U6 https://www.magiclantern.fm/forum/index.php?topic=1761.0
- U7 https://wiki.magiclantern.fm/uart_connectors
- U8 https://www.magiclantern.fm/forum/index.php?topic=8912.0
- E1 https://billgrundmann.wordpress.com/2009/03/04/ettl-interface/
- E2 https://blog.csdn.net/taboo/article/details/6070455
- E3 https://forum.arduino.cc/t/sniff-circuit-traffic/17487
- E4 https://web.media.mit.edu/~bandy/invariant/move_lens.pdf
- L1 https://gist.github.com/marcan/858c242db2fc595da1e0bb70a05192fc
- L2 https://pickandplace.wordpress.com/2011/10/05/canon-ef-s-protocol-and-electronic-follow-focus/
- L3 https://howiem.org/wordpress/2016/07/07/motion-control-canon-ef-lens-hacking/
- L4 https://panarilab.hatenablog.com/entry/2021/01/23/123628
- W1 https://petrst.github.io/flashair-developers-site/en/documents/tutorials/iot-hub/3/index.htm
- W2 https://aggregate.org/CACS/flashair.html
- T1 https://github.com/LvClFER/Lut-to-Canon-Picture-Style
- T2 https://www.reddit.com/r/CanonCamera/comments/1vpvr1z/lut_to_canon_picture_style/
- T3 https://filmrecipehub.com/tools
- Q1 https://www.reddit.com/r/canon/comments/rvrqmg/film_recipes_for_canon_cameras/
