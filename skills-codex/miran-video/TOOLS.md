# Making a video end to end — tool reference (generated)

Source: the runtime registry via `kinetic schemas` — do not hand-edit; run `bun scripts/gen-docs.ts`.
35 of 178 tools — the ones this skill teaches. The full registry lives in docs/TOOLS.md.

## runScript

Run JavaScript that calls any tool as miran.<toolName>(params) with loops, math and each call's result (ids, sizes) - build or animate a whole scene or beat in ONE call instead of dozens. Atomic: an error rolls everything back; success is one undo step. Rendered PNGs come back as images. See guide {topic:"scripting"}.

- family: `script` · mutating: `true` · transport: CLI `kinetic tool runScript` / SDK `batch([{tool: "runScript"}])` / MCP `tools/call {name: "runScript"}`
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: none (scripts do not nest)
- signature: `runScript {source: string, dry_run?: bool = false, images?: bool = true, label?: string, strict?: bool, timeout_seconds?: number = 45, verbose?: bool = false}`

| param | type | required/default | notes |
|---|---|---|---|
| `source` | string | required | — JavaScript function body. Call tools as miran.createLayer({...}) (returns the tool's result, throws on error with .code and .hint); kit.* has ready-made motion-design pieces (kit.title, kit.words, kit.counter, kit.line, kit.ring, kit.card, kit.bars, kit.cursor, kit.wipe, kit.finish, ...; guide {topic:"kit"}); log(...) collects debug lines; range(n), lerp(a,b,t), clamp(x,lo,hi) are available; `return` a value to get it back. No file/network access. |
| `label` | string | optional | — Name for the undo step, e.g. "beat 2: stats reveal" (default: first line of the script) |
| `dry_run` | boolean | optional default=false | — Run everything, report the result, then roll the project back (test a script without keeping it) |
| `timeout_seconds` | number | optional default=45 | (min=1, max=600) — Wall-clock budget (max 50 over MCP so the call returns before the host times out) |
| `strict` | boolean | optional | — A misspelled param fails the call (and rolls back) instead of being ignored (default true) |
| `images` | boolean | optional default=true | — false: PNGs the script renders stay files (listed under files), not inline images |
| `verbose` | boolean | optional default=false | — changes lists every layer touched (default: counts and the first 8 of each list, to keep results short) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Staggered bar chart in one call

  ```json
  {"label":"stats bars","source":"const values = [42, 67, 88, 54, 96];\nconst ids = values.map((v, i) => miran.createLayer({name: `Bar ${i}`, kind: 'shape', width: 64, height: v * 4, fill: '#3D5AFE', position: [-260 + i * 130, v * 2 - 220]}).id);\nids.forEach((id, i) => miran.animateProperty({layer: id, property: 'scale.y', from: 0, to: 1, start_frame: 10 + i * 3, duration_frames: 24, ease: 'spring:snappy'}));\nreturn {bars: ids.length};"}
  ```

- Lay out a title lockup and look at it

  ```json
  {"source":"const title = miran.createLayer({name: 'Title', kind: 'text', text: 'Built for speed', font_size: 120, position: [0, 60]});\nmiran.setTextStyle({layer: title.id, font_weight: 700, tracking: -0.03, color: '#F5F5F7'});\nconst sub = miran.createLayer({name: 'Sub', kind: 'text', text: 'Render at the speed of thought', font_size: 40, position: [0, -40]});\nmiran.setTextStyle({layer: sub.id, color: '#8A8F98'});\nmiran.addTextAnimator({layer: title.id, animator: {from: {y: -40, opacity: 0}, start_frame: 6, stagger_frames: 2, spring: 'snappy'}});\nmiran.animateProperty({layer: sub.id, property: 'opacity', from: 0, to: 1, start_frame: 24, duration_frames: 14, ease: 'standard'});\nreturn miran.renderContactSheet({frames: [0, 12, 24, 40], columns: 4, out: 'lockup.png'});"}
  ```

- Try something without keeping it

  ```json
  {"dry_run":true,"source":"for (const i of range(12)) miran.createLayer({name: `Dot ${i}`, kind: 'shape', shape: 'circle', width: 24, fill: '#FF4D00', position: [Math.cos(i / 12 * Math.PI * 2) * 300, Math.sin(i / 12 * Math.PI * 2) * 300]});\nreturn miran.listLayers().layers.length;"}
  ```

Tips:

- Work one beat per script: build or animate it, render a contact sheet at the end (it comes back as an image), look, then write the next script.
- Use each call's result: createLayer returns {id}; pass that id to later calls instead of guessing names.
- Exports (renderVideo) are not allowed inside a script: call renderVideo afterwards; it runs as a background job.

CLI: `kinetic tool runScript --params '<json>' --workspace ./out`

## query

Read-only runScript: look at the scene and compute over it in one call (list, inspect, measure, sample properties, render a frame or a contact sheet; images come back inline). Only tools that do not change the project; nothing is committed. Use runScript for changes.

- family: `script` · mutating: `false` · transport: CLI `kinetic tool query` / SDK `batch([{tool: "query"}])` / MCP `tools/call {name: "query"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none (scripts do not nest)
- signature: `query {source: string, images?: bool = true, strict?: bool, timeout_seconds?: number = 45}`

| param | type | required/default | notes |
|---|---|---|---|
| `source` | string | required | — JavaScript function body, as in runScript: miran.<tool>(params) for read-only tools, kit helpers that only look, log(...), `return` a value |
| `timeout_seconds` | number | optional default=45 | (min=1, max=600) — Wall-clock budget (max 50 over MCP) |
| `strict` | boolean | optional | — Misspelled params fail instead of being ignored (default true) |
| `images` | boolean | optional default=true | — false: rendered PNGs stay files (listed under files) |
CLI: `kinetic tool query --params '<json>' --workspace ./out`

## openViewer

Start the live Miran Viewer and return its link: a timeline of every layer, keyframe, effect, mask, audio and marker, an inspector, your edit history and a preview, live as you edit. The user can edit there too (drag keys, scrub values, rename, reorder, undo); their changes come back in your next result as viewer_edits. Show the link to the user (open it in a browser pane if the host has one, else pass browser:true). Safe to repeat.

- family: `project` · mutating: `false` · transport: CLI `kinetic tool openViewer` / SDK `batch([{tool: "openViewer"}])` / MCP `tools/call {name: "openViewer"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `openViewer {browser?: bool = false, frame?: number [frame], port?: int}`

| param | type | required/default | notes |
|---|---|---|---|
| `browser` | boolean | optional default=false | — Also open the link in the computer's default web browser |
| `frame` | number | optional | (min=0) — Open at this frame (link end #frame=N seeks) |
| `composition` | string\|integer | optional | — Open on this composition (id or name) |
| `port` | integer | optional | (min=1024, max=65535) — Preferred port; the next free one is used when it is taken (default 47800) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Start the live viewer and get its link

  ```json
  {}
  ```

CLI: `kinetic tool openViewer --params '<json>' --workspace ./out`

## viewerApi

Private data feed of the Miran Viewer panel in chat apps. Not for direct use: call openViewer.

- family: `project` · mutating: `false` · transport: CLI `kinetic tool viewerApi` / SDK `batch([{tool: "viewerApi"}])` / MCP `tools/call {name: "viewerApi"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: yes
- signature: `viewerApi {op: state|values|boxes|curve|wave|audio|thumb|frame|…, asset?: string, calls?: object[], comp?: string, frame?: number [frame], k?: string, layer?: string|int, path?: string, quality?: draft|full, rate?: int, since?: int, text?: string, to?: number [frame], urgent?: bool, v?: int, width?: int}`

| param | type | required/default | notes |
|---|---|---|---|
| `op` | enum["state"|"values"|"boxes"|"curve"|"wave"|"audio"|"thumb"|"frame"|"edit"|"note"] | required | — Panel request |
| `since` | integer | optional | (min=0) — state: version held |
| `comp` | string | optional | — Composition id |
| `frame` | number | optional | — Frame |
| `asset` | string | optional | — Asset id |
| `rate` | integer | optional | (min=8000, max=48000) — audio: sample rate of the mix |
| `width` | integer | optional | (min=64, max=3840) — Pixels |
| `quality` | enum["draft"|"full"] | optional | — Preview quality |
| `v` | integer | optional | (min=0) — Project version shown |
| `k` | string | optional | — Frame content key |
| `urgent` | boolean | optional | — false: prefetch |
| `calls` | array | optional | — edit: the person's tool calls |
| `text` | string | optional | — note text |
| `layer` | string\|integer | optional | — note: selected layer; curve: its layer |
| `path` | string | optional | — curve: property |
| `to` | number | optional | — curve: last frame (from = frame) |
CLI: `kinetic tool viewerApi --params '<json>' --workspace ./out`

## viewerInbox

Notes the user typed for you in the Miran Viewer, with the layer and frame they were on. New notes also arrive with your next result as viewer_notes.

- family: `project` · mutating: `false` · transport: CLI `kinetic tool viewerInbox` / SDK `batch([{tool: "viewerInbox"}])` / MCP `tools/call {name: "viewerInbox"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.viewerInbox(...)` in runScript or query · inside runScript: yes
- signature: `viewerInbox {}`

No parameters.

Example: `{}`

CLI: `kinetic tool viewerInbox --params '<json>' --workspace ./out`

## openSettings

Open Miran Settings for the user (API keys for cloud voices, kept from you; cloud permission; defaults; models; caches). Returns the link and which keys are set.

- family: `project` · mutating: `false` · transport: CLI `kinetic tool openSettings` / SDK `batch([{tool: "openSettings"}])` / MCP `tools/call {name: "openSettings"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `openSettings {browser?: bool = false}`

| param | type | required/default | notes |
|---|---|---|---|
| `browser` | boolean | optional default=false | — Also open the link in the computer's default web browser |

Example: `{}`

CLI: `kinetic tool openSettings --params '<json>' --workspace ./out`

## renderContactSheet

Grid PNG of frames (QA artifact; GPU by default).

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
| `backend` | enum["auto"|"gpu"|"cpu"] | optional default="auto" | — auto: GPU if available; cpu: slow exact reference |
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

## critiqueMotion

How things move, like an animation director: straight diagonal travel (use arcs), constant-speed moves, entrances that accelerate into a dead stop, lockstep timing, one tempo for everything, nothing overshooting or following through, jumps, an empty first frame. Score, findings and a fix for each.

- family: `design` · mutating: `false` · transport: CLI `kinetic tool critiqueMotion` / SDK `batch([{tool: "critiqueMotion"}])` / MCP `tools/call {name: "critiqueMotion"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.critiqueMotion(...)` in runScript or query · inside runScript: yes
- signature: `critiqueMotion {ignore?: array|string|int}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `ignore` | array\|string\|integer | optional | — Layers whose motion is intended: names or ids (reveals are skipped already) |

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

- Findings come from the moves themselves (channels, paths, follows), so it runs in milliseconds; each gives the layer, frames and a concrete fix.
- Scenery (backgrounds, rigs, adjustment layers) may drift at constant speed; subjects should not.
- Reveals are not judged: the source of a track matte, a layer that never shows, a plate painted in the colour of what is behind it (a typing wipe's cover), and mask-path or trim-path animation. not_judged in the result names them. For anything else that is meant, pass ignore: [layer names or ids] (a name that is no layer is an error), or mark the layer once with setLayerField {field: "intended", value: true}. Its exit is not read time: text leaving with its shot is not flagged, and letters used as texture (ghost type, a wall of glyphs, faint ink) are not text.

Related: `animateProperty`, `setLayerField`, `critiqueLayout`, `inspectMotionActivity`

CLI: `kinetic tool critiqueMotion --params '<json>' --workspace ./out`

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
| `backend` | enum["auto"|"gpu"|"cpu"] | optional default="auto" | — auto (default) renders on the GPU when an adapter exists and falls back to the CPU reference otherwise |

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

## addMusic

Compose a backing track in-engine and add it as an audio layer: mood sets tempo, harmony, instruments and drums; the beat drops in at drop, accents land on hits, and it resolves and rings out from ending. Returns beat_frames, bar_frames, sections (intro, build, stop, drop, ending; frames) and an edit note. Same name replaces.

- family: `audio` · mutating: `true` · transport: CLI `kinetic tool addMusic` / SDK `batch([{tool: "addMusic"}])` / MCP `tools/call {name: "addMusic"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.addMusic(...)` in runScript · inside runScript: yes
- signature: `addMusic {bpm?: number, drop?: number [frame], drums?: bool, duck_db?: number = -9, end?: number, ending?: number [frame], energy?: number, gain_db?: number, hits?: number[] [frames], key?: string, mood?: tech|corporate|uplifting|epic|hype|chill|ambient|dark|… = "tech", name?: string = "Music", seed?: int, start?: number = 0 [frame], stop?: bool}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `mood` | enum["tech"|"corporate"|"uplifting"|"epic"|"hype"|"chill"|"ambient"|"dark"|"playful"|"minimal"|"luxury"] | optional default="tech" | — Style (guide sound) |
| `bpm` | number | optional | — Tempo (mood default) |
| `key` | string | optional | — e.g. D minor |
| `energy` | number | optional | — 0..1 |
| `start` | number | optional default=0 | — Start frame |
| `end` | number | optional | — Default: comp end |
| `hits` | array | optional | — Accent frames |
| `drop` | number | optional | — Frame the full beat starts: the reveal (snaps to the 16th-note grid) |
| `ending` | number | optional | — Frame of the final chord |
| `drums` | boolean | optional | — false: no beat |
| `stop` | boolean | optional | — Dead stop (~0.4 s) before the drop so it lands (default on for hype, epic, dark) |
| `seed` | integer | optional | — Another arrangement |
| `gain_db` | number | optional | — Default 0, lower under quiet talking (ducked ~14 LU under the words) |
| `duck_db` | number | optional default=-9 | — Dip under voiceover lines; 0 off |
| `name` | string | optional default="Music" | — Display name |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Score the whole piece

  ```json
  {"mood":"tech","hits":[60],"drop":60,"ending":120}
  ```

- A calm bed without drums

  ```json
  {"mood":"ambient","drums":false,"gain_db":-3}
  ```

Tips:

- Pass your big cuts as hits, the moment the piece opens up as drop, and the logo frame as ending; cut on beat_frames.
- Re-scoring replaces the bed (same name). Mood follows the style: tech/luxury/minimal for product, epic/dark for cinematic, hype/playful for energy.
- The bed dips 9 dB under voiceover lines by itself (duck_db; 0 turns it off), easing down just before the words and back up after.

Related: `addSound`, `mixAudio`, `setAudioFades`, `renderVideo`

CLI: `kinetic tool addMusic --params '<json>' --workspace ./out`

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

## voiceover

Voice a script as timed lines: built-in Kokoro (offline, free), or Gemini / ElevenLabs when the person asks (key in Settings). Each line is an audio layer that knows its words (captions, beats). Also voices, sample, pronounce, transcribe, words. guide voices.

- family: `audio` · mutating: `true` · transport: CLI `kinetic tool voiceover` / SDK `batch([{tool: "voiceover"}])` / MCP `tools/call {name: "voiceover"}`
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: voices, words, pronounce; generate with the built-in voice once it is installed (lines already voiced are cached)
- signature: `voiceover {action?: generate|voices|sample|pronounce|transcribe|words = "generate", extend?: bool = true, file?: string, find?: string, gain_db?: number = 0, gap?: number = 0.25 [s], layer?: string|int, locale?: string = "en-US", model?: string, name?: string = "VO", pronounce?: object, provider?: kokoro|gemini|elevenlabs, script?: string, speed?: number = 1, start_frame?: number = 0 [frame], style?: string|object, text?: string, voice?: string, voices?: object}`

| param | type | required/default | notes |
|---|---|---|---|
| `action` | enum["generate"|"voices"|"sample"|"pronounce"|"transcribe"|"words"] | optional default="generate" | — What to do |
| `script` | string | optional | — "NAME: line" lines; blank line = pause; [tags] <short pause> |
| `voice` | string | optional | — af_heart, am_michael, bf_emma... or gemini:Kore, elevenlabs:<name> |
| `voices` | object | optional | — Speaker → voice |
| `provider` | enum["kokoro"|"gemini"|"elevenlabs"] | optional | — Cloud only when asked |
| `model` | string | optional | — Cloud model (default newest) |
| `speed` | number | optional default=1 | — 0.5..2 |
| `style` | string\|object | optional | — Cloud delivery, or per speaker |
| `pronounce` | object | optional | — Word → "MEER-un" or /phonemes/ (saved) |
| `composition` | string\|integer | optional | |
| `start_frame` | number | optional default=0 | — First line's frame |
| `gap` | number | optional default=0.25 | — Seconds between lines |
| `name` | string | optional default="VO" | — Layer prefix |
| `gain_db` | number | optional default=0 | — Level nudge |
| `extend` | boolean | optional default=true | — Lengthen the comp to fit |
| `text` | string | optional | — sample / pronounce: words |
| `layer` | string\|integer | optional | — transcribe / words: audio layer |
| `file` | string | optional | — transcribe: audio file |
| `locale` | string | optional default="en-US" | — transcribe: language |
| `find` | string | optional | — words: word or phrase |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Voice a script (each line a timed layer)

  ```json
  {"script":"NARRATOR: Meet Miran. It turns one sentence into a motion graphic."}
  ```

- Cast two speakers

  ```json
  {"script":"MAYA: Wait, really?\nSAM: Watch this.","voices":{"MAYA":"af_bella","SAM":"am_puck"},"start_frame":10}
  ```

- Check and fix a pronunciation

  ```json
  {"action":"pronounce","text":"Miran ships in Q3.","pronounce":{"Miran":"MEER-un"}}
  ```

- Hear a voice before choosing

  ```json
  {"action":"sample","voice":"bf_emma"}
  ```

- Every voice, key and model

  ```json
  {"action":"voices"}
  ```

Tips:

- The built-in voice (Kokoro) is the default: offline, free, no key. Use gemini: or elevenlabs: voices only when the person asks; a missing key fails with no-key: tell them to add it in Miran Settings, never ask for it in chat.
- Run pronounce on brand and product names first; respell guessed words in syllables with the stressed one in capitals ("MEER-un"). Pronunciations and the cast are saved in the project.
- Re-calling with an edited script voices only the changed lines. words {find} and kit.when("...") give the frame a word starts: land visuals 2-4 frames before it; kit.captions() captions every word.

Related: `addMusic`, `runScript`, `setAudioGain`, `renderVideo`

CLI: `kinetic tool voiceover --params '<json>' --workspace ./out`

## character

Cut-out characters from SVG: cast (a starter character: ava, ben, kit, pip), rig (named parts become a jointed, parented rig with pivots at the joints, under a root null), parts (what a rigged character has), lipsync (mouth shapes timed to its voice lines). IK: setLayerField ik. guide characters.

- family: `scene` · mutating: `true` · transport: CLI `kinetic tool character` / SDK `batch([{tool: "character"}])` / MCP `tools/call {name: "character"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.character(...)` in runScript · inside runScript: yes
- signature: `character {action?: cast|rig|parts|lipsync = "rig", at?: number[] [px], audio?: string|int|array, character?: string, frame?: number = 0 [frame], height?: number = 600 [px], starter?: ava|ben|kit|pip}`

| param | type | required/default | notes |
|---|---|---|---|
| `action` | enum["cast"|"rig"|"parts"|"lipsync"] | optional default="rig" | — cast a starter (none: list them with each one's voice and personality), rig an imported SVG character, list its parts, or lip sync it |
| `character` | string | optional | — The name given to importSvg (its layers are <character>/<part>) |
| `starter` | enum["ava"|"ben"|"kit"|"pip"] | optional | — cast: a starter character to import and rig |
| `at` | array | optional | — cast: where its feet stand (px, y up) |
| `height` | number | optional default=600 | — cast: px tall |
| `composition` | string\|integer | optional | |
| `frame` | number | optional default=0 | — rig: the rest pose's frame; parts: where pivots are |
| `audio` | string\|integer\|array | optional | — lipsync: voice layer(s); default every line whose speaker is the character |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- The starters: looks, default voice and personality of each

  ```json
  {"action":"cast"}
  ```

- Ava, feet on the floor at the left, 600 px tall

  ```json
  {"action":"cast","starter":"ava","at":[-420,-340],"height":600}
  ```

- The robot (in runScript: const bot = kit.cast("kit", {at: [420, -340]}); never a variable named kit)

  ```json
  {"action":"cast","starter":"kit","at":[420,-340]}
  ```

- Rig your own SVG character (importSvg {svg, name:"Mia"} first)

  ```json
  {"action":"rig","character":"Mia"}
  ```

- Its parts, joints and rest pose

  ```json
  {"action":"parts","character":"Ava"}
  ```

- Lip sync to every line Ava speaks

  ```json
  {"action":"lipsync","character":"Ava"}
  ```

Tips:

- Act in runScript with the kit: kit.cast, kit.life (always), kit.say, kit.dialogue, kit.wave, kit.walk, kit.reach (guide characters).
- Starters: ava (af_heart), ben (am_michael), kit (am_puck), pip (af_bella); the cast listing gives each one's voice and personality. The robot is named kit like the helper library: call its variable bot in scripts.
- A conversation: kit.dialogue(lines, {dry: true}) times it without building (a line takes pause / hold for a beat of silence); read miran.help("kit.dialogue") for every option, which layers join its Stage and why backgrounds need bleed.
- Own art: one <g id> per part (head, torso, arm_l_upper, arm_l_lower, hand_l, leg_r_upper, mouth_mbp, ...); _l is the screen's left; limbs overlap where they join, so the joint lands there.
- Turn joints with animateProperty rotation (degrees, counter-clockwise); move or scale the whole character by its root null; re-run lipsync after changing lines.

Related: `runScript`, `voiceover`, `animateProperty`, `setLayerField`, `importSvg`

CLI: `kinetic tool character --params '<json>' --workspace ./out`

## reference

Learn a reference video's style: cuts and shot lengths, transitions, motion and camera, palette and contrast, type (read on device). compare: what differs in your render, worst first, with numbers. recipe: its editing (shot lengths, transitions, cuts on the beat) for edit recreate. guide reference.

- family: `design` · mutating: `false` · transport: CLI `kinetic tool reference` / SDK `batch([{tool: "reference"}])` / MCP `tools/call {name: "reference"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `reference {video: string, action?: analyze|compare|recipe = "analyze", against?: string, read_text?: bool = true}`

| param | type | required/default | notes |
|---|---|---|---|
| `action` | enum["analyze"|"compare"|"recipe"] | optional default="analyze" | — What to do (recipe: its editing as a recipe for edit recreate) |
| `video` | string | required | — The reference video (in the workspace) |
| `against` | string | optional | — compare: your video (default: a draft render of the composition) |
| `composition` | string\|integer | optional | — compare: what to render when `against` is not given |
| `read_text` | boolean | optional default=true | — Read the type on screen (slower) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Learn a reference video's style

  ```json
  {"video":"ref.mp4"}
  ```

- Its editing as a recipe for edit recreate

  ```json
  {"action":"recipe","video":"ref.mp4"}
  ```

- What differs in my piece (renders a draft)

  ```json
  {"action":"compare","video":"ref.mp4","read_text":false}
  ```

Tips:

- Learn the style, not the content: take its pacing, palette, contrast and type treatment into your own concept; never copy its footage, logos or words.
- style gives a ready start: palette, background, accent (brand {action:"save", theme}), shot length in frames at 30 fps, the main transition and the motion level.
- compare after each pass: fix the first differences (pacing and contrast matter most), render again, compare again. Analyses are cached in reference/.

Related: `brand`, `renderVideo`, `renderContactSheet`, `critiqueMotion`

CLI: `kinetic tool reference --params '<json>' --workspace ./out`

## footage

Read real footage on the device. index once (cached): shots, framing, faces, text, sound, words. Then map (levels 0-3), search, transcript, best (frames, takes, thumbnail), anchor, watch (frames over the sound), describe, frame (a still as a PNG, at start or an anchor), area (text colour and shadow or scrim that read over a box of a clip). guide footage.

- family: `footage` · mutating: `false` · transport: CLI `kinetic tool footage` / SDK `batch([{tool: "footage"}])` / MCP `tools/call {name: "footage"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: map, search, transcript, best, anchor (once a clip is indexed), area; not index, watch or describe
- signature: `footage {action: index|map|search|transcript|best|anchor|watch|describe|…, anchor?: any, as?: words|sentences|srt|text = "sentences", asset?: string, assets?: string[], box?: number[] [px], count?: int = 5, detail?: fast|full = "fast", edge?: start|end, end?: number [s], face?: any, folder?: string, frames?: int = 12 [frames], label?: string, language?: string = "en-US", layer?: string|int, level?: int = 1, like?: object, limit?: int = 10, max_chars?: int = 8000, query?: string, range?: number[] [frames], shots?: int[], sound?: string, start?: number, text?: string, what?: frames|takes|thumbnail = "frames", words?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `action` | enum["index"|"map"|"search"|"transcript"|"best"|"anchor"|"watch"|"describe"|"frame"|"area"] | required | — What to do |
| `asset` | string | optional | — Clip: asset id/name or workspace file |
| `assets` | array | optional | — Several clips (default: all video/audio assets) |
| `folder` | string | optional | — index: every clip in this folder |
| `detail` | enum["fast"|"full"] | optional default="fast" | — index: full adds labels, aesthetics, look-alikes, people |
| `language` | string | optional default="en-US" | — index: spoken language |
| `level` | integer | optional default=1 | (min=0, max=3) — map: 0 clips, 1 shots, 2 sentences, 3 samples |
| `max_chars` | integer | optional default=8000 | (min=500, max=60000) — map: text budget |
| `query` | string | optional | — search: free text (said, seen, heard, on screen) |
| `words` | string | optional | — search: exact phrase said |
| `label` | string | optional | — search: thing seen (full) |
| `sound` | string | optional | — search: sound event |
| `text` | string | optional | — search: text on screen |
| `face` | any | optional | — search: true, "closeup" or a person id (full) |
| `like` | object | optional | — search: {asset, time} look-alikes (full) |
| `limit` | integer | optional default=10 | (min=1, max=100) — search: results |
| `as` | enum["words"|"sentences"|"srt"|"text"] | optional default="sentences" | — transcript: form |
| `what` | enum["frames"|"takes"|"thumbnail"] | optional default="frames" | — best: what to rank |
| `count` | integer | optional default=5 | (min=1, max=50) — best: how many |
| `anchor` | any | optional | — anchor: {word|sentence|shot|cut: n, edge?}, {event, n?}, {beat|downbeat|bar: n}, {marker}, {seconds} |
| `edge` | enum["start"|"end"] | optional | — anchor: the start (default) or end of the word, sentence or shot (the same as edge inside the anchor) |
| `layer` | string\|integer | optional | — anchor: a layer instead of asset: the frame in its composition; area: the clip layer |
| `composition` | string\|integer | optional | — anchor, area: where layer is (default the first); transcript without asset: the words said on its timeline, through the cut |
| `box` | array | optional | — area: [x, y, w, h] composition px (centre of the box, y up) |
| `range` | array | optional | — area: [from, to) frames (default the clip's) |
| `start` | number | optional | (min=0) — watch: from second; frame: at second |
| `end` | number | optional | (min=0) — watch: to second (60 s max) |
| `frames` | integer | optional default=12 | (min=6, max=24) — watch: frames |
| `shots` | array | optional | — describe: only these shots |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Index a clip once (shots, faces, words, sounds; cached)

  ```json
  {"action":"index","asset":"clip.mp4"}
  ```

- A line per shot

  ```json
  {"action":"map","level":1,"assets":["clip.mp4"]}
  ```

- Find a moment by what is said, seen or heard

  ```json
  {"action":"search","query":"miran","assets":["clip.mp4"]}
  ```

- The words as subtitles

  ```json
  {"action":"transcript","asset":"clip.mp4","as":"srt"}
  ```

- The best frames for a thumbnail

  ```json
  {"action":"best","asset":"clip.mp4","what":"thumbnail","count":3}
  ```

- When the first shot ends

  ```json
  {"action":"anchor","asset":"clip.mp4","anchor":{"shot":0,"edge":"end"}}
  ```

- Look at a stretch: frames over its sound

  ```json
  {"action":"watch","asset":"clip.mp4","start":0,"end":1.5,"frames":6}
  ```

Tips:

- Index once (a direct call; a job for long clips), then map, search, transcript, best, anchor and watch answer from the cache, also inside runScript.
- Read map level 0 or 1 first, search for the moments, then watch the stretches you will cut: frames over the sound with the words at their times.
- Name moments by anchors ({sentence: n}, {word: n}, {shot: n}, {event: "laughter"}), not by seconds; footage anchor turns one into seconds.

Related: `importAsset`, `createLayer`, `reference`, `jobStatus`, `edit`

CLI: `kinetic tool footage --params '<json>' --workspace ./out`

## edit

Cut footage on tracks (V1 picture, V2 cover, A1 sound): by words, beats, length; transitions, reframe, multicam, paper edit, recreate, broll. Times: s or anchors ({sentence: 3}). guide edit.

- family: `edit` · mutating: `true` · transport: CLI `kinetic tool edit` / SDK `batch([{tool: "edit"}])` / MCP `tools/call {name: "edit"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.edit(...)` in runScript · inside runScript: all but multicam (it decodes every camera's sound: call it directly)
- signature: `edit {action: assemble|list|trim|split|remove|move|slip|cutWords|…, a?: string, angle?: number [deg], angles?: string[], aspect?: 9:16|1:1|4:5|16:9, asset?: string, assets?: string[], at?: any, audio?: lead|lag, b?: string, by?: number [s], clip?: string, clips?: object[], color?: string, dir?: left|right|up|down, end_on?: downbeat|sentence, every?: any, follow?: auto|face|person|saliency|center = "auto", frames?: int, importance?: object[], in?: any, keep?: string[][], kind?: dissolve|dip|whip|zoom|luma|slide|glitch|morph, language?: string, leave?: number [s], master?: string, max_cost?: number, min?: number [s], min_shot?: number [s], mode?: export|apply, music?: string, name?: string, out?: any, over?: object[], platform?: reels|tiktok|shorts, punch?: bool, range?: number[] [frames], recipe?: object, remove?: string[][], repeats?: bool = false, ripple?: bool = true, seconds?: number, smooth?: number = 0.6, source?: footage|stills|generate|auto = "footage", text?: string, to?: number [frame], track?: string, wide?: string, window?: number[] [s], words?: string[]}`

| param | type | required/default | notes |
|---|---|---|---|
| `action` | string | required | — What to do |
| `composition` | string\|integer | optional | — The sequence |
| `clips` | array | optional | — assemble: [{asset, in, out, speed?, track?, at?}] |
| `clip` | string | optional | — A clip (layer) |
| `asset` | string | optional | — A whole clip as a new sequence |
| `track` | string | optional | — V1, V2, A1 ... |
| `name` | string | optional | — New composition's name |
| `in` | any | optional | — trim: source in (s or anchor) |
| `out` | any | optional | — trim: source out |
| `at` | any | optional | — split: a frame; transition/jl: {cut: n} |
| `to` | number | optional | (min=0) — move: start frame |
| `by` | number | optional | — slip: source seconds |
| `range` | array | optional | — remove: [from, to) frames |
| `ripple` | boolean | optional default=true | — Close the gap |
| `text` | string | optional | — cutWords: new transcript; paper: the edit |
| `remove` | array | optional | — cutWords: [[word...]] to cut |
| `keep` | array | optional | — cutWords: [[word...]] to keep |
| `min` | number | optional | (min=0.1, max=10) — removeSilence: shortest pause, s |
| `leave` | number | optional | (min=0, max=2) — removeSilence: pause kept, s |
| `punch` | boolean | optional | — removeSilence: 112% at jump cuts |
| `words` | array | optional | — removeFillers: the fillers |
| `repeats` | boolean | optional default=false | — removeFillers: also false starts, repeats |
| `language` | string | optional | — removeFillers: language (en-US) |
| `music` | string | optional | — The music layer (recreate: a file) |
| `every` | any | optional | — beatCut: beat|downbeat|bar|n beats; snap: beat|downbeat|word |
| `seconds` | number | optional | (min=1, max=10800) — tighten/recreate: length |
| `importance` | array | optional | — tighten: [{sentence, score}] |
| `end_on` | enum["downbeat"|"sentence"] | optional | — tighten: the end |
| `kind` | enum["dissolve"|"dip"|"whip"|"zoom"|"luma"|"slide"|"glitch"|"morph"] | optional | — transition |
| `dir` | enum["left"|"right"|"up"|"down"] | optional | — whip/slide direction |
| `color` | string | optional | — dip colour |
| `angle` | number | optional | — luma angle, deg |
| `frames` | integer | optional | (min=1, max=120) — transition/jl length |
| `audio` | enum["lead"|"lag"] | optional | — jl: J lead, L lag |
| `aspect` | enum["9:16"|"1:1"|"4:5"|"16:9"] | optional | — reframe: shape |
| `follow` | enum["auto"|"face"|"person"|"saliency"|"center"] | optional default="auto" | — reframe: follows |
| `platform` | enum["reels"|"tiktok"|"shorts"] | optional | — reframe: app UI to clear |
| `smooth` | number | optional default=0.6 | (min=0, max=1) — reframe: 0 snappy, 1 slow |
| `angles` | array | optional | — multicam: cameras |
| `wide` | string | optional | — multicam: the wide angle |
| `master` | string | optional | — multicam: master sound |
| `window` | array | optional | — multicam: [from, to) s of the master |
| `min_shot` | number | optional | (min=0.5, max=30) — multicam: shortest shot, s |
| `mode` | enum["export"|"apply"] | optional | — paper |
| `a` | string | optional | — diff: before |
| `b` | string | optional | — diff: after |
| `recipe` | object | optional | — recreate: reference recipe |
| `assets` | array | optional | — recreate/broll: footage |
| `over` | array | optional | — broll: [{sentence} | {words: [a, b]}] |
| `source` | enum["footage"|"stills"|"generate"|"auto"] | optional default="footage" | — broll: from (generate, auto: need max_cost) |
| `max_cost` | number | optional | (min=0, max=1000) — Most to spend, USD |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Lay three takes end to end (seconds or anchors from the index)

  ```json
  {"action":"assemble","name":"Story","clips":[{"asset":"talk.mp4","in":{"sentence":2},"out":{"sentence":2,"edge":"end"}},{"asset":"talk.mp4","in":0,"out":{"sentence":0,"edge":"end"}},{"asset":"broll.mp4","in":0.5,"out":2,"track":"V2","at":20}]}
  ```

- Edit the transcript: the picture follows

  ```json
  {"action":"cutWords","asset":"talk.mp4","text":"Our pricing is simple. Thank you all for coming."}
  ```

- Shorten pauses, punching in to hide the jumps

  ```json
  {"action":"removeSilence","composition":"Cut","min":0.35,"leave":0.12,"punch":true}
  ```

- Cut on every downbeat of the music

  ```json
  {"action":"beatCut","composition":"Cut","music":"Music","every":"downbeat"}
  ```

- Down to 30 seconds, keeping the best sentences

  ```json
  {"action":"tighten","composition":"Cut","seconds":30,"importance":[{"sentence":0,"score":5}],"end_on":"downbeat"}
  ```

- An 8-frame dissolve at the second cut

  ```json
  {"action":"transition","at":{"cut":1},"kind":"dissolve","frames":8}
  ```

- A J cut: the next voice starts 8 frames early

  ```json
  {"action":"jl","at":{"cut":0},"audio":"lead","frames":8}
  ```

- Vertical, following the speaker, captions clear of TikTok's buttons

  ```json
  {"action":"reframe","composition":"Cut","aspect":"9:16","follow":"auto","platform":"tiktok"}
  ```

- The edit as text (edit it, then mode apply)

  ```json
  {"action":"paper","mode":"export","composition":"Cut"}
  ```

- Two cameras, synced by sound, cut to who speaks

  ```json
  {"action":"multicam","angles":["camA.mp4","camB.mp4"],"wide":"camA.mp4"}
  ```

- Cover a sentence with matching footage

  ```json
  {"action":"broll","over":[{"sentence":2}],"source":"footage"}
  ```

Tips:

- Index the clips first (footage index): words, sentences, pauses, faces and shots come from it.
- Cut by meaning: anchors ({sentence: 3}) and cutWords with the transcript as it should read, not seconds by ear.
- Every action is one undo step; paper export shows the whole edit as text, diff shows what changed.

Related: `footage`, `detectBeats`, `renderVideo`, `reference`

CLI: `kinetic tool edit --params '<json>' --workspace ./out`

## cutout

Lift people or any subject off footage on the device: a steady matte, placed as a composition over the clip (text behind people, outlines, new backgrounds). person (people: [n] left to right); subject (pick: {x, y} picture fractions); depth (an image layer: a 3D photo); cleanPlate; contour (an outline following a cutout). A job when long. guide vfx.

- family: `vfx` · mutating: `true` · transport: CLI `kinetic tool cutout` / SDK `batch([{tool: "cutout"}])` / MCP `tools/call {name: "cutout"}`
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: none (they decode the clip frame by frame and run as jobs: call them directly)
- signature: `cutout {action: person|subject|depth|cleanPlate|contour, layer: string|int, color?: string, far?: number, frames?: int = 40 [frames], invert?: bool = false, mode?: above|replace = "above", near?: number, people?: int[], pick?: object|array, points?: int = 160, quality?: fast|balanced|accurate = "balanced", range?: number[] [frames], simplify?: number = 1.0 [px], width?: number [px]}`

| param | type | required/default | notes |
|---|---|---|---|
| `action` | enum["person"|"subject"|"depth"|"cleanPlate"|"contour"] | required | — What to make |
| `layer` | string\|integer | required | — The video layer (contour: the cutout layer; depth: or an image) |
| `composition` | string\|integer | optional | |
| `quality` | enum["fast"|"balanced"|"accurate"] | optional default="balanced" | — person |
| `people` | array | optional | — person: these, left to right |
| `pick` | object\|array | optional | — subject: {x, y} on the range's first frame, or several points [{x, y}, {x, y, out: true}] (out: not the subject) |
| `mode` | enum["above"|"replace"] | optional default="above" | — above keeps the original under it; replace turns it off |
| `invert` | boolean | optional default=false | — Keep all but the subject |
| `range` | array | optional | — [from, to) frames |
| `frames` | integer | optional default=40 | (min=5, max=200) — cleanPlate: frames in the median |
| `points` | integer | optional default=160 | (min=16, max=1024) — contour |
| `simplify` | number | optional default=1 | (min=0, max=20) — contour: px |
| `color` | string | optional | — contour stroke |
| `width` | number | optional | (min=0.5, max=80) — contour stroke px |
| `near` | number | optional | (min=0, max=1) — depth: keep nearer than this (0 near, 1 far) |
| `far` | number | optional | (min=0, max=1) — depth: and farther than this |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Lift the people off the footage (text can then go behind them)

  ```json
  {"action":"person","layer":"Clip"}
  ```

- Only the second person from the left

  ```json
  {"action":"person","layer":"Clip","people":[1]}
  ```

- The thing under a point, followed through the clip

  ```json
  {"action":"subject","layer":"Clip","pick":{"x":0.62,"y":0.55}}
  ```

- An outline that follows the cutout

  ```json
  {"action":"contour","layer":"Clip subject","points":160,"color":"#FFFFFF","width":4}
  ```

- The background without what moves

  ```json
  {"action":"cleanPlate","layer":"Clip"}
  ```

Tips:

- Text behind a person: cutout person, then put the text between the clip and the cutout layer it made (mode above keeps the original as the background).
- person finds real people in camera footage; subject finds anything (pick a point for one of several); a no-subject error says to try the other.
- One pick point can catch only part of a figure (the shirt, not the head). Pick a point on each part, and mark what clings to it but is not it with out: true: pick: [{x, y} head, {x, y} body, {x, y, out: true} the wall behind].
- The matte is made once per clip (a job for long ones) and cached: call again with the same params to place it.

Related: `track`, `setMatte`, `addEffect`, `footage`

CLI: `kinetic tool cutout --params '<json>' --workspace ./out`

## track

Follow things in footage, on the device. point/object: a null that moves with what you mark (parent graphics to it); plane: corner nulls on a screen or sign, onto pins a layer there; stabilize; level; body/hands/face: nulls per joint; mocap: a body drives a character; redact: hide secrets, emails, phones, cards, faces or text. Points are picture fractions from the top-left. A job when long. guide vfx.

- family: `vfx` · mutating: `true` · transport: CLI `kinetic tool track` / SDK `batch([{tool: "track"}])` / MCP `tools/call {name: "track"}`
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: none (they decode the clip frame by frame and run as jobs: call them directly)
- signature: `track {action: point|object|plane|stabilize|level|body|hands|face|…, layer: string|int, at?: object, box?: object, character?: string, corners?: number[][], detect?: screen, find?: string[], frame?: number [frame], joints?: string[], mode?: smooth|lock = "smooth", name?: string, onto?: string, person?: int, range?: number[] [frames], strength?: number = 0.5 [s], style?: blur|pixelate|box = "blur"}`

| param | type | required/default | notes |
|---|---|---|---|
| `action` | enum["point"|"object"|"plane"|"stabilize"|"level"|"body"|"hands"|"face"|"mocap"|"redact"] | required | — What to follow |
| `layer` | string\|integer | required | — The video layer |
| `composition` | string\|integer | optional | |
| `at` | object | optional | — point: {frame, x, y} |
| `box` | object | optional | — object: {frame, x, y, w, h}, x y its top-left |
| `corners` | array | optional | — plane: [x, y] x4 clockwise from the top-left, on frame |
| `frame` | number | optional | (min=0) — plane: the corners' frame |
| `detect` | enum["screen"] | optional | — plane: find the screen itself |
| `onto` | string | optional | — plane: the layer to pin onto it |
| `range` | array | optional | — [from, to) frames |
| `name` | string | optional | — The null's name |
| `mode` | enum["smooth"|"lock"] | optional default="smooth" | — stabilize |
| `strength` | number | optional default=0.5 | (min=0.05, max=5) — stabilize: seconds evened out |
| `person` | integer | optional | (min=0, max=7) — Nth from the left (default: the main subject, the largest) |
| `joints` | array | optional | — body/hands: e.g. nose, left_wrist, index_tip |
| `character` | string | optional | — mocap |
| `find` | array | optional | — redact: secrets, emails, phones, cards, faces, text:<pattern> |
| `style` | enum["blur"|"pixelate"|"box"] | optional default="blur" | — redact |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- A null that follows a point (parent a label to it)

  ```json
  {"action":"point","layer":"Clip","at":{"frame":0,"x":0.42,"y":0.31},"name":"Logo pin"}
  ```

- Follow a thing that turns and grows

  ```json
  {"action":"object","layer":"Clip","box":{"frame":12,"x":0.3,"y":0.4,"w":0.2,"h":0.25}}
  ```

- Put a picture on a phone screen

  ```json
  {"action":"plane","layer":"Clip","detect":"screen","onto":"App shot"}
  ```

- Smooth a handheld shot

  ```json
  {"action":"stabilize","layer":"Clip","mode":"smooth","strength":0.5}
  ```

- Blur API keys and emails in a screen recording

  ```json
  {"action":"redact","layer":"Clip","find":["secrets","emails"],"style":"blur"}
  ```

- A null on each wrist

  ```json
  {"action":"body","layer":"Clip","joints":["left_wrist","right_wrist"]}
  ```

- The person's moves drive a character

  ```json
  {"action":"mocap","layer":"Clip","character":"Ava"}
  ```

Tips:

- Points, boxes and corners are fractions of the picture from the top-left, on the frame you give.
- A tracked null is parented to the footage: parent graphics to the null and set their position to [0, 0] to sit on the point.
- Tracks are cached per clip: when a long one runs as a job, call again with the same params once it is done and it is placed at once.
- lost_at says where a track stopped (covered, out of the picture); track again from a later frame.

Related: `cutout`, `parentLayer`, `addEffect`, `footage`

CLI: `kinetic tool track --params '<json>' --workspace ./out`

## enhance

Make footage better on the device. slowmo (factor 2|4|8: new in-between frames), upscale (2|4), denoise, motionBlur (amount). Writes a new clip to enhanced/ (cached); place:true swaps the layer's clip, slowmo grows the layer by factor. Sound: voice (clean speech), music (to length), ramp (speed ramps), foley. A job when long. guide vfx.

- family: `vfx` · mutating: `true` · transport: CLI `kinetic tool enhance` / SDK `batch([{tool: "enhance"}])` / MCP `tools/call {name: "enhance"}`
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: none (it decodes and re-encodes whole clips as a job: call it directly)
- signature: `enhance {action: slowmo|upscale|denoise|motionBlur|voice|music|ramp|foley, amount?: number = 0.5, asset?: string, density?: number, end?: natural|cut, factor?: int, keep_length?: bool, layer?: string|int, music?: string|int, place?: bool = false, range?: number[] [frames], seconds?: number, strength?: number, style?: velocity|impact}`

| param | type | required/default | notes |
|---|---|---|---|
| `action` | enum["slowmo"|"upscale"|"denoise"|"motionBlur"|"voice"|"music"|"ramp"|"foley"] | required | — What to do |
| `layer` | string\|integer | optional | — The video layer (id or name) |
| `asset` | string | optional | — A clip (asset id or name, or a workspace file) instead of a layer |
| `composition` | string\|integer | optional | — Composition of the layer (foley: the one to score) |
| `factor` | integer | optional | (min=2, max=8) — slowmo 2, 4 or 8; upscale 2 or 4 |
| `range` | array | optional | — [from, to): layer frames, or seconds with asset |
| `place` | boolean | optional default=false | — Swap the layer's clip for the new one |
| `amount` | number | optional default=0.5 | (min=0, max=1) — motionBlur strength |
| `strength` | number | optional | (min=0, max=1) — denoise (0.5) or voice cleanup strength |
| `seconds` | number | optional | (min=0.5, max=3600) — music: target length |
| `end` | enum["natural"|"cut"] | optional | — music: keep the song's ending, or cut |
| `style` | enum["velocity"|"impact"] | optional | — ramp |
| `music` | string\|integer | optional | — ramp: the music layer to land peaks on |
| `keep_length` | boolean | optional | — slowmo/ramp: keep the layer's length |
| `density` | number | optional | (min=0, max=1) — foley: how many sounds |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- The best second, slowed with new in-between frames

  ```json
  {"action":"slowmo","layer":"Clip","factor":2,"range":[0,15],"place":true}
  ```

- Calm sensor noise on a dark shot

  ```json
  {"action":"denoise","layer":"Clip","strength":0.6,"range":[0,8]}
  ```

- Upscale a few frames

  ```json
  {"action":"upscale","layer":"Clip","factor":2,"range":[0,4]}
  ```

- Clean a noisy voice recording

  ```json
  {"action":"voice","layer":"Music","strength":0.8}
  ```

- Music cut to the film's length on a downbeat

  ```json
  {"action":"music","layer":"Song","seconds":7}
  ```

- A velocity edit: slow on each hit, fast between, hits on the beat

  ```json
  {"action":"ramp","layer":"Clip","music":"Song","style":"impact"}
  ```

- A first pass of sound design

  ```json
  {"action":"foley","composition":"Main","density":0.5}
  ```

Tips:

- slowmo makes real in-between frames (macOS 15.4+; else frame blends that ghost). The layer grows by the factor and later clips do not move: edit ripples them; keep_length cuts the slowed clip instead.
- Each video action writes a new clip to enhanced/ and caches it. A long clip runs as a job: when jobStatus says done, call enhance again with the same params and it is placed at once.
- upscale and denoise are learned on macOS 26+ (else Lanczos and a temporal average, which add no detail); the result's backend and note say which ran. A slowed clip is silent.
- Sound: voice cleans speech (sample-aligned, words keep their timing); music jumps between two alike bars on a downbeat so the song keeps its ending; ramp slows on each burst of motion and lands it on a beat (enhance slowmo first for smooth slow beats); foley spots the whole cut.

Related: `edit`, `footage`, `cutout`, `addSound`, `addMusic`, `mixAudio`

CLI: `kinetic tool enhance --params '<json>' --workspace ./out`

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

## importHtml

Lay out a screen written as HTML + CSS in a local Chrome and import it: each element with an id (or data-part) becomes its own layer where the page put it, over a base layer. Real-looking product UI for kit.screen3d, kit.frame and kit.cursor (guide ui).

- family: `assets` · mutating: `true` · transport: CLI `kinetic tool importHtml` / SDK `batch([{tool: "importHtml"}])` / MCP `tools/call {name: "importHtml"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.importHtml(...)` in runScript · inside runScript: yes
- signature: `importHtml {at?: number[], dark?: bool, height?: number [px], html?: string, name?: string, parts?: bool = true, path?: string, scale?: number [px], width?: number [px]}`

| param | type | required/default | notes |
|---|---|---|---|
| `html` | string | optional | — A fragment or a whole document; give the parts that move an id |
| `path` | string | optional | — Instead of html: a workspace .html file |
| `name` | string | optional | — Layer name prefix (default "Screen") |
| `width` | number | optional | — CSS px (default 1440) |
| `height` | number | optional | — CSS px (default 900) |
| `at` | array | optional | — [x, y] of the page centre (default [0, 0]) |
| `scale` | number | optional | (min=1, max=4) — Pixels per CSS px (2; 3 for deep pushes) |
| `parts` | boolean | optional default=true | — false: one flat image of the page |
| `dark` | boolean | optional | — Dark mode |
| `composition` | string\|integer | optional | |

Example: `{}`

CLI: `kinetic tool importHtml --params '<json>' --workspace ./out`

## captureSite

Screenshot a public web page in local Chrome; read its colours, fonts, logo, headline and the boxes of headings, buttons, images and cards. place: true adds it as a layer. Never invent a page.

- family: `assets` · mutating: `true` · transport: CLI `kinetic tool captureSite` / SDK `batch([{tool: "captureSite"}])` / MCP `tools/call {name: "captureSite"}`
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `captureSite {url: string, at?: number[], dark?: bool, fill?: number [frame], height?: number [px], name?: string, parts?: bool = false, place?: bool = false, scale?: number [px], scroll?: number [px], width?: number [px]}`

| param | type | required/default | notes |
|---|---|---|---|
| `url` | string | required | — A public http(s) page |
| `width` | number | optional | — CSS px (1440) |
| `height` | number | optional | — CSS px (900) |
| `scale` | number | optional | (min=1, max=3) — Pixels per CSS px (2) |
| `dark` | boolean | optional | — Dark mode |
| `scroll` | number | optional | — CSS px to scroll first |
| `place` | boolean | optional default=false | — Add it as a layer; boxes in comp px |
| `parts` | boolean | optional default=false | — Boxes as layers |
| `fill` | number | optional | — Share of the frame width (0.8) |
| `at` | array | optional | — [x, y] of its centre |
| `name` | string | optional | — Default the host name |
| `composition` | string\|integer | optional | |
CLI: `kinetic tool captureSite --params '<json>' --workspace ./out`

## generateImage

Make an image with the user's OpenRouter key (Miran Settings): a plate, an illustration, a texture. Saved with its prompt; the same call reuses it. svg: true traces it to animate. Never words, logos or UI.

- family: `assets` · mutating: `false` · transport: CLI `kinetic tool generateImage` / SDK `batch([{tool: "generateImage"}])` / MCP `tools/call {name: "generateImage"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `generateImage {prompt: string, aspect?: string, fresh?: bool, model?: string, name?: string, reference?: string|array, size?: 1K|2K|4K, svg?: boolean|string, transparent?: bool}`

| param | type | required/default | notes |
|---|---|---|---|
| `prompt` | string | required | — Subject in the film's look (palette, medium, light) |
| `aspect` | string | optional | — "16:9", "9:16", "1:1"... (the composition's) |
| `size` | enum["1K"|"2K"|"4K"] | optional | — Resolution (2K) |
| `svg` | boolean\|string | optional | — Also an SVG: true, "flat", "detailed", "lineart" |
| `transparent` | boolean | optional | — Cut-out background where offered |
| `reference` | string\|array | optional | — Image path(s), or {asset, time} frames, to edit or match |
| `name` | string | optional | — File name |
| `model` | string | optional | — OpenRouter model id (this call) |
| `fresh` | boolean | optional | — New image, not the saved one |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- A plate in the film's look

  ```json
  {"prompt":"misty pine valley at dawn, flat gouache, periwinkle and rose palette, soft light from the left","aspect":"16:9"}
  ```

- Flat art traced to an SVG

  ```json
  {"prompt":"a paper boat, flat vector shapes, three colours, plain white background","svg":true,"name":"boat"}
  ```

- Match a reference

  ```json
  {"prompt":"the same mascot waving, same colours and style","reference":"miran/film/art/mascot.png","aspect":"1:1"}
  ```

CLI: `kinetic tool generateImage --params '<json>' --workspace ./out`

## generateVideo

A video shot in the cloud (user's OpenRouter key), priced first, refused over the Settings caps. Actions: generate, estimate, models, extend, bridge, bible (a consistent look), plan (shots under a budget). Same call = saved file. guide generate.

- family: `assets` · mutating: `false` · transport: CLI `kinetic tool generateVideo` / SDK `batch([{tool: "generateVideo"}])` / MCP `tools/call {name: "generateVideo"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `generateVideo {action?: generate|estimate|models|extend|bridge|bible|plan, aspect?: string, audio?: bool, budget_usd?: number, cast?: string[], draft?: bool, first_frame?: any, for?: element, fresh?: bool, from?: object, last_frame?: any, loop?: bool, max_cost?: number, model?: string, name?: string, prompt?: string, references?: string[], resolution?: string, seconds?: number, seed?: int, set?: object, shots?: object[], to?: object, video?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `action` | enum["generate"|"estimate"|"models"|"extend"|"bridge"|"bible"|"plan"] | optional | — Default generate |
| `prompt` | string | optional | — Subject, action, camera, light, look |
| `model` | string | optional | — Model id (default: Settings) |
| `draft` | boolean | optional | — A cheap preview: the draft model (Settings) at its lowest resolution |
| `seconds` | number | optional | (min=1, max=60) — Length |
| `resolution` | string | optional | — 480p..4K |
| `aspect` | string | optional | — Default: the comp's |
| `first_frame` | any | optional | — Path or {asset, time} |
| `last_frame` | any | optional | — Path or {asset, time} |
| `references` | array | optional | — Image paths |
| `cast` | array | optional | — Bible names |
| `audio` | boolean | optional | — Generated sound |
| `seed` | integer | optional | — Seed |
| `loop` | boolean | optional | — Ends where it starts |
| `for` | enum["element"] | optional | — On black, to key over footage |
| `video` | string | optional | — extend: the shot |
| `from` | object | optional | — bridge: {asset, time} |
| `to` | object | optional | — bridge: {asset, time} |
| `set` | object | optional | — bible: {name, images, description} |
| `shots` | array | optional | — plan: [{id, seconds, importance, needs_motion}] |
| `budget_usd` | number | optional | (min=0) — plan: dollars |
| `max_cost` | number | optional | (min=0) — Dollars this call may cost |
| `name` | string | optional | — File name |
| `fresh` | boolean | optional | — Not the saved file |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- The price first

  ```json
  {"action":"estimate","prompt":"a paper boat drifts across a rainy street, low angle, soft light","seconds":5,"resolution":"480p"}
  ```

- A draft shot from a still

  ```json
  {"prompt":"the camera pushes in slowly as rain falls","first_frame":"miran/film/art/street.png","seconds":5,"resolution":"480p"}
  ```

- A cheap preview on the draft model (Settings) to judge the idea

  ```json
  {"action":"estimate","prompt":"a paper boat drifts across a rainy street","seconds":5,"draft":true}
  ```

- A seamless background loop

  ```json
  {"prompt":"slow drifting ink clouds, deep blue","first_frame":"miran/film/art/ink.png","loop":true,"model":"google/veo-3.1-fast","seconds":6,"audio":false}
  ```

- A fire element to key over footage

  ```json
  {"prompt":"a small campfire flame, close","for":"element","seconds":5}
  ```

- Keep a character the same

  ```json
  {"action":"bible","set":{"name":"Mia","images":["miran/film/art/mia.png"],"description":"short red hair, green raincoat"}}
  ```

- Cover a film in a budget

  ```json
  {"action":"plan","budget_usd":2,"shots":[{"id":"hero","seconds":5,"importance":5,"needs_motion":true},{"id":"end card","seconds":3,"importance":2,"needs_motion":false}]}
  ```

Tips:

- Estimate first and say the price; over a cap the call is refused (limit-exceeded): offer a cheaper path, never retry around it.
- Cheapest first: motion graphics, a parallax still, a 480p draft, then the final; a still is often enough for 3-6 s.
- A shot is a material: place puts it anywhere (background, inside text, element, screen, matte).

Related: `generateImage`, `generate`, `place`, `edit`

CLI: `kinetic tool generateVideo --params '<json>' --workspace ./out`

## generate

Any cloud model with the user's key on fal, Replicate or OpenRouter (lipsync, music, edits, upscale, presenter, 3D). input: the model's own fields; files: workspace files sent as data URLs. Unknown prices need max_cost. guide generate.

- family: `assets` · mutating: `false` · transport: CLI `kinetic tool generate` / SDK `batch([{tool: "generate"}])` / MCP `tools/call {name: "generate"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `generate {input: object, model: string, provider: fal|replicate|openrouter, files?: object, fresh?: bool, max_cost?: number, name?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `provider` | enum["fal"|"replicate"|"openrouter"] | required | — Where it runs |
| `model` | string | required | — fal-ai/..., owner/name[:version], or a video model id |
| `input` | object | required | — The model's fields |
| `files` | object | optional | — {field: path or {asset, time}} |
| `max_cost` | number | optional | (min=0) — Dollars this call may cost |
| `name` | string | optional | — File name |
| `fresh` | boolean | optional | — Not the saved result |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Lipsync a voice onto a clip (fal)

  ```json
  {"provider":"fal","model":"fal-ai/sync-lipsync","input":{"sync_mode":"cut_off"},"files":{"video_url":"talk.mp4","audio_url":"line.wav"},"max_cost":0.5}
  ```

- Any Replicate model

  ```json
  {"provider":"replicate","model":"owner/model-name","input":{"prompt":"..."},"max_cost":0.2}
  ```

Tips:

- input is the model's own fields as its page names them; files fills fields with workspace files as data URLs.
- fal and Replicate do not say a price before: pass max_cost and tell the user the figure.

Related: `generateVideo`, `generateImage`, `place`

CLI: `kinetic tool generate --params '<json>' --workspace ./out`

## place

Put any image or video into any role in one call: layer, background, behind/front a layer, inside (fills text or a shape), element (fire or smoke on black, green or alpha), screen (pinned to a tracked plane), matte, replace (swap a layer's media), sprite (a particle's picture), transition (a bridge clip into a cut). media: a path, an asset, or {asset, time} (a still of a video). Works in runScript.

- family: `edit` · mutating: `true` · transport: CLI `kinetic tool place` / SDK `batch([{tool: "place"}])` / MCP `tools/call {name: "place"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.place(...)` in runScript · inside runScript: yes
- signature: `place {media: string|object, as?: layer|background|behind|front|inside|element|screen|matte|… = "layer", at?: number [frame], blend?: normal|multiply|screen|overlay|add|darken|lighten|subtract|…, cut?: int, duration?: number [frames], fit?: cover|contain|stretch, key?: auto|black|green|alpha = "auto", layer?: string|int, loop?: bool = false, matte?: luma|alpha|luma_inverted|alpha_inverted = "luma", name?: string, opacity?: number, parent?: string|int, position?: number[] [px], size?: number[] [px]}`

| param | type | required/default | notes |
|---|---|---|---|
| `media` | string\|object | required | — Image or video: a workspace path, an asset id or name, an image or video layer's name, or {asset, time} for a still of a video at time seconds |
| `as` | enum["layer"|"background"|"behind"|"front"|"inside"|"element"|"screen"|"matte"|"replace"|"sprite"|"transition"|"texture"] | optional default="layer" | — The role. layer: a new layer; background: full frame at the bottom (layer: a cutout, goes under it and turns the original off); behind/front: next to layer, same timing; inside: fills layer (text or shape) through its alpha; element: fire/smoke/sparks, see key; screen: pinned onto a tracked plane; matte: the matte of layer; replace: new media in layer, same timing, transform, effects; sprite: the picture of a particle layer; transition: a bridge clip played between the two shots of cut (on V1, later clips move up); texture: the picture painted on a 3D layer (extruded text or shape, a model) |
| `cut` | integer | optional | (min=0) — transition: the cut, counted from 0 (edit list shows them) |
| `layer` | string\|integer | optional | — The target layer (id or name). Needed by behind, front, inside, matte, replace, sprite, screen (the tracked footage, or the base of its '<name> plane tl/tr/br/bl' nulls). Optional for background (a cutout) and element (goes above it) |
| `composition` | string\|integer | optional | — Composition id, name or index (default: the one holding layer, else the first) |
| `at` | number | optional | (min=0) — Start frame (default 0, or the target's start) |
| `duration` | number | optional | (min=1) — Frames shown (default: the clip's length, the target's span, or to the end) |
| `loop` | boolean | optional default=false | — Video: repeat the clip to fill the time |
| `position` | array | optional | — [x, y] px from the centre, +y up |
| `size` | array | optional | — [w, h] px box (sprite: size[0] is the particle size) |
| `fit` | enum["cover"|"contain"|"stretch"] | optional | — How the picture fills size (fit alone: the frame) |
| `parent` | string\|integer | optional | — Layer to parent to, e.g. a tracked null |
| `blend` | string | optional | — Blend mode (element on black: screen) |
| `opacity` | number | optional | (min=0, max=1) — 0..1 |
| `key` | enum["auto"|"black"|"green"|"alpha"] | optional default="auto" | — element: what the picture sits on. black: screen blend; green: chroma key with despill; alpha: as is; auto: reads the corners |
| `matte` | enum["luma"|"alpha"|"luma_inverted"|"alpha_inverted"] | optional default="luma" | — matte: the type |
| `name` | string | optional | — Name of the new layer |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Fill text with a picture

  ```json
  {"media":"photo.png","as":"inside","layer":"Headline"}
  ```

- A picture under everything

  ```json
  {"media":"photo.png","as":"background"}
  ```

- A picture as the particle sprite

  ```json
  {"media":"photo.png","as":"sprite","layer":"Sparks","size":[40,40]}
  ```

- Swap the picture, keep the animation

  ```json
  {"media":"photo.png","as":"replace","layer":"Hero"}
  ```

- A clip lit over the shot (black keyed out)

  ```json
  {"media":"clip.mp4","as":"element","key":"black","position":[0,-200],"loop":true}
  ```

- A picture onto a tracked screen

  ```json
  {"media":"photo.png","as":"screen","layer":"Clip"}
  ```

Tips:

- inside, matte and background only shape the picture: the text or matte source stays at opacity 0, so animate the new layer, not the text.
- element key auto reads the corners; pass key for a vignette or a grey backdrop. A short clip holds its last frame unless loop is true.
- Only role layer keeps a video's sound. {asset, time} takes a still, so a frame of any clip can feed any role.
- transition puts a bridge clip (generateVideo bridge, from the last frame of one shot and the first of the next) into cut n on V1; later clips move up by its length. texture paints a picture on extruded 3D text or shapes (planar across the front, top to top) or a model.

Related: `generateImage`, `generateVideo`, `importAsset`, `cutout`, `track`, `setMatte`, `setParticles`

CLI: `kinetic tool place --params '<json>' --workspace ./out`

## renderVideo

Render a range and encode MP4 (FFmpeg); probes the result (GPU by default). Includes the mixed sound whenever the comp has audio layers (audio defaults to true then).

- family: `render` · mutating: `false` · transport: CLI `kinetic tool renderVideo` / SDK `batch([{tool: "renderVideo"}])` / MCP `tools/call {name: "renderVideo"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `renderVideo {out: string, audio?: bool, backend?: auto|gpu|cpu = "auto", cache?: bool = true, check?: bool, dither?: sierra|floyd_steinberg|bayer|none, end?: int [frame], fps?: int [frames], max_mb?: number [frames], mix_rate?: int = 48000, palette?: scene|global|frame, quality?: draft|standard|web|high|max|master = "high", scale?: number, start?: int = 0 [frame]}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `start` | integer | optional default=0 | — First frame (inclusive, default 0) |
| `end` | integer | optional | — Stop frame, exclusive (default: the composition's duration) |
| `out` | string | required | — .mp4 or .mov; .gif, .webp or .apng make a looping animated image (640 px GIF, 1080 px otherwise; scale overrides; result's image reports size, fps, mb and match) |
| `fps` | integer | optional | — Animated images: frame rate (GIF 15, others up to 30); only those frames render |
| `palette` | enum["scene"|"global"|"frame"] | optional | — GIF colours: one palette per scene (cuts and markers; default), one for all (smallest), or per frame (best colour, ~30% bigger) |
| `dither` | enum["sierra"|"floyd_steinberg"|"bayer"|"none"] | optional | — GIF dithering (default sierra) |
| `backend` | enum["auto"|"gpu"|"cpu"] | optional default="auto" | — auto: GPU if available; cpu: slow exact reference |
| `audio` | boolean | optional | — Mix the comp's audio layers into the MP4 (default: true when it has any) |
| `mix_rate` | integer | optional default=48000 | — Audio mix sample rate when audio=true |
| `cache` | boolean | optional default=true | — Re-export only what changed: the video is kept in 2 s pieces and a piece whose frames did not change since an earlier export (same quality and size) is reused as it is. Pictures are identical to a full export; sound is always mixed fresh. Grain exports render whole. false renders every frame |
| `scale` | number | optional | (min=0.1, max=1) — Render at this fraction of the size (default 1; 0.5 for draft) |
| `check` | boolean | optional | — Then open the exported file: frames decoded from it come back as a labelled sheet, with size, bitrate, audio and loudness (checkExport). Default on in agent sessions |
| `max_mb` | number | optional | — Aim the whole file (sound included) under this many MB, e.g. 25 for chat uploads: x264 from lossless frames, 2-pass if needed; the result's fit reports the size and the measured match. Default from Miran Settings; 0 = no limit |
| `quality` | enum["draft"|"standard"|"web"|"high"|"max"|"master"] | optional default="high" | — draft: fast preview at half size (H.264, motion blur capped at 4 samples). standard: H.264 High, smaller. web: x264 tuned for flat animation, about a third of high's size at 99.5% of its picture (software, slower). high (default): H.264 High at high constant quality, plays everywhere; very dark smooth gradients can band faintly (the result's encoding.tip says when). max: 10-bit HEVC Main10 from full RGB frames; gradients are dithered when rendered, so dark gradients and glows stay band-free. master: ProRes 422 HQ, 10-bit 4:2:2 (needs a .mov out path; ProRes 4444 with alpha when the composition is transparent). All tagged BT.709, hardware-encoded (VideoToolbox) on Apple Silicon with automatic software fallback. |

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

## guide

Concept guides written for agents: conventions and units, layer kinds, typography, motion and easing, design craft, camera, every effect's params, rendering/export. Call with no topic for the list.

- family: `docs` · mutating: `false` · transport: CLI `kinetic tool guide` / SDK `batch([{tool: "guide"}])` / MCP `tools/call {name: "guide"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: yes
- signature: `guide {section?: string, topic?: start|creative|directing|kit|ui|svg|fonts|units|…}`

| param | type | required/default | notes |
|---|---|---|---|
| `topic` | string | optional | — Guide to read (omit to list topics) |
| `section` | string | optional | — Only sections whose heading has this (kit "Charts") |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Where to start

  ```json
  {"topic":"start"}
  ```

- Easing and springs

  ```json
  {"topic":"motion"}
  ```

CLI: `kinetic tool guide --params '<json>' --workspace ./out`

## describeTool

Everything about a tool: full parameter schema with units, worked examples that run as-is on the starter scene, gotchas, and related tools. brief: the one-line signature only; tools: several at once.

- family: `docs` · mutating: `false` · transport: CLI `kinetic tool describeTool` / SDK `batch([{tool: "describeTool"}])` / MCP `tools/call {name: "describeTool"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: yes
- signature: `describeTool {brief?: bool = false, tool?: string, tools?: string[]}`

| param | type | required/default | notes |
|---|---|---|---|
| `tool` | string | optional | — Tool name, e.g. "animateProperty" |
| `tools` | array | optional | — Several tools at once |
| `brief` | boolean | optional default=false | — Signature line (params, types, defaults, units) and purpose only |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Schema, examples, related tools

  ```json
  {"tool":"animateProperty"}
  ```

- Signatures of several

  ```json
  {"tools":["createLayer","setTextStyle","addTextAnimator"],"brief":true}
  ```

CLI: `kinetic tool describeTool --params '<json>' --workspace ./out`

## toolCatalog

Compact map of every tool grouped by family (name, one-line purpose, required params). Start here instead of reading full schemas; then describeTool for one tool, guide for concepts.

- family: `docs` · mutating: `false` · transport: CLI `kinetic tool toolCatalog` / SDK `batch([{tool: "toolCatalog"}])` / MCP `tools/call {name: "toolCatalog"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: yes
- signature: `toolCatalog {family?: string, query?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `family` | string | optional | — Only this family: project, scene, props, content, camera, vector, design, render, assets, footage, edit, vfx, audio, jobs, docs |
| `query` | string | optional | — Case-insensitive search over tool names, descriptions, params, effect ids and kit functions, e.g. "blur", "caption", "export" |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Everything, compact

  ```json
  {}
  ```

- Search

  ```json
  {"query":"blur"}
  ```

CLI: `kinetic tool toolCatalog --params '<json>' --workspace ./out`

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

