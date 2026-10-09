# Measure and look — tool reference (generated)

Source: the runtime registry via `kinetic schemas` — do not hand-edit; run `bun scripts/gen-docs.ts`.
23 of 165 tools — the ones this skill teaches. The full registry lives in docs/TOOLS.md.

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

## critiqueDesign

Static + temporal + (optional) pixel critique.

- family: `design` · mutating: `false` · transport: CLI `kinetic tool critiqueDesign` / SDK `batch([{tool: "critiqueDesign"}])` / MCP `tools/call {name: "critiqueDesign"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.critiqueDesign(...)` in runScript or query · inside runScript: yes
- signature: `critiqueDesign {spec: object, frame?: number [frame]}`

| param | type | required/default | notes |
|---|---|---|---|
| `frame` | number | optional | — Include pixel lens at this frame |
| `spec` | object | required | — DesignSpec from createDesignSpec / generateDesignCandidates |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Critique with pixels

  ```json
  {"spec":$SPEC,"frame":30}
  ```

`$BRIEF` is a design brief (see `guide {topic:"design-pipeline"}`); `$SPEC` is the spec object createDesignSpec returns.

Related: `applyRepair`, `validateFinalDesign`

CLI: `kinetic tool critiqueDesign --params '<json>' --workspace ./out`

## critiquePrimitives

Flags circles/ellipses covering 30%+ of the frame without role=background (the clip-art look). Paths are never flagged.

- family: `design` · mutating: `false` · transport: CLI `kinetic tool critiquePrimitives` / SDK `batch([{tool: "critiquePrimitives"}])` / MCP `tools/call {name: "critiquePrimitives"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.critiquePrimitives(...)` in runScript or query · inside runScript: yes
- signature: `critiquePrimitives {frames?: number[] [frames]}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `frames` | array | optional | — Frames to inspect; default samples across the composition duration |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Scan for clip-art primitives

  ```json
  {"frames":[0,75,149]}
  ```

CLI: `kinetic tool critiquePrimitives --params '<json>' --workspace ./out`

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
| `platform` | enum["reels"|"tiktok"|"shorts"] | optional | — 9:16 app UI zones (top bar, caption block, button column); kit.safe({platform}) gives the clear box |
| `safe` | number\|string\|object | optional default=0.05 | — Margin as a fraction of the frame; or {left, top, right, bottom}; or a platform name |

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
- A 9:16 Reels, TikTok or Shorts teaser: platform: "reels" (or tiktok, shorts) checks against the app's own interface: about the top 220 px, the bottom 420 px and a 140 px column on the right of a 1080x1920 frame for reels, scaled to your size. The result's safe lists the margins in px; kit.safe({platform}) returns the same box to lay out in (guide design, guide kit).

Related: `measureText`, `renderContactSheet`, `inspectMotionActivity`, `reorderLayer`, `critiqueMotion`

CLI: `kinetic tool critiqueLayout --params '<json>' --workspace ./out`

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
- Reveals are not judged: the source of a track matte, a layer that never shows, a plate painted in the colour of what is behind it (a typing wipe's cover), and mask-path or trim-path animation. not_judged in the result names them. For anything else that is meant, pass ignore: [layer names or ids]; a name that is no layer is an error.

Related: `animateProperty`, `setLayerField`, `critiqueLayout`, `inspectMotionActivity`

CLI: `kinetic tool critiqueMotion --params '<json>' --workspace ./out`

## validateFinalDesign

Pass/fail: errors must be zero (frame renders pixel lens).

- family: `design` · mutating: `false` · transport: CLI `kinetic tool validateFinalDesign` / SDK `batch([{tool: "validateFinalDesign"}])` / MCP `tools/call {name: "validateFinalDesign"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.validateFinalDesign(...)` in runScript or query · inside runScript: yes
- signature: `validateFinalDesign {spec: object, frame?: number [frame]}`

| param | type | required/default | notes |
|---|---|---|---|
| `frame` | number | optional | — Frame for the pixel lens (omit for structural checks only) |
| `spec` | object | required | — DesignSpec from createDesignSpec / generateDesignCandidates |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Gate before delivery

  ```json
  {"spec":$SPEC}
  ```

`$BRIEF` is a design brief (see `guide {topic:"design-pipeline"}`); `$SPEC` is the spec object createDesignSpec returns.

CLI: `kinetic tool validateFinalDesign --params '<json>' --workspace ./out`

## compareDesigns

Render two specs at a frame; diff stats.

- family: `design` · mutating: `false` · transport: CLI `kinetic tool compareDesigns` / SDK `batch([{tool: "compareDesigns"}])` / MCP `tools/call {name: "compareDesigns"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.compareDesigns(...)` in runScript or query · inside runScript: yes
- signature: `compareDesigns {spec_a: object, spec_b: object, frame?: number = 0 [frame], out_a?: string, out_b?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `frame` | number | optional default=0 | — Frame to compare (default 0) |
| `out_a` | string | optional | — Optional PNG path for spec_a's frame |
| `out_b` | string | optional | — Optional PNG path for spec_b's frame |
| `spec_a` | object | required | — First DesignSpec |
| `spec_b` | object | required | — Second DesignSpec |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- A/B

  ```json
  {"spec_a":$SPEC,"spec_b":$SPEC,"frame":0}
  ```

`$BRIEF` is a design brief (see `guide {topic:"design-pipeline"}`); `$SPEC` is the spec object createDesignSpec returns.

CLI: `kinetic tool compareDesigns --params '<json>' --workspace ./out`

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

## renderFrame

Render one comp frame to PNG (GPU by default; backend=cpu for the reference).

- family: `render` · mutating: `false` · transport: CLI `kinetic tool renderFrame` / SDK `batch([{tool: "renderFrame"}])` / MCP `tools/call {name: "renderFrame"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.renderFrame(...)` in runScript or query · inside runScript: yes
- signature: `renderFrame {out: string, backend?: auto|gpu|cpu = "auto", frame?: number = 0 [frame], scale?: number}`

| param | type | required/default | notes |
|---|---|---|---|
| `backend` | enum["auto"|"gpu"|"cpu"] | optional default="auto" | — auto: GPU if available; cpu: slow exact reference |
| `composition` | string\|integer | optional | |
| `frame` | number | optional default=0 | — Frame to render (default 0) |
| `out` | string | required | — PNG output path, workspace-relative; the image is returned inline to MCP clients |
| `scale` | number | optional | (min=0.1, max=1) — Fraction of the size for fast previews (0.5 = half) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Look at frame 45

  ```json
  {"frame":45,"out":"frames/f045.png"}
  ```

Related: `critiqueLayout`, `inspectMotionActivity`, `inspectImage`, `renderVideo`

CLI: `kinetic tool renderFrame --params '<json>' --workspace ./out`

## inspectImage

Dims, mean color, alpha coverage of a PNG.

- family: `render` · mutating: `false` · transport: CLI `kinetic tool inspectImage` / SDK `batch([{tool: "inspectImage"}])` / MCP `tools/call {name: "inspectImage"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.inspectImage(...)` in runScript or query · inside runScript: yes
- signature: `inspectImage {path: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `path` | string | required | — PNG path, workspace-relative |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Stats

  ```json
  {"path":"photo.png"}
  ```

CLI: `kinetic tool inspectImage --params '<json>' --workspace ./out`

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

## inspectCameraCoverage

Project focal layers over [start, end]; report bounds + off-frame violations.

- family: `camera` · mutating: `false` · transport: CLI `kinetic tool inspectCameraCoverage` / SDK `batch([{tool: "inspectCameraCoverage"}])` / MCP `tools/call {name: "inspectCameraCoverage"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.inspectCameraCoverage(...)` in runScript or query · inside runScript: yes
- signature: `inspectCameraCoverage {end: number [frame], start: number [frame], layers?: string|int[], step?: number = 1 [frames]}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `end` | number | required | — End frame |
| `layers` | array | optional | — Focal layers that must stay in frame (default: every visual layer) |
| `start` | number | required | — Start frame |
| `step` | number | optional default=1 | — Sample every `step` frames (default 1) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Does the content stay in frame?

  ```json
  {"layers":["Card","Headline"],"start":0,"end":120,"step":10}
  ```

CLI: `kinetic tool inspectCameraCoverage --params '<json>' --workspace ./out`

## validateCameraMove

Validate camera motion: flips, spikes, margins, focal jumps, discontinuities, conflicts, story order. Optional profile: restrained/cinematic/energetic/handheld (legacy thresholds when absent).

- family: `camera` · mutating: `false` · transport: CLI `kinetic tool validateCameraMove` / SDK `batch([{tool: "validateCameraMove"}])` / MCP `tools/call {name: "validateCameraMove"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.validateCameraMove(...)` in runScript or query · inside runScript: yes
- signature: `validateCameraMove {end: number [frame], start: number [frame], profile?: restrained|cinematic|energetic|handheld|legacy, step?: number = 1 [frames], story_event_frame?: number [frame], story_event_name?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `end` | number | required | — End frame |
| `profile` | enum["restrained"|"cinematic"|"energetic"|"handheld"|"legacy"] | optional | — Motion-style thresholds: restrained, cinematic, energetic, handheld (omit for the legacy defaults) |
| `start` | number | required | — Start frame |
| `step` | number | optional default=1 | — Sampling step in frames |
| `story_event_frame` | number | optional | — Frame of the story beat the move must support (checks order) |
| `story_event_name` | string | optional | — Label for that story beat in the report |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Check for spikes and flips

  ```json
  {"start":0,"end":150,"profile":"cinematic"}
  ```

CLI: `kinetic tool validateCameraMove --params '<json>' --workspace ./out`

## compareSvgFidelity

resvg reference vs native source layer/block render + PASS/FAIL metrics.

- family: `vector` · mutating: `false` · transport: CLI `kinetic tool compareSvgFidelity` / SDK `batch([{tool: "compareSvgFidelity"}])` / MCP `tools/call {name: "compareSvgFidelity"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.compareSvgFidelity(...)` in runScript or query · inside runScript: yes
- signature: `compareSvgFidelity {layer: string|int, frame?: number = 0 [frame], height?: int [px], tolerance?: int = 2, width?: int [px]}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `frame` | number | optional default=0 | — Frame to compare (default 0) |
| `height` | integer | optional | — Comparison raster height in px (default: document size) |
| `layer` | string\|integer | required | |
| `tolerance` | integer | optional default=2 | — Per-channel tolerance 0..255 (default 2) |
| `width` | integer | optional | — Comparison raster width in px (default: document size) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Check the import against resvg

  ```json
  {"layer":"Logo"}
  ```

CLI: `kinetic tool compareSvgFidelity --params '<json>' --workspace ./out`

## validateProtectedSettle

Verify protected SVG artwork or a split source block at its settle frame (pixels + node count + alpha bbox).

- family: `vector` · mutating: `false` · transport: CLI `kinetic tool validateProtectedSettle` / SDK `batch([{tool: "validateProtectedSettle"}])` / MCP `tools/call {name: "validateProtectedSettle"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.validateProtectedSettle(...)` in runScript or query · inside runScript: yes
- signature: `validateProtectedSettle {layer: string|int, height?: int [px], tolerance?: int = 2, width?: int [px]}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `height` | integer | optional | — Comparison raster height in px (default: document size) |
| `layer` | string\|integer | required | |
| `tolerance` | integer | optional default=2 | — Per-channel tolerance 0..255 (default 2) |
| `width` | integer | optional | — Comparison raster width in px (default: document size) |
CLI: `kinetic tool validateProtectedSettle --params '<json>' --workspace ./out`

## validateProject

Run a validation profile over the project.

- family: `project` · mutating: `false` · transport: CLI `kinetic tool validateProject` / SDK `batch([{tool: "validateProject"}])` / MCP `tools/call {name: "validateProject"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.validateProject(...)` in runScript or query · inside runScript: yes
- signature: `validateProject {profile?: structural|render-readiness|portability|performance|final-output|all = "all"}`

| param | type | required/default | notes |
|---|---|---|---|
| `profile` | enum["structural"|"render-readiness"|"portability"|"performance"|"final-output"|"all"] | optional default="all" | — structural, render-readiness, portability, performance, final-output, or all (default) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Pre-render check

  ```json
  {"profile":"render-readiness"}
  ```

CLI: `kinetic tool validateProject --params '<json>' --workspace ./out`

## inspectProject

Summarize the project (comps, layers, assets, hashes).

- family: `project` · mutating: `false` · transport: CLI `kinetic tool inspectProject` / SDK `batch([{tool: "inspectProject"}])` / MCP `tools/call {name: "inspectProject"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.inspectProject(...)` in runScript or query · inside runScript: yes
- signature: `inspectProject {}`

No parameters.

Example: `{}`

CLI: `kinetic tool inspectProject --params '<json>' --workspace ./out`

## historyLog

History entries, checkpoints, and journal.

- family: `project` · mutating: `false` · transport: CLI `kinetic tool historyLog` / SDK `batch([{tool: "historyLog"}])` / MCP `tools/call {name: "historyLog"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.historyLog(...)` in runScript or query · inside runScript: yes
- signature: `historyLog {}`

No parameters.

Example: `{}`

CLI: `kinetic tool historyLog --params '<json>' --workspace ./out`

## motionSummary

Channels + drivers per layer (curve metadata, not pixel proof).

- family: `props` · mutating: `false` · transport: CLI `kinetic tool motionSummary` / SDK `batch([{tool: "motionSummary"}])` / MCP `tools/call {name: "motionSummary"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.motionSummary(...)` in runScript or query · inside runScript: yes
- signature: `motionSummary {layer?: string|int}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | optional | — Optional single layer |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- All motion on a layer

  ```json
  {"layer":"Headline"}
  ```

CLI: `kinetic tool motionSummary --params '<json>' --workspace ./out`

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

## inspectTextCoverage

Glyph coverage for a text layer or an SVG layer's <text>, or every one when layer is omitted: requested family, families used, missing chars, substitution flag (same shaper the renderers use).

- family: `content` · mutating: `false` · transport: CLI `kinetic tool inspectTextCoverage` / SDK `batch([{tool: "inspectTextCoverage"}])` / MCP `tools/call {name: "inspectTextCoverage"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.inspectTextCoverage(...)` in runScript or query · inside runScript: yes
- signature: `inspectTextCoverage {layer?: string|int}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | optional | — Text layer id or name; omit to check every text layer in the composition |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Every text layer at once

  ```json
  {}
  ```

- One layer

  ```json
  {"layer":"Headline"}
  ```

CLI: `kinetic tool inspectTextCoverage --params '<json>' --workspace ./out`

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

