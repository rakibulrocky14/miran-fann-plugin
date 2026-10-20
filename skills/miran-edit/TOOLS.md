# Editing footage — tool reference (generated)

Source: the runtime registry via `kinetic schemas` — do not hand-edit; run `bun scripts/gen-docs.ts`.
16 of 181 tools — the ones this skill teaches. The full registry lives in docs/TOOLS.md.

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

## record

Record the screen on this Mac (macOS 15+): a display or one window, 1-600 s, to video/record-<stamp>.mov in the film folder, imported as an asset, plus <same>.clicks.json (the mouse clicks, for kit.zoomFollow). The app running Miran needs Screen Recording permission (System Settings > Privacy & Security): without it the call answers permission-needed. audio: true adds system sound. guide footage.

- family: `footage` · mutating: `true` · transport: CLI `kinetic tool record` / SDK `batch([{tool: "record"}])` / MCP `tools/call {name: "record"}`
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: none (it records the screen for as long as you ask: call it directly)
- signature: `record {audio?: bool = false, clicks?: bool = true, display?: int = 0, name?: string, seconds?: number = 10, window?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `seconds` | number | optional default=10 | (min=1, max=600) — How long to record |
| `display` | integer | optional default=0 | (min=0, max=15) — Which display (0 = the main one) |
| `window` | string | optional | — Record one window instead: part of its title or app name |
| `audio` | boolean | optional default=false | — Record the system's sound too |
| `clicks` | boolean | optional default=true | — Log the mouse clicks next to the video |
| `name` | string | optional | — File name part: record-<name>.mov (default: the time) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Eight seconds of the main display, clicks logged

  ```json
  {"seconds":8}
  ```

- One app's window, with its sound

  ```json
  {"seconds":20,"window":"Safari","audio":true,"name":"checkout-flow"}
  ```

Tips:

- Needs macOS 15 and the user's Screen Recording permission for the app running Miran (Claude, Codex, Terminal). Without it the call answers permission-needed with where to switch it on: tell the user, then call again; never loop. Miran cannot grant it.
- It records for the seconds you ask (1-600), so ask the user to have the screen ready first. Over about 40 s it runs as a job: when jobStatus says done, importAsset the path it gave. Shorter recordings are imported at once.
- The click log (<name>.clicks.json, beside the video; inspectAsset shows it) holds t, frame, x, y (pixels of the picture, top-left) and fx, fy (fractions) for each mouse press. kit.zoomFollow(layer) with no clicks reads it and zooms on them: createLayer {kind: "video", path}, then kit.zoomFollow(layer, {zoom: 2}).
- audio: true records the Mac's own sound (not the microphone); the app's sound only plays if it is not muted. A window recording follows that window; a display recording shows everything on it, including notifications.

Related: `importAsset`, `createLayer`, `inspectAsset`, `footage`, `captureSite`

CLI: `kinetic tool record --params '<json>' --workspace ./out`

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

## variants

Versions of a cut, a composition per combination: opening hooks (a title over the first seconds), shapes (reframed or refitted, captions set again) and caption languages (translated on the device). The base is not changed. guide edit.

- family: `edit` · mutating: `true` · transport: CLI `kinetic tool variants` / SDK `batch([{tool: "variants"}])` / MCP `tools/call {name: "variants"}`
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: none (it builds each version with its own script: call it directly)
- signature: `variants {aspects?: 9:16|1:1|4:5|16:9[], follow?: auto|face|person|saliency|center, from?: string, hook_seconds?: number, hooks?: string[], languages?: string[]}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | — The base cut (default the first composition) |
| `hooks` | array | optional | — Opening lines, one version each |
| `aspects` | array | optional | — Shapes, one version each (default the base's) |
| `languages` | array | optional | — Caption languages, one version each: en, fr, es, de, ja, pt-BR ... |
| `from` | string | optional | — The captions' language (default en) |
| `hook_seconds` | number | optional | (min=0.5, max=10) — How long a hook shows (default 2.5) |
| `follow` | enum["auto"|"face"|"person"|"saliency"|"center"] | optional | — Reframing cut footage: what the crop follows (default auto) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Two opening hooks to test against each other

  ```json
  {"hooks":["Stop scrolling","You missed this"]}
  ```

- A vertical and a square version, each with the hook

  ```json
  {"hooks":["Ship it today"],"aspects":["9:16","1:1"]}
  ```

Tips:

- languages translates the caption layers (kit.captions makes them) and the hooks on this Mac with Apple Translation; a language-not-installed error says where to add it. from is the captions' language (default en). Each phrase keeps its time.
- Every combination is its own composition (hooks x aspects x languages, 24 at most); the base is not changed. A new shape is reframed for cut footage or refitted for built scenes, and its captions are set again at that shape.
- Check each version (renderContactSheet on the opening and a caption), then deliver it: deliver {composition, to}.

Related: `deliver`, `exportVersions`, `edit`, `critiqueEdit`, `renderContactSheet`

CLI: `kinetic tool variants --params '<json>' --workspace ./out`

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

