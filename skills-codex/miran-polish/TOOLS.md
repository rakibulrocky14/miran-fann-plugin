# The last pass — tool reference (generated)

Source: the runtime registry via `kinetic schemas` — do not hand-edit; run `bun scripts/gen-docs.ts`.
18 of 181 tools — the ones this skill teaches. The full registry lives in docs/TOOLS.md.

## inspectMotionActivity

Measure how much the picture moves over time: renders the range and reports, per second, the share of the frame that visibly changes, plus still holds, a weak opening and a frozen ending as findings. Run it on a draft before exporting.

- family: `design` · mutating: `false` · transport: CLI `kinetic tool inspectMotionActivity` / SDK `batch([{tool: "inspectMotionActivity"}])` / MCP `tools/call {name: "inspectMotionActivity"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `inspectMotionActivity {backend?: auto|gpu|cpu = "auto", end?: int [frame], start?: int = 0 [frame], step?: int = 1 [frame]}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `start` | integer | optional default=0 | — First frame (default 0) |
| `end` | integer | optional | — End frame, exclusive (default: the composition duration) |
| `step` | integer | optional default=1 | (min=1) — Sample every Nth frame (1 = every frame; 2 halves the cost on long pieces) |
| `backend` | enum["auto"|"gpu"|"cpu"] | optional default="auto" | — auto (default) renders on the GPU (Metal) when an adapter exists and on Flint, the CPU renderer, otherwise |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Whole piece, every other frame

  ```json
  {"step":2}
  ```

- One beat

  ```json
  {"start":0,"end":45}
  ```

Tips:

- Contact sheets hide timing; this measures it. Run it on every draft before exporting and fix each finding (it names frame ranges).
- Open strong: the first 2 s should be at least as lively as the rest. A frozen end card reads as a dropped frame: keep a slow push or drift on it.
- A still hold for reading is fine up to about 1 s; longer ones need secondary motion (noise float, slow scale, drifting gradient).

Related: `motionSummary`, `renderContactSheet`, `animateProperty`, `setPropertyDriver`

CLI: `kinetic tool inspectMotionActivity --params '<json>' --workspace ./out`

## critiqueMotion

How things move, like an animation director: straight diagonal travel (use arcs), constant-speed moves, entrances that accelerate into a dead stop, lockstep timing, one tempo for everything, nothing overshooting or following through, jumps, an empty first frame; and, from the frames themselves (the 3D camera too), jolts from rest, dead stops, speed jumps at hand-offs, creeping tails, stops on a beat, fast moves without blur. Score, findings and a fix call for each.

- family: `design` · mutating: `false` · transport: CLI `kinetic tool critiqueMotion` / SDK `batch([{tool: "critiqueMotion"}])` / MCP `tools/call {name: "critiqueMotion"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.critiqueMotion(...)` in runScript or query · inside runScript: yes
- signature: `critiqueMotion {frames?: number[] [frames], ignore?: array|string|int}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `ignore` | array\|string\|integer | optional | — Layers whose motion is intended: names or ids (reveals are skipped already); "camera" leaves the 3D camera out |
| `frames` | array | optional | — [from, to]: check only this range of frames (a long film's result names the ranges it did not sample) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Review how the whole film moves

  ```json
  {}
  ```

- Leave intended motion out (a typing wipe's cover, a calm drift): layer names or ids

  ```json
  {"ignore":["Accent"]}
  ```

Tips:

- Findings come from the moves themselves (channels, paths, follows) and, for smoothness, from the film's own frames: where each subject layer sits on screen at every frame, and, when a layer is 3D, how the camera moves the scene (long films: the windows around moves, keys and drivers, spread over the whole film; stats.sampled_frames says how many, and a sample_cap note names the ranges the cap left out: critiqueMotion {frames:[from, to]} checks one range). It takes a second or two on a long film. Each finding gives the layer, frames and a fix that names a tool call (a batch when several properties change); apply it and run the check again.
- Smoothness findings, judged on the frames a layer is shown (opacity 0.5 or more, on screen), so an entrance that starts hidden or off-frame is exempt from its start; speeds and lengths are judged in seconds, so 24, 30 and 60 fps get the same verdict: first_frame_jump (leaves rest with more than 3% of the move in its first frame and faster than 2x the move's average speed, or at full speed from the first frame, as linear keys are: use arrive, glide or easy-ease; strong ease-outs such as smooth and emphasized-decelerate are for layers that start hidden), dead_stop (arrives at speed and stops in one frame: glide), velocity_jump (the speed steps in one frame while moving, as when a second move takes over: auto keys on every axis that moves; a step that turns the layer round is an impact, a bounce, and is not judged), tail_crawl (more than 0.2 s at 0.6-9 px a second, which shimmers: another ease, a shorter move, or hold still; a layer that is meant to drift is marked), periodic_stop (three or more near-stops without turning round at an even beat, as lattice noise or back-to-back eased moves make: auto keys; a bob, a pendulum and a ping-pong turn round and are meant), stop_between_moves (a note: one stop between two moves the same way), fast_no_blur (a step above 2.5% of the width per frame with no motion blur, shutter or sized blur effect) and transition_peak (a frame that travels 15% of the width at 30 fps while the blur covers under 45% of it: turn comp blur on, shutter 180, or size a directional blur to it). Keyframe-driven layers are fixed on their keys (setPropertyDriver with keys), moves with animateProperty. A layer that is meant to snap (hold keys), step on twos (posterize, hold keys, posterizeTime), drift or stop on a beat is marked setLayerField {field: "intended", value: true} (a stage carries its children; ignore: ["camera"] leaves the 3D camera out); a stage's children that ride one move are one finding. The camera is judged for jolts, dead stops and blur only: a slow dolly is how a camera should move.
- Scenery (backgrounds, rigs, adjustment layers) may drift at constant speed; subjects should not.
- Reveals are not judged: the source of a track matte, a layer that never shows, a plate painted in the colour of what is behind it (a typing wipe's cover), and mask-path or trim-path animation. not_judged in the result names them. For anything else that is meant, pass ignore: [layer names or ids] (a name that is no layer is an error), or mark the layer once with setLayerField {field: "intended", value: true}. Its exit is not read time: text leaving with its shot is not flagged, and letters used as texture (ghost type, a wall of glyphs, faint ink) are not text.

Related: `animateProperty`, `setLayerField`, `critiqueLayout`, `inspectMotionActivity`

CLI: `kinetic tool critiqueMotion --params '<json>' --workspace ./out`

## critiqueLayout

Where things land on screen: text hitting art or other text, art over text, stacked text that almost lines up, text past the safe area or too small, empty frames, frozen holds. Geometry only, fast.

- family: `design` · mutating: `false` · transport: CLI `kinetic tool critiqueLayout` / SDK `batch([{tool: "critiqueLayout"}])` / MCP `tools/call {name: "critiqueLayout"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.critiqueLayout(...)` in runScript or query · inside runScript: yes
- signature: `critiqueLayout {frames?: number[] [frames], platform?: reels|tiktok|shorts, safe?: number|string|object = 0.05}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `frames` | array | optional | — Frames to check; default samples the whole composition |
| `safe` | number\|string\|object | optional default=0.05 | — Margin as a fraction of the frame; or {left, top, right, bottom}; or a platform name |
| `platform` | enum["reels"|"tiktok"|"shorts"] | optional | — 9:16 app UI zones (top bar, caption block, button column); kit.safe({platform}) gives the clear box |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Check the whole film

  ```json
  {}
  ```

- Check held frames with a wider safe area

  ```json
  {"frames":[40,100,140],"safe":0.08}
  ```

- A Reels / TikTok / Shorts teaser: text must clear the app's top bar, caption block and button column

  ```json
  {"platform":"reels"}
  ```

- Your own asymmetric margins (fractions of the frame)

  ```json
  {"safe":{"top":0.12,"bottom":0.22,"right":0.13}}
  ```

Tips:

- Only landed layout counts: layers still flying in or fading are skipped, so overlaps during motion are fine. Text past the safe area is not reported while its layer (or the scene group it rides on) is in fast transit, faster than 0.6 frame widths a second (2% of the width per frame at 30 fps), such as a card flung out during a whip; collisions of slow or still layers stay flagged.
- sparse and empty-area are notes, not errors: calm styles leave space on purpose. frozen means nothing moved: add kit.drift or kit.float, or cut sooner.
- A 9:16 Reels, TikTok or Shorts teaser: platform: "reels" (or tiktok, shorts) checks against the app's own interface: about the top 270 px, the bottom 420 px and a 140 px column on the right of a 1080x1920 frame for reels, scaled to your size. The result's safe lists the margins in px; kit.safe({platform}) returns the same box to lay out in (guide design, guide kit).

Related: `measureText`, `renderContactSheet`, `inspectMotionActivity`, `reorderLayer`, `critiqueMotion`

CLI: `kinetic tool critiqueLayout --params '<json>' --workspace ./out`

## critiqueEdit

QC a cut: flash, black and frozen frames; jump cuts; eye trace across cuts; montage cuts off the beat; generated shots that do not match; sound (pops, clipping, loudness, music over speech, dead air); captions (speed, lines, time, faces, app UI, contrast); retention for shorts; flashes (photosensitivity, approximate). Three worst moments per check with thumbnails. A job when long.

- family: `design` · mutating: `false` · transport: CLI `kinetic tool critiqueEdit` / SDK `batch([{tool: "critiqueEdit"}])` / MCP `tools/call {name: "critiqueEdit"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none (it renders the whole cut: call it directly)
- signature: `critiqueEdit {checks?: frames|jump|eye|beat|mixed|audio|captions|retention|…[], platform?: reels|tiktok|shorts, target_lufs?: number, thumbs?: bool}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | — The cut (default the first composition) |
| `checks` | array | optional | — Only these checks (default all) |
| `platform` | enum["reels"|"tiktok"|"shorts"] | optional | — captions: the app whose interface to keep clear of |
| `target_lufs` | number | optional | (min=-30, max=-5) — audio: loudness target (default the composition's, else -14) |
| `thumbs` | boolean | optional | — Small images of the worst moments (default true) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- QC the whole cut before export

  ```json
  {}
  ```

- A vertical short for TikTok: captions clear of its interface

  ```json
  {"platform":"tiktok"}
  ```

- Only the sound and the flashes, against a -16 LUFS target

  ```json
  {"checks":["audio","flashes"],"target_lufs":-16}
  ```

Tips:

- Run it on the finished cut, before renderVideo. It makes one small draft render of the whole composition (cached until the project changes) and reads the mix as the export hears it, so a long film returns {job}: poll jobStatus.
- Each failed check gives up to three worst moments: a frame, what is wrong and the fix, and a small image. Look at the images, fix, and run it again; a check that is a style on purpose (jump cuts in a vlog) can be left, so say so to the user.
- flashes is an approximate photosensitivity check on a 10 x 10 grid after the WCAG flash rules (more than three flashes a second over a quarter of the screen, saturated red counted on its own): it is not a certified Harding test. A strobe or fast flicker fails it; slow it under 3 a second or shrink it.
- captions reads text layers named "Caption ..." (kit captions) or under a "Captions" null, each caption set (the null its layers hang from) as its own, and measures the contrast of every colour a word shows (its highlight too) against the picture behind it, through its plate or outline; mixed reads generated clips by the sidecar generateVideo writes; retention runs on 9:16 films of 3 minutes or less.

Related: `edit`, `setAudioGain`, `grade`, `critiqueLayout`, `renderVideo`

CLI: `kinetic tool critiqueEdit --params '<json>' --workspace ./out`

## renderContactSheet

Grid PNG at cell size (GPU); warm-cell `eta`.

- family: `render` · mutating: `false` · transport: CLI `kinetic tool renderContactSheet` / SDK `batch([{tool: "renderContactSheet"}])` / MCP `tools/call {name: "renderContactSheet"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.renderContactSheet(...)` in runScript or query · inside runScript: yes
- signature: `renderContactSheet {frames: array|string|int, out: string, backend?: auto|gpu|cpu = "auto", columns?: int = 4, ignore?: array|string|int, max_width?: int = 2400 [px], platform?: reels|tiktok|shorts, scale?: number}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `frames` | array\|string\|integer | required | — Frames to show: a list, a count (spread evenly), or "auto": each scene's middle, both sides of every cut and the last frame. Each cell is labelled with its frame and time |
| `columns` | integer | optional default=4 | (min=1, max=64) — Sheet columns, 1..=64 (the sheet buffer is frame_w*columns by frame_h*rows) |
| `max_width` | integer | optional default=2400 | — Shrink the sheet to at most this many px wide so it comes back inline (0 = full size) |
| `ignore` | array\|string\|integer | optional | — Layers whose motion is intended (names or ids) |
| `platform` | enum["reels"|"tiktok"|"shorts"] | optional | — 9:16 app UI zones for the layout list |
| `out` | string | required | — PNG output path; the sheet is returned inline to MCP clients |
| `backend` | enum["auto"|"gpu"|"cpu"] | optional default="auto" | — auto: GPU (Metal) if available, else Flint; cpu: Flint, the exact CPU renderer |
| `scale` | number | optional | (min=0.1, max=1) — Fraction of the size for fast previews (0.5 = half) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Eight key poses

  ```json
  {"frames":[0,10,20,30,45,60,90,120],"columns":4,"out":"sheet.png"}
  ```

- A vertical teaser against Reels zones, with an intended wipe left out of the motion list

  ```json
  {"frames":[0,30],"out":"reels-sheet.png","platform":"reels","ignore":["Accent"]}
  ```

Tips:

- The fastest way to see an animation: pick frames at each key pose and inspect the returned image before exporting video. Stills hide timing: follow with inspectMotionActivity to find dead stretches.
- The result's layout list names collisions and text past the safe area in these frames, measured from geometry: thumbnails hide them. For a vertical teaser pass platform (reels, tiktok, shorts) so the list checks the app's interface zones; ignore: [layer names or ids] keeps intended motion (a typing wipe) out of the motion list.

Related: `critiqueLayout`, `inspectMotionActivity`, `inspectImage`, `renderVideo`

CLI: `kinetic tool renderContactSheet --params '<json>' --workspace ./out`

## grade

Colour work, measured not guessed. scopes: luma p1/p50/p99, clipping, saturation, white-balance cast, skin-tone deviation and a waveform/vectorscope/histogram image. match: move a layer's colour to another layer or image (Reinhard in Lab, baked to a 33^3 .cube, re-running replaces it). auto: bring clips to one exposure and white balance. look: teal-orange, bleach, film, mono, fade, warm, cool. grain: copy the grain of footage onto a layer. All land as ordinary effects. guide color.

- family: `color` · mutating: `true` · transport: CLI `kinetic tool grade` / SDK `batch([{tool: "grade"}])` / MCP `tools/call {name: "grade"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.grade(...)` in runScript · inside runScript: yes
- signature: `grade {action: scopes|match|auto|look|grain, amount?: number, frame?: number [frame], layer?: string|int, layers?: string|int[], match?: string, name?: teal-orange|bleach|film|mono|fade|warm|cool, range?: number[] [frames], size?: number [px], strength?: number = 1, target?: number = 0.42, to?: string, track?: string, white_balance?: bool = true}`

| param | type | required/default | notes |
|---|---|---|---|
| `action` | enum["scopes"|"match"|"auto"|"look"|"grain"] | required | — What to do |
| `layer` | string\|integer | optional | — The layer (name or id); scopes and look may take composition instead |
| `composition` | string\|integer | optional | — Composition (id, name or index) |
| `layers` | array | optional | — auto: the layers to balance |
| `track` | string | optional | — auto: an edit track (V1) whose clips to balance |
| `frame` | number | optional | (min=0) — scopes/measuring: one frame |
| `range` | array | optional | — [from, to) frames, sampled evenly |
| `to` | string | optional | — match: the layer, image asset or image file to look like |
| `strength` | number | optional default=1 | (min=0, max=1) — match/look: 0 none, 1 full |
| `name` | enum["teal-orange"|"bleach"|"film"|"mono"|"fade"|"warm"|"cool"] | optional | — look |
| `target` | number | optional default=0.42 | (min=0.2, max=0.7) — auto: median luma to reach |
| `white_balance` | boolean | optional default=true | — auto: false leaves the colour alone |
| `match` | string | optional | — grain: the footage layer whose grain to copy |
| `amount` | number | optional | (min=0, max=1) — grain: set the amount yourself instead of measuring |
| `size` | number | optional | (min=0.5, max=8) — grain: with amount, the grain size in px |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Read the middle frame: numbers plus waveform, vectorscope and histogram

  ```json
  {"action":"scopes","composition":"Main"}
  ```

- A layer over a stretch of frames

  ```json
  {"action":"scopes","layer":"Card","range":[0,30]}
  ```

- Move a layer's colour toward an image or another layer

  ```json
  {"action":"match","layer":"Card","to":"photo","strength":0.8}
  ```

- Bring layers to one exposure and white balance

  ```json
  {"action":"auto","layers":["Background","Card"]}
  ```

- A film look over everything, kept light

  ```json
  {"action":"look","composition":"Main","name":"film","strength":0.7}
  ```

- Footage grain, set by hand

  ```json
  {"action":"grain","layer":"Background","amount":0.03,"size":2}
  ```

Tips:

- Look first with scopes, then auto, then match, then one light look on the composition, and grain last: one film, not a collage.
- Running match, look or grain again replaces the previous result; auto owns the layer's first exposure_contrast_saturation and color_temp_tint.
- white_balance: false keeps a deliberately warm or coloured scene; a delta_e_to_target_mean over 3 means part of the match clipped: lower strength.
- grain needs a flat area in the footage (a wall, sky or shadow); skin numbers in scopes need footage index first. Layers above the composition's Look adjustment layer are left out of the look.

Related: `addEffect`, `footage`, `edit`, `inspectRenderGraph`

CLI: `kinetic tool grade --params '<json>' --workspace ./out`

## inspectAudio

Probe an audio asset/file: input streams, codec, sample rate, channels, duration.

- family: `audio` · mutating: `false` · transport: CLI `kinetic tool inspectAudio` / SDK `batch([{tool: "inspectAudio"}])` / MCP `tools/call {name: "inspectAudio"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.inspectAudio(...)` in runScript or query · inside runScript: yes
- signature: `inspectAudio {asset?: string, path?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `asset` | string | optional | — Project asset id |
| `path` | string | optional | — Workspace-relative file path |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Probe

  ```json
  {"asset":"music"}
  ```

CLI: `kinetic tool inspectAudio --params '<json>' --workspace ./out`

## addSound

Add a sound effect as an audio layer, generated in-engine: a preset by name (whoosh, swish, riser, impact, slam, click, pop, typing, ticks, chime, logo, cartoon gags like boing, honk, slide-whistle, crunch, gulp, splat ... 82; guide {topic:"sound"}), your own synth patch, or raw samples. Its hit (a whoosh's pass, a riser's peak, an impact's attack) lands on frame. Same name replaces.

- family: `audio` · mutating: `true` · transport: CLI `kinetic tool addSound` / SDK `batch([{tool: "addSound"}])` / MCP `tools/call {name: "addSound"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.addSound(...)` in runScript · inside runScript: yes
- signature: `addSound {align?: hit|start = "hit", auto?: bool, count?: int, duration_frames?: number, for?: string|int|array, frame?: number = 0 [frame], from?: number [frame], gain_db?: number = 0 [dB], name?: string, note?: string, out?: string, pan?: number = 0, patch?: object, pitch?: number, place?: bool = true, sample_rate?: int = 48000 [Hz], samples?: any[], seed?: int, sound?: string, to?: number [frame], tone?: number}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `sound` | string | optional | — Preset name |
| `patch` | object | optional | — Own synth patch {voices:[{osc,note|freq,env,filter,...}],fx} (guide sound) |
| `samples` | array | optional | — Raw audio: [s...] or [[left...],[right...]], -1..1 |
| `sample_rate` | integer | optional default=48000 | — Hz of samples |
| `frame` | number | optional default=0 | — Frame the hit lands on |
| `align` | enum["hit"|"start"] | optional default="hit" | — hit: sync point on frame; start |
| `duration_frames` | number | optional | — Length (presets) |
| `pitch` | number | optional | — Semitones |
| `tone` | number | optional | — -1 dark .. 1 bright |
| `count` | integer | optional | — Events (typing, ticks; honks, bites, gulps, scratch strokes) |
| `note` | string | optional | — Root note, e.g. D2 |
| `seed` | integer | optional | — Variation |
| `gain_db` | number | optional default=0 | — dB on top of the mix-ready level |
| `pan` | number | optional default=0 | — -1 left .. 1 right |
| `name` | string | optional | — Display name |
| `for` | string\|integer\|array | optional | — Layer(s) this sound scores: deleting the last of them deletes the sound |
| `place` | boolean | optional default=true | — false: only render the file (returns the preset patch to edit) |
| `auto` | boolean | optional | — Sounds for the motion already there (whooshes, pops, slams, typing, ticks), in from..to |
| `from` | number | optional | — auto: first frame |
| `to` | number | optional | — auto: end frame |
| `out` | string | optional | — WAV path |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- A whoosh peaking on the cut at frame 60

  ```json
  {"sound":"whoosh","frame":60}
  ```

- A 1.5 s riser ending on the reveal

  ```json
  {"sound":"riser","frame":90,"duration_frames":45}
  ```

- Typing under 12 characters

  ```json
  {"sound":"typing","frame":20,"count":12,"duration_frames":24,"align":"start"}
  ```

- A cartoon boing as the logo bounces

  ```json
  {"sound":"boing","frame":42}
  ```

- Two honks (count repeats honks, bites, gulps, scratch strokes)

  ```json
  {"sound":"honk","frame":60,"count":2}
  ```

- Your own sound: a detuned chord stab

  ```json
  {"frame":30,"name":"Stab","patch":{"voices":[{"osc":"supersaw","note":["D3","A3","D4"],"length":0.3,"env":"hit","filter":{"type":"lowpass","cutoff":{"from":5000,"to":400,"time":0.8}}}],"fx":{"reverb":0.3}}}
  ```

- Get a preset's patch to edit

  ```json
  {"sound":"chime","place":false}
  ```

- A pop that goes when its layer is deleted

  ```json
  {"sound":"pop","frame":40,"for":"Accent"}
  ```

- Score the motion that is already there

  ```json
  {"auto":true}
  ```

Tips:

- frame is where the sound's hit lands: a riser's peak, a whoosh's pass-by, an impact's attack. align:"start" starts it there instead.
- Sounds render mix-ready; gain_db only nudges (-6 tucks under, +3 features). One hero sound per moment.
- No preset fits? Write a patch (voices + fx; guide sound), or start from a preset: place:false returns its patch.
- Cartoon gags are presets: boing, slide-whistle (-down), cork-pop, honk, car-horn, crunch, gulp, record-scratch, splat, brake-hiss, bonk, squeak, sad-trombone. count repeats the honk, beep, bite, gulp or scratch stroke; names accept _ or spaces.
- auto:true scores motion built without the kit (fast moves, pop-ins, slams, spins, typewriter text, counters) and skips layers a sound already scores; place:false lists what it would add.

Related: `addMusic`, `mixAudio`, `setAudioGain`, `renderVideo`

CLI: `kinetic tool addSound --params '<json>' --workspace ./out`

## setLayerField

Set a base field (enabled, name, blend_mode, opacity, in/out, is_3d, motion_blur_enabled, position, scale, rotation, anchor; intended: true exempts it from critiqueMotion; behind: the cutout a text sits behind, which critiqueLayout keeps it under), motion: follow (overlap, follow-through), squash (stretch with speed), speed (speed ramp), time_remap, posterize (on twos), echo (trails), 3D: light, material, extrude, model, depth (an image layer + a gray depth map = a 3D photo: {asset, scale_px?, near?, far?}), or puppet (pins that bend the artwork), ik (a limb reaches a target).

- family: `scene` · mutating: `true` · transport: CLI `kinetic tool setLayerField` / SDK `batch([{tool: "setLayerField"}])` / MCP `tools/call {name: "setLayerField"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setLayerField(...)` in runScript · inside runScript: yes
- signature: `setLayerField {field: enabled|name|blend_mode|opacity|in_frame|out_frame|is_3d|motion_blur_enabled|…, layer: string|int, value: any}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `field` | string | required | — Which base field to set; see value for each field's format |
| `value` | any | required | — position: [x, y] or [x, y, z] px from comp centre, y up. scale: number (uniform) or [sx, sy(, sz)], 1 = 100%. rotation: degrees, a number (2D spin) or [x, y, z]. anchor: [x, y] px in layer space (the pivot). opacity: 0..1. in_frame/out_frame: frames (visible when in_frame <= f < out_frame). blend_mode: normal, multiply, screen, overlay, add, darken, lighten, subtract, difference, color_dodge, color_burn, hard_light, soft_light, exclusion, hue, saturation, color, luminosity. is_3d, motion_blur_enabled, enabled, solo, locked, shy: bool. intended: bool (true: critiqueMotion leaves this layer's motion out; false clears it). behind: the cutout layer (id or name) a text sits behind (kit.textBehind sets it; critiqueLayout reports the text if it paints over that cutout); null clears. depth (an image layer, a 3D photo): {asset: gray map, white near, scale_px: 120, near: 1, far: 0} or the asset name; null clears. name: string. follow: {layer (leader; omit = own motion), delay (frames, 3), spring, amount, axes (separate dimensions: ["x", {axis:"rotation", delay, spring, amount}])}. squash: 0.5 (1 cartoon). speed: [[frame, speed], ...]. time_remap: [[frame, source_frame(, ease|"hold")], ...] (frame in this composition; source_frame in the nested composition's own frames). posterize: fps (12). echo: {count, interval, decay}. puppet: {pins: [{name, at: [x, y] layer px from its centre, y up | "top"/"left"/... | node: svg id, kind: position|bend|advanced}], starch: [{at, radius, amount}], detail: coarse|normal|fine}, then animateProperty puppet.<pin>.x / .y (.rotation, .scale for bend pins). null removes a motion field |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Move (px from centre, y up)

  ```json
  {"layer":"Accent","field":"position","value":[-420,260]}
  ```

- Uniform scale 110%

  ```json
  {"layer":"Accent","field":"scale","value":1.1}
  ```

- Rotate 12 degrees

  ```json
  {"layer":"Accent","field":"rotation","value":12}
  ```

- Screen blend

  ```json
  {"layer":"Accent","field":"blend_mode","value":"screen"}
  ```

- Make a layer 3D (camera moves affect it)

  ```json
  {"layer":"Headline","field":"is_3d","value":true}
  ```

- Visible from frame 30

  ```json
  {"layer":"Accent","field":"in_frame","value":30}
  ```

- Overlap: follow the card 3 frames late, springy

  ```json
  {"layer":"Headline","field":"follow","value":{"layer":"Card","delay":3,"spring":"snappy"}}
  ```

- Inertia: the layer's own stops overshoot and settle

  ```json
  {"layer":"Accent","field":"follow","value":{"spring":"bouncy"}}
  ```

- Puppet: pin an arm at the shoulder (holds its angle) and the hand

  ```json
  {"layer":"Arm","field":"puppet","value":{"pins":[{"name":"shoulder","at":"left","kind":"advanced"},{"name":"hand","at":"right"}],"starch":[{"at":"right","radius":30}]}}
  ```

- Puppet pins by SVG node id

  ```json
  {"layer":"Character/arm","field":"puppet","value":{"pins":[{"name":"shoulder","node":"upper","kind":"advanced"},{"name":"hand","node":"fist"}]}}
  ```

- IK: the forearm reaches for a null (the arm bends to get there)

  ```json
  {"layer":"Ava/arm_r_lower","field":"ik","value":{"target":"Goal","bend":"auto"}}
  ```

- Separate dimensions: x lags springy, y exact, rotation drags most

  ```json
  {"layer":"Headline","field":"follow","value":{"layer":"Card","delay":3,"axes":[{"axis":"x","spring":"bouncy"},{"axis":"y","delay":0},{"axis":"rotation","delay":8,"amount":0.6}]}}
  ```

- Squash and stretch with speed

  ```json
  {"layer":"Accent","field":"squash","value":0.6}
  ```

- Speed ramp: slow motion around frame 60

  ```json
  {"layer":"Card","field":"speed","value":[[40,1],[55,0.25],[70,0.25],[85,1]]}
  ```

- Time remap: freeze, then rewind

  ```json
  {"layer":"Card","field":"time_remap","value":[[30,30,"hold"],[50,30],[70,10]]}
  ```

- Animate on twos

  ```json
  {"layer":"Accent","field":"posterize","value":12}
  ```

- Motion trail

  ```json
  {"layer":"Accent","field":"echo","value":{"count":6,"interval":2,"decay":0.6}}
  ```

- Extrude the logo into a 3D solid, 40 px deep

  ```json
  {"layer":"Logo","field":"extrude","value":40}
  ```

- Extruded type with a rounded bevel and darker walls

  ```json
  {"layer":"Headline","field":"extrude","value":{"depth":60,"bevel":4,"bevel_style":"convex","side_color":"#5A6170"}}
  ```

- Chrome material: tight highlight tinted by the colour

  ```json
  {"layer":"Headline","field":"material","value":{"specular":0.8,"shininess":0.75,"metal":0.7}}
  ```

Tips:

- extrude (a depth, or {depth, bevel, bevel_style, side_color}) turns text, shapes and SVG into lit 3D solids; material sets how a 3D layer takes light (specular, shininess, metal, shadows); light edits a light (only the keys you pass).
- follow, squash, speed, time_remap, posterize and echo are animator fields: they change how existing motion plays, not the base value.

Related: `setProperty`, `animateProperty`, `inspectLayer`

CLI: `kinetic tool setLayerField --params '<json>' --workspace ./out`

## renderVideo

Render a range and encode MP4 (FFmpeg); probes the result (GPU by default). Includes mixed sound. Long exports include an advisory estimate and cached chunk ranges and always render; preflight:true estimates without rendering. Explicit start/end renders that range.

- family: `render` · mutating: `false` · transport: CLI `kinetic tool renderVideo` / SDK `batch([{tool: "renderVideo"}])` / MCP `tools/call {name: "renderVideo"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `renderVideo {out: string, audio?: bool, backend?: auto|gpu|cpu = "auto", cache?: bool = true, check?: bool, chroma?: 420|444, dither?: sierra|floyd_steinberg|bayer|none, end?: int [frame], fps?: int [frames], max_mb?: number [frames], mix_rate?: int = 48000, palette?: scene|global|frame, preflight?: bool = false, quality?: draft|standard|web|high|max|master = "high", scale?: number, start?: int = 0 [frame], supersample?: 1|2}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `start` | integer | optional default=0 | — First frame (inclusive, default 0) |
| `end` | integer | optional | — Stop frame, exclusive (default: the composition's duration) |
| `out` | string | required | — .mp4 or .mov; .gif, .webp or .apng make a looping animated image (640 px GIF, 1080 px otherwise; scale overrides) |
| `fps` | integer | optional | — Animated images: frame rate (GIF 15, others up to 30); only those frames render |
| `palette` | enum["scene"|"global"|"frame"] | optional | — GIF colours: per scene (cuts and markers; default), one for all (smallest), or per frame (best colour, ~30% bigger) |
| `dither` | enum["sierra"|"floyd_steinberg"|"bayer"|"none"] | optional | — GIF dithering (default sierra) |
| `backend` | enum["auto"|"gpu"|"cpu"] | optional default="auto" | — auto: GPU (Metal) if available, else Flint; cpu: Flint, the exact CPU renderer |
| `audio` | boolean | optional | — Mix the comp's audio layers into the MP4 (default: true when it has any) |
| `mix_rate` | integer | optional default=48000 | — Audio mix sample rate when audio=true |
| `preflight` | boolean | optional default=false | — Sample up to 9 frames (costly, median, cheap) through this backend and pixel conversion plus a short software encode; return an uncertain cost-weighted estimate and chunk ranges without exporting. Sound mixing, joining and cold setup are extra |
| `cache` | boolean | optional default=true | — Re-export only what changed: the video is kept in 2 s pieces and a piece whose frames did not change since an earlier export (same quality and size) is reused. Pictures are identical to a full export; sound is mixed fresh; grain exports render whole. false renders every frame |
| `scale` | number | optional | (min=0.1, max=1) — Render at this fraction of the size (default 1; 0.5 for draft) |
| `check` | boolean | optional | — Then open the exported file: a labelled sheet of its frames, with size, bitrate, audio and loudness (checkExport). Default on in agent sessions |
| `max_mb` | number | optional | — Aim the whole file (sound included) under this many MB, e.g. 25 for chat uploads: x264 from lossless frames, 2-pass if needed; the result's fit reports size and match. Default from Miran Settings; 0 = no limit |
| `quality` | enum["draft"|"standard"|"web"|"high"|"max"|"master"] | optional default="high" | — draft: fast preview at half size (H.264, motion blur capped at 4 taps, measured frames too). standard: H.264 High, smaller. web: x264 tuned for flat animation, a third of high's size at 99.5% of its picture (software, slower). high (default): H.264 High at high constant quality, plays everywhere; the render's gradient dither survives this encode, so dark gradients stay band-free. max: 10-bit HEVC Main10, the cleanest gradients and glows (Apple, YouTube, modern browsers; not every chat app or Windows PC); H.264 at crf 12 without VideoToolbox HEVC. master: ProRes 422 HQ, 10-bit 4:2:2 (needs a .mov out path; ProRes 4444 with alpha when the composition is transparent). All BT.709; hardware-encoded on Apple Silicon, software fallback elsewhere. |
| `chroma` | enum["420"|"444"] | optional | — Master only: "444" writes ProRes 4444 without alpha (full-resolution colour for compositing and grading) instead of ProRes 422 HQ. Default: the preset's own (4:2:0 for delivery, 4:2:2 for master). A transparent composition already keeps ProRes 4444 with alpha. |
| `supersample` | enum[1|2] | optional | — 1 or 2: render at 2x and resolve before encoding. Default 2 for master up to 1440p, else 1 (guide render) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Delivery MP4 (whole composition)

  ```json
  {"out":"renders/teaser.mp4"}
  ```

- Fast half-size preview of a section

  ```json
  {"start":0,"end":60,"out":"renders/preview.mp4","quality":"draft"}
  ```

- 10-bit HEVC, no banding

  ```json
  {"end":60,"out":"renders/teaser-hevc.mp4","quality":"max"}
  ```

- Under 25 MB for a chat upload

  ```json
  {"out":"renders/teaser-small.mp4","max_mb":25}
  ```

- A GIF under 5 MB

  ```json
  {"out":"renders/teaser.gif","max_mb":5}
  ```

Tips:

- Over MCP, long renders return {job, status:"running"} after ~40 s; call jobStatus {job} until done.
- Width and height must be even. quality=high is the delivery default; max for gradients/glows (10-bit HEVC); master needs a .mov path.
- max_mb fits the whole file under a size (chats and mail take ~25 MB); the result's fit reports passes and the measured match. quality web is x264 at about a third of high's size.

Related: `jobStatus`, `renderContactSheet`, `exportVersions`, `validateProject`

CLI: `kinetic tool renderVideo --params '<json>' --workspace ./out`

## deliver

Every file a release needs, in one job: youtube, shorts, tiktok, reels, x, linkedin (each its own shape), master (ProRes), alpha (ProRes 4444), podcast (M4A). Sound at -14 LUFS (podcast -16), true peak under -1 dBTP, measured on the files; SRT/VTT captions, YouTube chapters, a 1280x720 thumbnail, deliver.json; generated media is labelled in the file (C2PA). The project is not changed. guide render.

- family: `render` · mutating: `false` · transport: CLI `kinetic tool deliver` / SDK `batch([{tool: "deliver"}])` / MCP `tools/call {name: "deliver"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none (it renders whole films as a job: call it directly)
- signature: `deliver {to: youtube|shorts|tiktok|reels|x|linkedin|master|alpha|…[], captions?: burn|srt|vtt|both|none = "burn", chapters?: object[], credentials?: auto|on|off = "auto", follow?: auto|face|person|saliency|center = "auto", loudness?: number, max_mb?: number, out?: string, quality?: standard|web|high|max, thumbnail?: boolean|object}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | — Default the first |
| `to` | array | required | — Targets |
| `captions` | enum["burn"|"srt"|"vtt"|"both"|"none"] | optional default="burn" | — burn keeps the caption layers; srt/vtt/both write files and hide them |
| `chapters` | array | optional | — YouTube chapters (first at 0) |
| `thumbnail` | boolean\|object | optional | — true: the best frame; or {frame | seconds | anchor, title} |
| `loudness` | number | optional | (min=-40, max=-5) — LUFS instead of -14 / -16 |
| `quality` | enum["standard"|"web"|"high"|"max"] | optional | — Platform video quality (high) |
| `max_mb` | number | optional | (min=0) — Aim each video under this |
| `follow` | enum["auto"|"face"|"person"|"saliency"|"center"] | optional default="auto" | — Cut footage going tall: the crop follows |
| `credentials` | enum["auto"|"on"|"off"] | optional default="auto" | — C2PA content credentials in MP4/MOV: auto signs when the cut has generated media |
| `out` | string | optional | — Folder (default <film>/deliver/<name>) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- YouTube file with chapters

  ```json
  {"to":["youtube"],"captions":"none","chapters":[{"at":0,"title":"Open"},{"at":2,"title":"Middle"},{"at":4,"title":"Close"}]}
  ```

- Shorts and TikTok with an SRT and a titled thumbnail

  ```json
  {"to":["shorts","tiktok"],"captions":"srt","thumbnail":{"seconds":2.5,"title":"Meet Miran"}}
  ```

- Podcast audio at -16 LUFS with WebVTT

  ```json
  {"to":["podcast"],"captions":"vtt"}
  ```

Tips:

- Targets: youtube 1920x1080 (the comp's own 16:9 size from 720p to 2160p), shorts, tiktok and reels 1080x1920 (one render, copied), x 1920x1080 (a tall or square film keeps its shape), linkedin 1080x1350, master ProRes 422 HQ and alpha ProRes 4444 at the comp's size, podcast M4A AAC 192k. A comp smaller than the target is not upscaled (warned).
- Chapters: `at` in seconds or "m:ss"; YouTube wants the first at 0:00, three or more, each 10 s or longer (warned, not changed). Thumbnail: true takes the best frame (footage best when the clips are indexed, else the middle); {frame | seconds | anchor, title} picks it and draws a title over the lower third.
- The last step: critiqueEdit first, then deliver once per release. Each target gets its own shape (vertical versions of a landscape film are refitted by exportVersions, cut footage goes tall by edit reframe following the speaker, burned captions set again clear of each app's interface, one version per app), its loudness (-14 LUFS for video, -16 for podcast, true peak under -1 dBTP, measured again on the encoded file), and a deliver.json listing every file.
- captions: burn (the default) keeps the caption layers in the picture; srt or vtt renders a clean picture and writes the sidecar from the caption layers, the voiceover words or the footage transcript. Chapters come out in YouTube's description format.
- credentials: auto (the default) signs each MP4 and MOV with C2PA content credentials when the cut holds generated media (generateVideo, generate, generateImage); on signs any cut; off signs none. The manifest marks the file trainedAlgorithmicMedia (every picture generated) or compositeWithTrainedAlgorithmicMedia (generated media mixed with footage, stills or graphics) and lists each generated segment with its time range, model and provider in com.miranfann.segments; prompts are not in it. The signer is a key made once on this Mac (kept like the API keys), on no trust list, so a checker shows an unknown signer with a valid signature: it proves the file is unchanged since Miran wrote it and what Miran labelled generated. Each file reports credentials in the result and deliver.json; a file that cannot be signed is delivered without, with a warning.
- The project is never changed. Files land in <film>/deliver/<name>/; check the vertical versions (renderContactSheet, critiqueLayout platform) before publishing.

Related: `critiqueEdit`, `exportVersions`, `renderVideo`, `edit`, `footage`, `checkExport`, `variants`

CLI: `kinetic tool deliver --params '<json>' --workspace ./out`

## checkExport

Open an exported video (or GIF) and check it: frames decoded from the file as a labelled sheet (returned as an image), duration, size, bitrate, fps, audio and loudness, with problems named. renderVideo runs this on every export.

- family: `render` · mutating: `false` · transport: CLI `kinetic tool checkExport` / SDK `batch([{tool: "checkExport"}])` / MCP `tools/call {name: "checkExport"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.checkExport(...)` in runScript or query · inside runScript: yes
- signature: `checkExport {path: string, max_mb?: number}`

| param | type | required/default | notes |
|---|---|---|---|
| `path` | string | required | — The file, workspace-relative (renderVideo's out) |
| `max_mb` | number | optional | — The size it had to fit, in MB |
CLI: `kinetic tool checkExport --params '<json>' --workspace ./out`

## compareImages

Pixel diff stats between two PNGs.

- family: `render` · mutating: `false` · transport: CLI `kinetic tool compareImages` / SDK `batch([{tool: "compareImages"}])` / MCP `tools/call {name: "compareImages"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.compareImages(...)` in runScript or query · inside runScript: yes
- signature: `compareImages {a: string, b: string, tolerance?: int = 8}`

| param | type | required/default | notes |
|---|---|---|---|
| `a` | string | required | — First PNG path |
| `b` | string | required | — Second PNG path (same size as a) |
| `tolerance` | integer | optional default=8 | — Per-channel difference 0..255 counted as unchanged (default 8) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Diff two renders

  ```json
  {"a":"photo.png","b":"photo.png"}
  ```

CLI: `kinetic tool compareImages --params '<json>' --workspace ./out`

## inspectRenderGraph

Plan a frame: GPU nodes + CPU fallbacks with reasons.

- family: `render` · mutating: `false` · transport: CLI `kinetic tool inspectRenderGraph` / SDK `batch([{tool: "inspectRenderGraph"}])` / MCP `tools/call {name: "inspectRenderGraph"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.inspectRenderGraph(...)` in runScript or query · inside runScript: yes
- signature: `inspectRenderGraph {frame?: number = 0 [frame]}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `frame` | number | optional default=0 | — Frame to plan (default 0) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- How will frame 30 render?

  ```json
  {"frame":30}
  ```

CLI: `kinetic tool inspectRenderGraph --params '<json>' --workspace ./out`

## listRenderCapabilities

CPU (Flint) + GPU (Metal) caps side by side (engines), effects, engine version, adapter, encoder (honest).

- family: `render` · mutating: `false` · transport: CLI `kinetic tool listRenderCapabilities` / SDK `batch([{tool: "listRenderCapabilities"}])` / MCP `tools/call {name: "listRenderCapabilities"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.listRenderCapabilities(...)` in runScript or query · inside runScript: yes
- signature: `listRenderCapabilities {}`

No parameters.

Example: `{}`

CLI: `kinetic tool listRenderCapabilities --params '<json>' --workspace ./out`

## jobStatus

Wait for a background render job (returned by a long render call) and report progress, or its final result once done — including the rendered image.

- family: `jobs` · mutating: `false` · transport: CLI `kinetic tool jobStatus` / SDK `batch([{tool: "jobStatus"}])` / MCP `tools/call {name: "jobStatus"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `jobStatus {job: string, wait_seconds?: number = 40}`

| param | type | required/default | notes |
|---|---|---|---|
| `job` | string | required | — Job id from the render call, e.g. "job-1" |
| `wait_seconds` | number | optional default=40 | (min=0, max=50) — How long to block waiting for completion before reporting progress (stays under host timeouts) |
Related: `renderVideo`, `renderRange`

CLI: `kinetic tool jobStatus --params '<json>' --workspace ./out`

## feedback

When the film is done: the issues you ran into in Miran, a short plain paragraph for the Miran team ("no issues" if none).

- family: `project` · mutating: `false` · transport: CLI `kinetic tool feedback` / SDK `batch([{tool: "feedback"}])` / MCP `tools/call {name: "feedback"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: yes
- signature: `feedback {issues: string, film?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `issues` | string | required | — A short plain paragraph |
| `film` | string | optional | — What you made |
CLI: `kinetic tool feedback --params '<json>' --workspace ./out`

## sounds

Search 352 CC0 sound effects; get one; place: true fetches, caches and imports it.

- family: `audio` · mutating: `true` · transport: CLI `kinetic tool sounds` / SDK `batch([{tool: "sounds"}])` / MCP `tools/call {name: "sounds"}`
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: search, get, and place for sounds already in the cache
- signature: `sounds {action?: search|get, category?: string, id?: string, limit?: int, max_seconds?: number, min_seconds?: number, place?: bool, query?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `action` | enum["search"|"get"] | optional | — get if id, else search |
| `query` | string | optional | — words, e.g. "whoosh" |
| `category` | string | optional | — ui, whoosh, riser, impact, ... |
| `min_seconds` | number | optional | (min=0, max=600) — shortest |
| `max_seconds` | number | optional | (min=0, max=600) — longest |
| `limit` | integer | optional | (min=1, max=50) — results (12) |
| `id` | string | optional | — get: an id from search |
| `place` | boolean | optional | — get: fetch and import it |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Sources, categories, licences

  ```json
  {}
  ```

- Short whooshes

  ```json
  {"query":"whoosh","max_seconds":1}
  ```

- Risers for a build

  ```json
  {"category":"riser","limit":8}
  ```

- One sound: licence, duration, pinned URL

  ```json
  {"id":"kenney/click1"}
  ```

Tips:

- placement is explicit: get without place returns metadata only, place: true downloads, caches, imports and credits the file. Place with a direct call: a script reads the cache but does not download.
- These are recordings; addSound and kit.sfx are synthesis. Use a recording for texture and a generated sound for a precise hit on the frame.
- Check duration_s before you place it: a 0.45 s whoosh is a cut, a 3 s riser is a build. max_seconds filters faster than listening.
- The cache is shared across films and works offline; a cached file that is not the indexed size is fetched again.

CLI: `kinetic tool sounds --params '<json>' --workspace ./out`

