# Exporting — tool reference (generated)

Source: the runtime registry via `kinetic schemas` — do not hand-edit; run `bun scripts/gen-docs.ts`.
27 of 165 tools — the ones this skill teaches. The full registry lives in docs/TOOLS.md.

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

## renderRange

Render frames start..end (exclusive) as PNG sequence (GPU by default).

- family: `render` · mutating: `false` · transport: CLI `kinetic tool renderRange` / SDK `batch([{tool: "renderRange"}])` / MCP `tools/call {name: "renderRange"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `renderRange {dir: string, end: int [frames], backend?: auto|gpu|cpu = "auto", scale?: number, start?: int = 0 [frame]}`

| param | type | required/default | notes |
|---|---|---|---|
| `backend` | enum["auto"|"gpu"|"cpu"] | optional default="auto" | — auto: GPU if available; cpu: slow exact reference |
| `composition` | string\|integer | optional | |
| `dir` | string | required | — Output folder for frame_NNNNN.png files, workspace-relative |
| `end` | integer | required | — Stop frame, exclusive (end=90 renders frames 0..89); at most 3600 frames per call |
| `scale` | number | optional | (min=0.1, max=1) — Fraction of the size for fast previews (0.5 = half) |
| `start` | integer | optional default=0 | — First frame (inclusive, default 0) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- PNG sequence of the first second

  ```json
  {"start":0,"end":30,"dir":"seq"}
  ```

CLI: `kinetic tool renderRange --params '<json>' --workspace ./out`

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

## renderVideoWithAudio

Render a range, mix composition audio, mux into the MP4, and verify. Fails loudly without ffmpeg; stays silent only when the comp has no audio layers.

- family: `audio` · mutating: `false` · transport: CLI `kinetic tool renderVideoWithAudio` / SDK `batch([{tool: "renderVideoWithAudio"}])` / MCP `tools/call {name: "renderVideoWithAudio"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `renderVideoWithAudio {out: string, backend?: auto|gpu|cpu = "auto", cache?: bool = true, end?: int [frame], max_mb?: number, mix_rate?: int = 48000 [Hz], quality?: draft|standard|web|high|max|master = "high", scale?: number, start?: int = 0 [frame]}`

| param | type | required/default | notes |
|---|---|---|---|
| `backend` | enum["auto"|"gpu"|"cpu"] | optional default="auto" | — auto: GPU if available; cpu: slow exact reference |
| `cache` | boolean | optional default=true | — See renderVideo.cache |
| `composition` | string\|integer | optional | |
| `end` | integer | optional | — Stop frame, exclusive (default: the composition's duration) |
| `max_mb` | number | optional | — Size limit; see renderVideo.max_mb |
| `mix_rate` | integer | optional default=48000 | — Audio sample rate in Hz (default 48000) |
| `out` | string | required | — Path, workspace-relative: .mp4 (or .mov, required for quality=master); .gif, .webp or .apng make a looping animated image |
| `quality` | enum["draft"|"standard"|"web"|"high"|"max"|"master"] | optional default="high" | — Delivery preset; see renderVideo.quality |
| `scale` | number | optional | (min=0.1, max=1) — Render at this fraction of the size (default 1; 0.5 for draft) |
| `start` | integer | optional default=0 | — First frame (inclusive, default 0) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- MP4 with the mixed soundtrack

  ```json
  {"end":60,"out":"renders/with-audio.mp4"}
  ```

Related: `jobStatus`, `renderContactSheet`, `exportVersions`, `validateProject`

CLI: `kinetic tool renderVideoWithAudio --params '<json>' --workspace ./out`

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

## exportVersions

Other aspect versions of a composition (16:9, 9:16, 1:1, 4:5...), built and exported in one call: content refits (side-by-side groups stack in portrait), backgrounds fill, animations stay as they are; each version's layout is checked. Versions are compositions you can tweak and export again.

- family: `render` · mutating: `true` · transport: CLI `kinetic tool exportVersions` / SDK `batch([{tool: "exportVersions"}])` / MCP `tools/call {name: "exportVersions"}`
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `exportVersions {aspects?: string[], export?: object, out?: string, rebuild?: bool = false, render?: bool = true}`

| param | type | required/default | notes |
|---|---|---|---|
| `aspects` | array | optional | — "W:H" each, default ["16:9","9:16","1:1"]; the short edge stays the source's |
| `composition` | string\|integer | optional | |
| `export` | object | optional | — renderVideo params for every version (quality, max_mb, backend, audio, fps...) |
| `out` | string | optional | — Base path, e.g. renders/final.mp4 -> renders/final-9x16.mp4 (.gif/.webp/.apng too) |
| `rebuild` | boolean | optional default=false | — Build again from the source a version that already exists (it keeps your edits otherwise) |
| `render` | boolean | optional default=true | — false only builds the versions |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Vertical and square versions of the film

  ```json
  {"aspects":["16:9","9:16","1:1"],"out":"renders/final.mp4"}
  ```

- Build a 4:5 version to adjust before exporting

  ```json
  {"aspects":["4:5"],"render":false}
  ```

Tips:

- Each version is a composition (<id>_9x16...) with Reframe nulls keyed per scene; edit it like any other and call exportVersions again (built versions are kept unless rebuild:true).
- Read layout.new_issues per version: what the reframe broke (tiny text in a small square is common; enlarge it there).

Related: `renderContactSheet`, `critiqueLayout`, `renderVideo`, `jobStatus`

CLI: `kinetic tool exportVersions --params '<json>' --workspace ./out`

## benchmarkRender

Time evaluation + render over N frames (GPU by default).

- family: `render` · mutating: `false` · transport: CLI `kinetic tool benchmarkRender` / SDK `batch([{tool: "benchmarkRender"}])` / MCP `tools/call {name: "benchmarkRender"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `benchmarkRender {backend?: auto|gpu|cpu = "auto", frames?: int = 8 [frames], repeat?: int = 1}`

| param | type | required/default | notes |
|---|---|---|---|
| `backend` | enum["auto"|"gpu"|"cpu"] | optional default="auto" | — auto: GPU if available; cpu: slow exact reference |
| `composition` | string\|integer | optional | |
| `frames` | integer | optional default=8 | — Frames to time (default 8) |
| `repeat` | integer | optional default=1 | — Timing passes (default 1) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Time 8 frames

  ```json
  {"frames":8}
  ```

CLI: `kinetic tool benchmarkRender --params '<json>' --workspace ./out`

## listRenderCapabilities

CPU+GPU caps, effects, engine version, adapter, encoder (honest).

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

## cancelJob

Cancel a running background job; its partial output is deleted.

- family: `jobs` · mutating: `false` · transport: CLI `kinetic tool cancelJob` / SDK `batch([{tool: "cancelJob"}])` / MCP `tools/call {name: "cancelJob"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `cancelJob {job: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `job` | string | required | — Job id to cancel |
Related: `renderVideo`, `renderRange`

CLI: `kinetic tool cancelJob --params '<json>' --workspace ./out`

## listJobs

List this session's background jobs with status and progress.

- family: `jobs` · mutating: `false` · transport: CLI `kinetic tool listJobs` / SDK `batch([{tool: "listJobs"}])` / MCP `tools/call {name: "listJobs"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.listJobs(...)` in runScript or query · inside runScript: yes
- signature: `listJobs {}`

No parameters.

Example: `{}`

Related: `renderVideo`, `renderRange`

CLI: `kinetic tool listJobs --params '<json>' --workspace ./out`

## inspectGpuBackend

Request a wgpu adapter; report it or fail honestly.

- family: `render` · mutating: `false` · transport: CLI `kinetic tool inspectGpuBackend` / SDK `batch([{tool: "inspectGpuBackend"}])` / MCP `tools/call {name: "inspectGpuBackend"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.inspectGpuBackend(...)` in runScript or query · inside runScript: yes
- signature: `inspectGpuBackend {}`

No parameters.

Example: `{}`

CLI: `kinetic tool inspectGpuBackend --params '<json>' --workspace ./out`

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

## decodeAudio

Decode an audio asset/file to memory; report rate, channels, duration, peak. Optional out= writes the decoded WAV.

- family: `audio` · mutating: `false` · transport: CLI `kinetic tool decodeAudio` / SDK `batch([{tool: "decodeAudio"}])` / MCP `tools/call {name: "decodeAudio"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.decodeAudio(...)` in runScript or query · inside runScript: yes
- signature: `decodeAudio {asset?: string, out?: string, path?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `asset` | string | optional | — Audio asset id (or give path instead) |
| `out` | string | optional | — Optional workspace-relative WAV output |
| `path` | string | optional | — Workspace-relative audio file (or give asset instead) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Decode and report

  ```json
  {"asset":"music"}
  ```

CLI: `kinetic tool decodeAudio --params '<json>' --workspace ./out`

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

## analyzeWaveform

Min/max peaks + RMS envelope buckets for an audio asset/file.

- family: `audio` · mutating: `false` · transport: CLI `kinetic tool analyzeWaveform` / SDK `batch([{tool: "analyzeWaveform"}])` / MCP `tools/call {name: "analyzeWaveform"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.analyzeWaveform(...)` in runScript or query · inside runScript: yes
- signature: `analyzeWaveform {asset?: string, buckets?: int = 128, path?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `asset` | string | optional | — Audio asset id (or give path instead) |
| `buckets` | integer | optional default=128 | (min=8, max=4096) — Number of envelope buckets across the file (default 128) |
| `path` | string | optional | — Workspace-relative audio file (or give asset instead) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Envelope

  ```json
  {"asset":"music","buckets":64}
  ```

CLI: `kinetic tool analyzeWaveform --params '<json>' --workspace ./out`

## detectBeats

Energy-onset beat markers for an audio asset/file, in seconds (beats) and composition frames (beat_frames). A lightweight onset grid, not a tempo tracker.

- family: `audio` · mutating: `false` · transport: CLI `kinetic tool detectBeats` / SDK `batch([{tool: "detectBeats"}])` / MCP `tools/call {name: "detectBeats"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.detectBeats(...)` in runScript or query · inside runScript: yes
- signature: `detectBeats {asset?: string, min_gap?: number = 0.25 [s], path?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `asset` | string | optional | — Audio asset id (or give path instead) |
| `composition` | string\|integer | optional | — Composition whose fps converts beats to beat_frames (default 0) |
| `min_gap` | number | optional default=0.25 | — Minimum seconds between markers |
| `path` | string | optional | — Workspace-relative audio file (or give asset instead) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Beat markers (seconds; frame = seconds x fps)

  ```json
  {"asset":"music","min_gap":0.3}
  ```

Related: `createLayer`, `animateProperty`, `renderVideoWithAudio`

CLI: `kinetic tool detectBeats --params '<json>' --workspace ./out`

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

## mixAudio

Mix every enabled in-range audio layer of a composition to a stereo WAV; report per-layer offsets, gain, fades, and errors.

- family: `audio` · mutating: `false` · transport: CLI `kinetic tool mixAudio` / SDK `batch([{tool: "mixAudio"}])` / MCP `tools/call {name: "mixAudio"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.mixAudio(...)` in runScript or query · inside runScript: yes
- signature: `mixAudio {out: string, mix_rate?: int = 48000 [Hz]}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | — Comp id, name, or index (default 0) |
| `mix_rate` | integer | optional default=48000 | — Audio sample rate in Hz (default 48000) |
| `out` | string | required | — WAV output path, workspace-relative |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Mix to WAV

  ```json
  {"out":"mix.wav"}
  ```

CLI: `kinetic tool mixAudio --params '<json>' --workspace ./out`

## muxAudio

Mux a WAV beside an encoded video (stream-copy video, AAC audio); verify with ffprobe and report stream + sync facts.

- family: `audio` · mutating: `false` · transport: CLI `kinetic tool muxAudio` / SDK `batch([{tool: "muxAudio"}])` / MCP `tools/call {name: "muxAudio"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.muxAudio(...)` in runScript or query · inside runScript: yes
- signature: `muxAudio {audio: string, out: string, video: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `audio` | string | required | — Audio (.wav) path, workspace-relative |
| `out` | string | required | — Output .mp4 path |
| `video` | string | required | — Video (.mp4) path, workspace-relative |
CLI: `kinetic tool muxAudio --params '<json>' --workspace ./out`

## setAudioGain

Set an audio layer's gain (dB), mute flag and ducking under voiceover lines. Commits.

- family: `audio` · mutating: `true` · transport: CLI `kinetic tool setAudioGain` / SDK `batch([{tool: "setAudioGain"}])` / MCP `tools/call {name: "setAudioGain"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setAudioGain(...)` in runScript · inside runScript: yes
- signature: `setAudioGain {layer: string|int, duck_db?: number, gain_db?: number [dB], muted?: bool}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `duck_db` | number | optional | — Dip under voices (-9 for a bed); 0 off |
| `gain_db` | number | optional | — Gain in dB (0 = unity, -6 = half amplitude) |
| `layer` | string\|integer | required | |
| `muted` | boolean | optional | — Mute the layer |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Duck under the voiceover

  ```json
  {"layer":"Music","duck_db":-9}
  ```

- Quieter

  ```json
  {"layer":"Music","gain_db":-6}
  ```

CLI: `kinetic tool setAudioGain --params '<json>' --workspace ./out`

## setAudioFades

Set an audio layer's linear fade-in/out lengths (seconds). Commits.

- family: `audio` · mutating: `true` · transport: CLI `kinetic tool setAudioFades` / SDK `batch([{tool: "setAudioFades"}])` / MCP `tools/call {name: "setAudioFades"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setAudioFades(...)` in runScript · inside runScript: yes
- signature: `setAudioFades {layer: string|int, fade_in_s?: number [s], fade_out_s?: number [s]}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `fade_in_s` | number | optional | — Fade-in length in seconds |
| `fade_out_s` | number | optional | — Fade-out length in seconds |
| `layer` | string\|integer | required | |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Fade in and out

  ```json
  {"layer":"Music","fade_in_s":0.5,"fade_out_s":1.5}
  ```

CLI: `kinetic tool setAudioFades --params '<json>' --workspace ./out`

## setAudioPan

Set an audio layer's constant-power pan (-1 left .. +1 right). Commits.

- family: `audio` · mutating: `true` · transport: CLI `kinetic tool setAudioPan` / SDK `batch([{tool: "setAudioPan"}])` / MCP `tools/call {name: "setAudioPan"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setAudioPan(...)` in runScript · inside runScript: yes
- signature: `setAudioPan {layer: string|int, pan: number}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `pan` | number | required | — Constant-power pan: -1 left, 0 centre, +1 right |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Slightly left

  ```json
  {"layer":"Music","pan":-0.2}
  ```

CLI: `kinetic tool setAudioPan --params '<json>' --workspace ./out`

## setAudioTiming

Set an audio layer's timeline window (in/out frames, or start_frame to move the whole clip), source offset, and playback stretch. Commits.

- family: `audio` · mutating: `true` · transport: CLI `kinetic tool setAudioTiming` / SDK `batch([{tool: "setAudioTiming"}])` / MCP `tools/call {name: "setAudioTiming"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setAudioTiming(...)` in runScript · inside runScript: yes
- signature: `setAudioTiming {layer: string|int, in_frame?: number [frame], out_frame?: number [frame], start_frame?: number, start_time?: number [s], time_stretch?: number}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `in_frame` | number | optional | — Composition frame the audio starts |
| `layer` | string\|integer | required | |
| `out_frame` | number | optional | — Composition frame the audio stops (exclusive) |
| `start_frame` | number | optional | — Move the clip to start on this frame: length, volume moves and the words keep with it (not with in_frame) |
| `start_time` | number | optional | — Seconds into the source |
| `time_stretch` | number | optional | — >1 plays slower |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Start the music at frame 15

  ```json
  {"layer":"Music","in_frame":15,"out_frame":150}
  ```

- Move the whole clip to start on frame 48

  ```json
  {"layer":"Music","start_frame":48}
  ```

CLI: `kinetic tool setAudioTiming --params '<json>' --workspace ./out`

