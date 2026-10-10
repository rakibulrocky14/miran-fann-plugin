# Making a video end to end — tool reference (generated)

Source: the runtime registry via `kinetic schemas` — do not hand-edit; run `bun scripts/gen-docs.ts`.
24 of 165 tools — the ones this skill teaches. The full registry lives in docs/TOOLS.md.

## runScript

Run JavaScript that calls any tool as miran.<toolName>(params) with loops, math and each call's result (ids, sizes) - build or animate a whole scene or beat in ONE call instead of dozens. Atomic: an error rolls everything back; success is one undo step. Rendered PNGs come back as images. See guide {topic:"scripting"}.

- family: `script` · mutating: `true` · transport: CLI `kinetic tool runScript` / SDK `batch([{tool: "runScript"}])` / MCP `tools/call {name: "runScript"}`
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: none (scripts do not nest)
- signature: `runScript {source: string, dry_run?: bool = false, images?: bool = true, label?: string, strict?: bool, timeout_seconds?: number = 45, verbose?: bool = false}`

| param | type | required/default | notes |
|---|---|---|---|
| `dry_run` | boolean | optional default=false | — Run everything, report the result, then roll the project back (test a script without keeping it) |
| `images` | boolean | optional default=true | — false: PNGs the script renders stay files (listed under files), not inline images |
| `label` | string | optional | — Name for the undo step, e.g. "beat 2: stats reveal" (default: first line of the script) |
| `source` | string | required | — JavaScript function body. Call tools as miran.createLayer({...}) (returns the tool's result, throws on error with .code and .hint); kit.* has ready-made motion-design pieces (kit.title, kit.words, kit.counter, kit.line, kit.ring, kit.card, kit.bars, kit.cursor, kit.wipe, kit.finish, ...; guide {topic:"kit"}); log(...) collects debug lines; range(n), lerp(a,b,t), clamp(x,lo,hi) are available; `return` a value to get it back. No file/network access. |
| `strict` | boolean | optional | — A misspelled param fails the call (and rolls back) instead of being ignored (default true) |
| `timeout_seconds` | number | optional default=45 | (min=1, max=600) — Wall-clock budget (max 50 over MCP so the call returns before the host times out) |
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
| `images` | boolean | optional default=true | — false: rendered PNGs stay files (listed under files) |
| `source` | string | required | — JavaScript function body, as in runScript: miran.<tool>(params) for read-only tools, kit helpers that only look, log(...), `return` a value |
| `strict` | boolean | optional | — Misspelled params fail instead of being ignored (default true) |
| `timeout_seconds` | number | optional default=45 | (min=1, max=600) — Wall-clock budget (max 50 over MCP) |
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
| `composition` | string\|integer | optional | — Open on this composition (id or name) |
| `frame` | number | optional | (min=0) — Open at this frame (link end #frame=N seeks) |
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
| `asset` | string | optional | — Asset id |
| `calls` | array | optional | — edit: the person's tool calls |
| `comp` | string | optional | — Composition id |
| `frame` | number | optional | — Frame |
| `k` | string | optional | — Frame content key |
| `layer` | string\|integer | optional | — note: selected layer; curve: its layer |
| `op` | enum["state"|"values"|"boxes"|"curve"|"wave"|"audio"|"thumb"|"frame"|"edit"|"note"] | required | — Panel request |
| `path` | string | optional | — curve: property |
| `quality` | enum["draft"|"full"] | optional | — Preview quality |
| `rate` | integer | optional | (min=8000, max=48000) — audio: sample rate of the mix |
| `since` | integer | optional | (min=0) — state: version held |
| `text` | string | optional | — note text |
| `to` | number | optional | — curve: last frame (from = frame) |
| `urgent` | boolean | optional | — false: prefetch |
| `v` | integer | optional | (min=0) — Project version shown |
| `width` | integer | optional | (min=64, max=3840) — Pixels |
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
| `backend` | enum["auto"|"gpu"|"cpu"] | optional default="auto" | — auto: GPU if available; cpu: slow exact reference |
| `columns` | integer | optional default=4 | (min=1, max=64) — Sheet columns, 1..=64 (the sheet buffer is frame_w*columns by frame_h*rows) |
| `composition` | string\|integer | optional | |
| `frames` | array\|string\|integer | required | — Frames to show: a list, a count (spread evenly), or "auto": each scene's middle, both sides of every cut and the last frame. Each cell is labelled with its frame and time |
| `ignore` | array\|string\|integer | optional | — Layers whose motion is intended (names or ids) |
| `max_width` | integer | optional default=2400 | — Shrink the sheet to at most this many px wide so it comes back inline (0 = full size) |
| `out` | string | required | — PNG output path; the sheet is returned inline to MCP clients |
| `platform` | enum["reels"|"tiktok"|"shorts"] | optional | — 9:16 app UI zones for the layout list |
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

## inspectMotionActivity

Measure how much the picture moves over time: renders the range and reports, per second, the share of the frame that visibly changes, plus still holds, a weak opening and a frozen ending as findings. Run it on a draft before exporting.

- family: `design` · mutating: `false` · transport: CLI `kinetic tool inspectMotionActivity` / SDK `batch([{tool: "inspectMotionActivity"}])` / MCP `tools/call {name: "inspectMotionActivity"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `inspectMotionActivity {backend?: auto|gpu|cpu = "auto", end?: int [frame], start?: int = 0 [frame], step?: int = 1 [frame]}`

| param | type | required/default | notes |
|---|---|---|---|
| `backend` | enum["auto"|"gpu"|"cpu"] | optional default="auto" | — auto (default) renders on the GPU when an adapter exists and falls back to the CPU reference otherwise |
| `composition` | string\|integer | optional | |
| `end` | integer | optional | — End frame, exclusive (default: the composition duration) |
| `start` | integer | optional default=0 | — First frame (default 0) |
| `step` | integer | optional default=1 | (min=1) — Sample every Nth frame (1 = every frame; 2 halves the cost on long pieces) |

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
- signature: `addMusic {bpm?: number, drop?: number [frame], drums?: bool, duck_db?: number = -9, end?: number, ending?: number [frame], energy?: number, gain_db?: number = 0 [dB], hits?: number[] [frames], key?: string, mood?: tech|corporate|uplifting|epic|hype|chill|ambient|dark|… = "tech", name?: string = "Music", seed?: int, start?: number = 0 [frame], stop?: bool}`

| param | type | required/default | notes |
|---|---|---|---|
| `bpm` | number | optional | — Tempo (mood default) |
| `composition` | string\|integer | optional | |
| `drop` | number | optional | — Frame the full beat starts: the reveal (snaps to the 16th-note grid) |
| `drums` | boolean | optional | — false: no beat |
| `duck_db` | number | optional default=-9 | — Dip under voiceover lines; 0 off |
| `end` | number | optional | — Default: comp end |
| `ending` | number | optional | — Frame of the final chord |
| `energy` | number | optional | — 0..1 |
| `gain_db` | number | optional default=0 | — dB on top of the mix-ready level |
| `hits` | array | optional | — Accent frames |
| `key` | string | optional | — e.g. D minor |
| `mood` | enum["tech"|"corporate"|"uplifting"|"epic"|"hype"|"chill"|"ambient"|"dark"|"playful"|"minimal"|"luxury"] | optional default="tech" | — Style (guide sound) |
| `name` | string | optional default="Music" | — Display name |
| `seed` | integer | optional | — Another arrangement |
| `start` | number | optional default=0 | — Start frame |
| `stop` | boolean | optional | — Dead stop (~0.4 s) before the drop so it lands (default on for hype, epic, dark) |

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
| `align` | enum["hit"|"start"] | optional default="hit" | — hit: sync point on frame; start |
| `auto` | boolean | optional | — Sounds for the motion already there (whooshes, pops, slams, typing, ticks), in from..to |
| `composition` | string\|integer | optional | |
| `count` | integer | optional | — Events (typing, ticks; honks, bites, gulps, scratch strokes) |
| `duration_frames` | number | optional | — Length (presets) |
| `for` | string\|integer\|array | optional | — Layer(s) this sound scores: deleting the last of them deletes the sound |
| `frame` | number | optional default=0 | — Frame the hit lands on |
| `from` | number | optional | — auto: first frame |
| `gain_db` | number | optional default=0 | — dB on top of the mix-ready level |
| `name` | string | optional | — Display name |
| `note` | string | optional | — Root note, e.g. D2 |
| `out` | string | optional | — WAV path |
| `pan` | number | optional default=0 | — -1 left .. 1 right |
| `patch` | object | optional | — Own synth patch {voices:[{osc,note|freq,env,filter,...}],fx} (guide sound) |
| `pitch` | number | optional | — Semitones |
| `place` | boolean | optional default=true | — false: only render the file (returns the preset patch to edit) |
| `sample_rate` | integer | optional default=48000 | — Hz of samples |
| `samples` | array | optional | — Raw audio: [s...] or [[left...],[right...]], -1..1 |
| `seed` | integer | optional | — Variation |
| `sound` | string | optional | — Preset name |
| `to` | number | optional | — auto: end frame |
| `tone` | number | optional | — -1 dark .. 1 bright |

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
| `composition` | string\|integer | optional | |
| `extend` | boolean | optional default=true | — Lengthen the comp to fit |
| `file` | string | optional | — transcribe: audio file |
| `find` | string | optional | — words: word or phrase |
| `gain_db` | number | optional default=0 | — Level nudge |
| `gap` | number | optional default=0.25 | — Seconds between lines |
| `layer` | string\|integer | optional | — transcribe / words: audio layer |
| `locale` | string | optional default="en-US" | — transcribe: language |
| `model` | string | optional | — Cloud model (default newest) |
| `name` | string | optional default="VO" | — Layer prefix |
| `pronounce` | object | optional | — Word → "MEER-un" or /phonemes/ (saved) |
| `provider` | enum["kokoro"|"gemini"|"elevenlabs"] | optional | — Cloud only when asked |
| `script` | string | optional | — "NAME: line" lines; blank line = pause; [tags] <short pause> |
| `speed` | number | optional default=1 | — 0.5..2 |
| `start_frame` | number | optional default=0 | — First line's frame |
| `style` | string\|object | optional | — Cloud delivery, or per speaker |
| `text` | string | optional | — sample / pronounce: words |
| `voice` | string | optional | — af_heart, am_michael, bf_emma... or gemini:Kore, elevenlabs:<name> |
| `voices` | object | optional | — Speaker → voice |

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
| `at` | array | optional | — cast: where its feet stand (px, y up) |
| `audio` | string\|integer\|array | optional | — lipsync: voice layer(s); default every line whose speaker is the character |
| `character` | string | optional | — The name given to importSvg (its layers are <character>/<part>) |
| `composition` | string\|integer | optional | |
| `frame` | number | optional default=0 | — rig: the rest pose's frame; parts: where pivots are |
| `height` | number | optional default=600 | — cast: px tall |
| `starter` | enum["ava"|"ben"|"kit"|"pip"] | optional | — cast: a starter character to import and rig |

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

Learn a reference video's style: cuts and shot lengths, transitions, motion and camera, palette and contrast, type (read on device). compare: what differs in your render, worst first, with numbers. guide reference.

- family: `design` · mutating: `false` · transport: CLI `kinetic tool reference` / SDK `batch([{tool: "reference"}])` / MCP `tools/call {name: "reference"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `reference {video: string, action?: analyze|compare = "analyze", against?: string, read_text?: bool = true}`

| param | type | required/default | notes |
|---|---|---|---|
| `action` | enum["analyze"|"compare"] | optional default="analyze" | — What to do |
| `against` | string | optional | — compare: your video (default: a draft render of the composition) |
| `composition` | string\|integer | optional | — compare: what to render when `against` is not given |
| `read_text` | boolean | optional default=true | — Read the type on screen (slower) |
| `video` | string | required | — The reference video (in the workspace) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Learn a reference video's style

  ```json
  {"video":"ref.mp4"}
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

## importHtml

Lay out a screen written as HTML + CSS in a local Chrome and import it: each element with an id (or data-part) becomes its own layer where the page put it, over a base layer. Real-looking product UI for kit.screen3d, kit.frame and kit.cursor (guide ui).

- family: `assets` · mutating: `true` · transport: CLI `kinetic tool importHtml` / SDK `batch([{tool: "importHtml"}])` / MCP `tools/call {name: "importHtml"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.importHtml(...)` in runScript · inside runScript: yes
- signature: `importHtml {at?: number[], dark?: bool, height?: number [px], html?: string, name?: string, parts?: bool = true, path?: string, scale?: number [px], width?: number [px]}`

| param | type | required/default | notes |
|---|---|---|---|
| `at` | array | optional | — [x, y] of the page centre (default [0, 0]) |
| `composition` | string\|integer | optional | |
| `dark` | boolean | optional | — Dark mode |
| `height` | number | optional | — CSS px (default 900) |
| `html` | string | optional | — A fragment or a whole document; give the parts that move an id |
| `name` | string | optional | — Layer name prefix (default "Screen") |
| `parts` | boolean | optional default=true | — false: one flat image of the page |
| `path` | string | optional | — Instead of html: a workspace .html file |
| `scale` | number | optional | (min=1, max=4) — Pixels per CSS px (2; 3 for deep pushes) |
| `width` | number | optional | — CSS px (default 1440) |

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
| `at` | array | optional | — [x, y] of its centre |
| `composition` | string\|integer | optional | |
| `dark` | boolean | optional | — Dark mode |
| `fill` | number | optional | — Share of the frame width (0.8) |
| `height` | number | optional | — CSS px (900) |
| `name` | string | optional | — Default the host name |
| `parts` | boolean | optional default=false | — Boxes as layers |
| `place` | boolean | optional default=false | — Add it as a layer; boxes in comp px |
| `scale` | number | optional | (min=1, max=3) — Pixels per CSS px (2) |
| `scroll` | number | optional | — CSS px to scroll first |
| `url` | string | required | — A public http(s) page |
| `width` | number | optional | — CSS px (1440) |
CLI: `kinetic tool captureSite --params '<json>' --workspace ./out`

## generateImage

Make an image with the user's OpenRouter key (Miran Settings): a plate, an illustration, a texture. Saved with its prompt; the same call reuses it. svg: true traces it to animate. Never words, logos or UI.

- family: `assets` · mutating: `false` · transport: CLI `kinetic tool generateImage` / SDK `batch([{tool: "generateImage"}])` / MCP `tools/call {name: "generateImage"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `generateImage {prompt: string, aspect?: string, fresh?: bool, model?: string, name?: string, reference?: string|array, size?: 1K|2K|4K, svg?: boolean|string, transparent?: bool}`

| param | type | required/default | notes |
|---|---|---|---|
| `aspect` | string | optional | — "16:9", "9:16", "1:1"... (the composition's) |
| `fresh` | boolean | optional | — New image, not the saved one |
| `model` | string | optional | — OpenRouter model id (this call) |
| `name` | string | optional | — File name |
| `prompt` | string | required | — Subject in the film's look (palette, medium, light) |
| `reference` | string\|array | optional | — Image path(s) to edit or match |
| `size` | enum["1K"|"2K"|"4K"] | optional | — Resolution (2K) |
| `svg` | boolean\|string | optional | — Also an SVG: true, "flat", "detailed", "lineart" |
| `transparent` | boolean | optional | — Cut-out background where offered |

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

## renderVideo

Render a range and encode MP4 (FFmpeg); probes the result (GPU by default). Includes the mixed sound whenever the comp has audio layers (audio defaults to true then).

- family: `render` · mutating: `false` · transport: CLI `kinetic tool renderVideo` / SDK `batch([{tool: "renderVideo"}])` / MCP `tools/call {name: "renderVideo"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `renderVideo {out: string, audio?: bool, backend?: auto|gpu|cpu = "auto", cache?: bool = true, check?: bool, dither?: sierra|floyd_steinberg|bayer|none, end?: int [frame], fps?: int [frames], max_mb?: number [frames], mix_rate?: int = 48000, palette?: scene|global|frame, quality?: draft|standard|web|high|max|master = "high", scale?: number, start?: int = 0 [frame]}`

| param | type | required/default | notes |
|---|---|---|---|
| `audio` | boolean | optional | — Mix the comp's audio layers into the MP4 (default: true when it has any) |
| `backend` | enum["auto"|"gpu"|"cpu"] | optional default="auto" | — auto: GPU if available; cpu: slow exact reference |
| `cache` | boolean | optional default=true | — Re-export only what changed: the video is kept in 2 s pieces and a piece whose frames did not change since an earlier export (same quality and size) is reused as it is. Pictures are identical to a full export; sound is always mixed fresh. Grain exports render whole. false renders every frame |
| `check` | boolean | optional | — Then open the exported file: frames decoded from it come back as a labelled sheet, with size, bitrate, audio and loudness (checkExport). Default on in agent sessions |
| `composition` | string\|integer | optional | |
| `dither` | enum["sierra"|"floyd_steinberg"|"bayer"|"none"] | optional | — GIF dithering (default sierra) |
| `end` | integer | optional | — Stop frame, exclusive (default: the composition's duration) |
| `fps` | integer | optional | — Animated images: frame rate (GIF 15, others up to 30); only those frames render |
| `max_mb` | number | optional | — Aim the whole file (sound included) under this many MB, e.g. 25 for chat uploads: x264 from lossless frames, 2-pass if needed; the result's fit reports the size and the measured match. Default from Miran Settings; 0 = no limit |
| `mix_rate` | integer | optional default=48000 | — Audio mix sample rate when audio=true |
| `out` | string | required | — .mp4 or .mov; .gif, .webp or .apng make a looping animated image (640 px GIF, 1080 px otherwise; scale overrides; result's image reports size, fps, mb and match) |
| `palette` | enum["scene"|"global"|"frame"] | optional | — GIF colours: one palette per scene (cuts and markers; default), one for all (smallest), or per frame (best colour, ~30% bigger) |
| `quality` | enum["draft"|"standard"|"web"|"high"|"max"|"master"] | optional default="high" | — draft: fast preview at half size (H.264, motion blur capped at 4 samples). standard: H.264 High, smaller. web: x264 tuned for flat animation, about a third of high's size at 99.5% of its picture (software, slower). high (default): H.264 High at high constant quality, plays everywhere; very dark smooth gradients can band faintly (the result's encoding.tip says when). max: 10-bit HEVC Main10 from full RGB frames; gradients are dithered when rendered, so dark gradients and glows stay band-free. master: ProRes 422 HQ, 10-bit 4:2:2 (needs a .mov out path; ProRes 4444 with alpha when the composition is transparent). All tagged BT.709, hardware-encoded (VideoToolbox) on Apple Silicon with automatic software fallback. |
| `scale` | number | optional | (min=0.1, max=1) — Render at this fraction of the size (default 1; 0.5 for draft) |
| `start` | integer | optional default=0 | — First frame (inclusive, default 0) |

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

## checkExport

Open an exported video (or GIF) and check it: frames decoded from the file as a labelled sheet (returned as an image), duration, size, bitrate, fps, audio and loudness, with problems named. renderVideo runs this on every export.

- family: `render` · mutating: `false` · transport: CLI `kinetic tool checkExport` / SDK `batch([{tool: "checkExport"}])` / MCP `tools/call {name: "checkExport"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.checkExport(...)` in runScript or query · inside runScript: yes
- signature: `checkExport {path: string, max_mb?: number}`

| param | type | required/default | notes |
|---|---|---|---|
| `max_mb` | number | optional | — The size it had to fit, in MB |
| `path` | string | required | — The file, workspace-relative (renderVideo's out) |
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
| `section` | string | optional | — Only sections whose heading has this (kit "Charts") |
| `topic` | string | optional | — Guide to read (omit to list topics) |

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
| `brief` | boolean | optional default=false | — Signature line (params, types, defaults, units) and purpose only |
| `tool` | string | optional | — Tool name, e.g. "animateProperty" |
| `tools` | array | optional | — Several tools at once |

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
| `family` | string | optional | — Only this family: project, scene, props, content, camera, vector, design, render, assets, audio, jobs, docs |
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
| `film` | string | optional | — What you made |
| `issues` | string | required | — A short plain paragraph |
CLI: `kinetic tool feedback --params '<json>' --workspace ./out`

