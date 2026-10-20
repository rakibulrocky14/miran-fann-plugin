# Finding and studying reference videos — tool reference (generated)

Source: the runtime registry via `kinetic schemas` — do not hand-edit; run `bun scripts/gen-docs.ts`.
5 of 181 tools — the ones this skill teaches. The full registry lives in docs/TOOLS.md.

## reference

Study video references: add a public video/page URL or workspace clip, list, sheets, frame, note, guide (recreation overlay), release, analyze, compare, recipe. For every video task, search for references unless the user opts out. guide reference.

- family: `design` · mutating: `false` · transport: CLI `kinetic tool reference` / SDK `batch([{tool: "reference"}])` / MCP `tools/call {name: "reference"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `reference {action?: add|list|sheets|frame|note|release|remove|guide|… = "analyze", against?: string, end?: number, id?: string, interval?: number = 1, layout?: side|stack = "side", mode?: overview|interval|shots = "overview", opacity?: number = 0.5, out?: string, page?: int = 0, pages?: int = 1, read_text?: bool = true, remove?: bool, source?: string, start?: number, text?: string, title?: string, video?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `action` | enum["add"|"list"|"sheets"|"frame"|"note"|"release"|"remove"|"guide"|"analyze"|"compare"|"recipe"] | optional default="analyze" | — guide overlays a reference for recreating; release deletes copied video, remove the study |
| `source` | string | optional | — add: public URL or workspace video |
| `video` | string | optional | — Reference id from add (or a workspace video) |
| `title` | string | optional | — add: a title for this reference |
| `mode` | enum["overview"|"interval"|"shots"] | optional default="overview" | — sheets mode; shots needs a footage index |
| `start` | number | optional | (min=0) — sheets: first second; frame/note: timestamp |
| `end` | number | optional | (min=0) — sheets/note: last second |
| `interval` | number | optional default=1 | (min=0.04, max=600) — interval sheets: step [s] |
| `page` | integer | optional default=0 | (min=0, max=1000) — sheets: page |
| `pages` | integer | optional default=1 | (min=1, max=4) — sheets: pages per call |
| `text` | string | optional | — note: what to learn at this timestamp |
| `against` | string | optional | — compare: your video |
| `composition` | string\|integer | optional | — Composition (default first) |
| `id` | string | optional | — guide/compare: same as video |
| `opacity` | number | optional default=0.5 | — guide opacity (0.05-1) |
| `remove` | boolean | optional | — guide: remove instead |
| `out` | string | optional | — compare: write the labelled side-by-side MP4 here |
| `layout` | enum["side"|"stack"] | optional default="side" | — compare panels: side | stack |
| `read_text` | boolean | optional default=true | — Read the type on screen (slower) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- List saved video studies

  ```json
  {"action":"list"}
  ```

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

- Search for suitable reference videos for each video task unless the user opts out. The miran-reference skill teaches discovery, URL/Envato preview extraction, contact sheets and task-specific application.
- add accepts source: a public video/page URL or workspace file. list returns saved ids; sheets and frame show the actual clip; note records what this task should adapt. Long work stays outside scripts.
- When the film or study is done, call reference {action: "release", video: ID} for each reference: copied video is deleted; sheets, frames, notes and source links remain. remove deletes the entire study. Reference media is refused as a production asset.
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

Follow the [reference workflow](SKILL.md) for discovery, acquisition limits, inspection and applying observations to the current film.
