# The design pipeline — tool reference (generated)

Source: the runtime registry via `kinetic schemas` — do not hand-edit; run `bun scripts/gen-docs.ts`.
32 of 181 tools — the ones this skill teaches. The full registry lives in docs/TOOLS.md.

## analyzeBrief

Validate a brief + score strategies with reasons.

- family: `design` · mutating: `false` · transport: CLI `kinetic tool analyzeBrief` / SDK `batch([{tool: "analyzeBrief"}])` / MCP `tools/call {name: "analyzeBrief"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.analyzeBrief(...)` in runScript or query · inside runScript: yes
- signature: `analyzeBrief {brief: object}`

| param | type | required/default | notes |
|---|---|---|---|
| `brief` | object | required | — Design brief; guide {topic:"design-pipeline"} shows a complete example |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Score strategies

  ```json
  {"brief":$BRIEF}
  ```

`$BRIEF` is a design brief (see `guide {topic:"design-pipeline"}`); `$SPEC` is the spec object createDesignSpec returns.

Related: `buildStaticDesign`, `critiqueDesign`, `applyRepair`, `renderHeroFrame`

CLI: `kinetic tool analyzeBrief --params '<json>' --workspace ./out`

## createDesignSpec

Build a spec skeleton for a brief + strategy.

- family: `design` · mutating: `false` · transport: CLI `kinetic tool createDesignSpec` / SDK `batch([{tool: "createDesignSpec"}])` / MCP `tools/call {name: "createDesignSpec"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.createDesignSpec(...)` in runScript or query · inside runScript: yes
- signature: `createDesignSpec {brief: object, strategy: restrained-minimal|swiss-typographic|editorial|cinematic|premium-luxury|technical|data-centric|expressive-type|…}`

| param | type | required/default | notes |
|---|---|---|---|
| `brief` | object | required | — Design brief, the same object analyzeBrief takes (describeTool analyzeBrief shows every field) |
| `strategy` | enum["restrained-minimal"|"swiss-typographic"|"editorial"|"cinematic"|"premium-luxury"|"technical"|"data-centric"|"expressive-type"|"playful"|"brutalist"|"monochrome"|"product-focused"] | required | — Art-direction strategy; analyzeBrief ranks all of them for the brief with reasons |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Spec for a strategy

  ```json
  {"brief":$BRIEF,"strategy":"swiss-typographic"}
  ```

`$BRIEF` is a design brief (see `guide {topic:"design-pipeline"}`); `$SPEC` is the spec object createDesignSpec returns.

Related: `buildStaticDesign`, `critiqueDesign`, `applyRepair`, `renderHeroFrame`

CLI: `kinetic tool createDesignSpec --params '<json>' --workspace ./out`

## generateDesignCandidates

Top-N diverse candidates (distinct compositions first).

- family: `design` · mutating: `false` · transport: CLI `kinetic tool generateDesignCandidates` / SDK `batch([{tool: "generateDesignCandidates"}])` / MCP `tools/call {name: "generateDesignCandidates"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.generateDesignCandidates(...)` in runScript or query · inside runScript: yes
- signature: `generateDesignCandidates {brief: object, n?: int = 3}`

| param | type | required/default | notes |
|---|---|---|---|
| `brief` | object | required | — Design brief, the same object analyzeBrief takes (describeTool analyzeBrief shows every field) |
| `n` | integer | optional default=3 | (min=1, max=12) — How many diverse candidates (1..12, default 3) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Three diverse directions

  ```json
  {"brief":$BRIEF,"n":3}
  ```

`$BRIEF` is a design brief (see `guide {topic:"design-pipeline"}`); `$SPEC` is the spec object createDesignSpec returns.

Related: `buildStaticDesign`, `critiqueDesign`, `applyRepair`, `renderHeroFrame`

CLI: `kinetic tool generateDesignCandidates --params '<json>' --workspace ./out`

## inspectDesignCandidate

Summarize a spec: thesis, regions, tokens, motion.

- family: `design` · mutating: `false` · transport: CLI `kinetic tool inspectDesignCandidate` / SDK `batch([{tool: "inspectDesignCandidate"}])` / MCP `tools/call {name: "inspectDesignCandidate"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.inspectDesignCandidate(...)` in runScript or query · inside runScript: yes
- signature: `inspectDesignCandidate {spec: object}`

| param | type | required/default | notes |
|---|---|---|---|
| `spec` | object | required | — DesignSpec from createDesignSpec / generateDesignCandidates |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Summarize

  ```json
  {"spec":$SPEC}
  ```

`$BRIEF` is a design brief (see `guide {topic:"design-pipeline"}`); `$SPEC` is the spec object createDesignSpec returns.

CLI: `kinetic tool inspectDesignCandidate --params '<json>' --workspace ./out`

## buildStaticDesign

Compile a spec; optionally import the comp.

- family: `design` · mutating: `true` · transport: CLI `kinetic tool buildStaticDesign` / SDK `batch([{tool: "buildStaticDesign"}])` / MCP `tools/call {name: "buildStaticDesign"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.buildStaticDesign(...)` in runScript · inside runScript: yes
- signature: `buildStaticDesign {spec: object, import?: bool = false, name?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `spec` | object | required | — DesignSpec from createDesignSpec / generateDesignCandidates |
| `import` | boolean | optional default=false | — Also add the compiled composition to the project (default false = compile and report only) |
| `name` | string | optional | — Comp name when importing |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Compile into the project

  ```json
  {"spec":$SPEC,"import":true,"name":"Design A"}
  ```

`$BRIEF` is a design brief (see `guide {topic:"design-pipeline"}`); `$SPEC` is the spec object createDesignSpec returns.

CLI: `kinetic tool buildStaticDesign --params '<json>' --workspace ./out`

## adaptToAspectRatio

Recompile a spec for W×H (reflow, not rescale).

- family: `design` · mutating: `false` · transport: CLI `kinetic tool adaptToAspectRatio` / SDK `batch([{tool: "adaptToAspectRatio"}])` / MCP `tools/call {name: "adaptToAspectRatio"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.adaptToAspectRatio(...)` in runScript or query · inside runScript: yes
- signature: `adaptToAspectRatio {height: int [px], spec: object, width: int [px]}`

| param | type | required/default | notes |
|---|---|---|---|
| `spec` | object | required | — DesignSpec from createDesignSpec / generateDesignCandidates |
| `width` | integer | required | — Target width in px (layout reflows, it does not stretch) |
| `height` | integer | required | — Target height in px |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Reflow for 9:16

  ```json
  {"spec":$SPEC,"width":1080,"height":1920}
  ```

`$BRIEF` is a design brief (see `guide {topic:"design-pipeline"}`); `$SPEC` is the spec object createDesignSpec returns.

CLI: `kinetic tool adaptToAspectRatio --params '<json>' --workspace ./out`

## renderHeroFrame

Compile + render one frame to PNG.

- family: `design` · mutating: `false` · transport: CLI `kinetic tool renderHeroFrame` / SDK `batch([{tool: "renderHeroFrame"}])` / MCP `tools/call {name: "renderHeroFrame"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.renderHeroFrame(...)` in runScript or query · inside runScript: yes
- signature: `renderHeroFrame {out: string, spec: object, frame?: number = 0 [frame]}`

| param | type | required/default | notes |
|---|---|---|---|
| `spec` | object | required | — DesignSpec from createDesignSpec / generateDesignCandidates |
| `frame` | number | optional default=0 | — Frame to render (default 0) |
| `out` | string | required | — PNG output path, workspace-relative |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Look at it

  ```json
  {"spec":$SPEC,"frame":0,"out":"hero.png"}
  ```

`$BRIEF` is a design brief (see `guide {topic:"design-pipeline"}`); `$SPEC` is the spec object createDesignSpec returns.

CLI: `kinetic tool renderHeroFrame --params '<json>' --workspace ./out`

## planMotion

Compile + summarize the motion plan (order, channels, easing).

- family: `design` · mutating: `false` · transport: CLI `kinetic tool planMotion` / SDK `batch([{tool: "planMotion"}])` / MCP `tools/call {name: "planMotion"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.planMotion(...)` in runScript or query · inside runScript: yes
- signature: `planMotion {spec: object}`

| param | type | required/default | notes |
|---|---|---|---|
| `spec` | object | required | — DesignSpec from createDesignSpec / generateDesignCandidates |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Motion plan

  ```json
  {"spec":$SPEC}
  ```

`$BRIEF` is a design brief (see `guide {topic:"design-pipeline"}`); `$SPEC` is the spec object createDesignSpec returns.

CLI: `kinetic tool planMotion --params '<json>' --workspace ./out`

## proposeRepairs

Critique + auto-repair proposals (not applied).

- family: `design` · mutating: `false` · transport: CLI `kinetic tool proposeRepairs` / SDK `batch([{tool: "proposeRepairs"}])` / MCP `tools/call {name: "proposeRepairs"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.proposeRepairs(...)` in runScript or query · inside runScript: yes
- signature: `proposeRepairs {spec: object, frame?: number [frame], limit?: int = 3}`

| param | type | required/default | notes |
|---|---|---|---|
| `spec` | object | required | — DesignSpec from createDesignSpec / generateDesignCandidates |
| `frame` | number | optional | — Frame for the pixel lens (omit for static + temporal checks only) |
| `limit` | integer | optional default=3 | — Maximum proposals (default 3) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- What to fix

  ```json
  {"spec":$SPEC,"limit":3}
  ```

`$BRIEF` is a design brief (see `guide {topic:"design-pipeline"}`); `$SPEC` is the spec object createDesignSpec returns.

Related: `applyRepair`, `validateFinalDesign`

CLI: `kinetic tool proposeRepairs --params '<json>' --workspace ./out`

## applyRepair

Checkpointed repair loop; optionally import + write PNG.

- family: `design` · mutating: `true` · transport: CLI `kinetic tool applyRepair` / SDK `batch([{tool: "applyRepair"}])` / MCP `tools/call {name: "applyRepair"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.applyRepair(...)` in runScript · inside runScript: yes
- signature: `applyRepair {spec: object, finding?: string, frame?: number = 0 [frame], import?: bool = false, max_rounds?: int = 3, out?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `spec` | object | required | — DesignSpec from createDesignSpec / generateDesignCandidates |
| `frame` | number | optional default=0 | — Frame used for the pixel lens and the optional PNG (default 0) |
| `finding` | string | optional | — Only repair this finding code |
| `max_rounds` | integer | optional default=3 | — Critique-repair rounds before stopping (default 3) |
| `out` | string | optional | — Write repaired frame PNG |
| `import` | boolean | optional default=false | — Also add the compiled composition to the project (default false = compile and report only) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Repair loop

  ```json
  {"spec":$SPEC,"max_rounds":2}
  ```

`$BRIEF` is a design brief (see `guide {topic:"design-pipeline"}`); `$SPEC` is the spec object createDesignSpec returns.

CLI: `kinetic tool applyRepair --params '<json>' --workspace ./out`

## importSvg

Import SVG as editable, animatable vector layers: a workspace file (path) or markup you write (svg). Draw icons, illustrations and UI in SVG; each top-level <g id> becomes a layer <name>/<id> pivoting on its own centre

- family: `vector` · mutating: `true` · transport: CLI `kinetic tool importSvg` / SDK `batch([{tool: "importSvg"}])` / MCP `tools/call {name: "importSvg"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.importSvg(...)` in runScript · inside runScript: yes
- signature: `importSvg {at?: number[] [px], explode?: array|string, filters?: rasterize|unfiltered|refuse = "rasterize", height?: number [px], mode?: string, name?: string, path?: string, pivot?: string, svg?: string, text_policy?: string = "keep_text", width?: number [px]}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `path` | string | optional | — Workspace-relative .svg path |
| `svg` | string | optional | — SVG markup instead of path; viewBox = composition size maps 1:1 (y down). One top-level <g id> per animated part; saved under svg/ |
| `name` | string | optional | — Name for the import: split layers are named <name>/<element id> (default svg) |
| `at` | array | optional | — [x, y] where the SVG's centre goes, px from the composition centre, y up (default [0, 0]) |
| `width` | number | optional | — Scale the SVG uniformly to this width in px (or pass height) |
| `height` | number | optional | — Scale the SVG uniformly to this height in px |
| `pivot` | string | optional | — content (default when split: each layer scales and rotates around its own element) or document (the SVG centre) |
| `mode` | string | optional | — explode_top_level_groups (default for svg markup: a layer per top-level element, animate each separately), preserve_groups (default for path: one layer), explode_all_nodes, single_vector_layer, explicit_bitmap_fallback |
| `text_policy` | string | optional default="keep_text" | — keep_text (default): editable text; outline: exact paths |
| `explode` | array\|string | optional | — Ids of <g> groups to split into their children, each a layer in place under a group layer (unnamed children: <group>/1, /2). For parts that move separately inside one group |
| `filters` | enum["rasterize"|"unfiltered"|"refuse"] | optional default="rasterize" | — SVG filters with no native equivalent (feDropShadow, blur, offset, flood, merge are native) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Editable vector layer

  ```json
  {"path":"logo.svg"}
  ```

- One layer per top-level group (animate parts separately)

  ```json
  {"path":"logo.svg","mode":"explode_top_level_groups"}
  ```

- Write the art as SVG: each <g id> becomes a layer (hero/card, hero/icon)

  ```json
  {"name":"hero","svg":"<svg viewBox=\"0 0 1920 1080\"><g id=\"card\"><rect x=\"660\" y=\"340\" width=\"600\" height=\"400\" rx=\"32\" fill=\"#16161D\" stroke=\"#FFFFFF22\"/></g><g id=\"icon\"><circle cx=\"960\" cy=\"500\" r=\"60\" fill=\"none\" stroke=\"#FF4D00\" stroke-width=\"10\" stroke-linecap=\"round\"/><path d=\"M930 500 l22 22 l40 -44\" fill=\"none\" stroke=\"#FF4D00\" stroke-width=\"10\" stroke-linecap=\"round\" stroke-linejoin=\"round\"/></g></svg>"}
  ```

- A 24 px line icon placed at 160 px

  ```json
  {"name":"bolt","svg":"<svg viewBox=\"0 0 24 24\"><g id=\"bolt\"><path d=\"M13 2 L4 14 h7 l-1 8 l9 -12 h-7 z\" fill=\"none\" stroke=\"#FFFFFF\" stroke-width=\"1.5\" stroke-linejoin=\"round\"/></g></svg>","width":160,"at":[-600,0]}
  ```

- Cards inside one <g id=stages>: explode it, so each card is its own layer (flow/ingest, flow/build, ...) under the group layer flow/stages

  ```json
  {"name":"flow","explode":["stages"],"svg":"<svg viewBox=\"0 0 1920 1080\"><g id=\"stages\"><g id=\"ingest\"><rect x=\"200\" y=\"420\" width=\"460\" height=\"240\" rx=\"28\" fill=\"#16161D\"/></g><g id=\"build\"><rect x=\"730\" y=\"420\" width=\"460\" height=\"240\" rx=\"28\" fill=\"#16161D\"/></g><g id=\"ship\"><rect x=\"1260\" y=\"420\" width=\"460\" height=\"240\" rx=\"28\" fill=\"#FF4D00\"/></g></g><g id=\"title\"><text x=\"200\" y=\"300\" font-size=\"72\" fill=\"#F5F5F7\">Pipeline</text></g></svg>"}
  ```

Tips:

- Write illustrations, icons, UI mockups and layouts as SVG markup (svg): you know SVG well, so use it for anything with detail. viewBox = the composition size maps SVG pixels 1:1 (top-left origin, y down).
- Put each part you want to animate in its own top-level <g id="name">: it becomes the layer <import name>/<id>, pivoting on its own centre, so kit.enter, scale pops and rotations act on the part.
- Stroked paths draw themselves on with animateProperty trim.end 0 -> 1 on the layer (fills hide until the stroke is complete). Gradients, rounded rects, text and opacity all import.
- SVG filters: blur, offset, flood, composite, merge, colour matrix and feDropShadow render natively. Others (turbulence, lighting, morphology) keep their look as a raster of that part and the result lists them under warnings; prefer Miran effects (addEffect drop_shadow, glow, fractal_noise) on the layer.
- Result: layers_added (also named layers, as in kit.svg) has one row per layer made: {id, name, nodes, kind (vector, or group for an exploded group), rest_scale, part (the <g id> it holds), center ([x, y] px from the composition centre, y up: where the part rests, also its position), parent and children (exploded groups)}. ids lists the layer ids; parts maps part id to layer id; at maps part id to centre (the same ids, parts and at kit.svg returns).
- More of the result: rest_scale is the scale every drawn layer rests at (the fit from width / height, else 1) and scale_note says so in words when it is not 1: animate scale relative to it, never to 1. report has the importer's counts and its warnings; unsupported lists what it could not draw; bitmap_fallback is true for mode explicit_bitmap_fallback; embedded_assets lists images the SVG carried, stored as project assets; warnings names filters and splits worth a look; path and mode (markup imports) are the saved file, which reimportSvg takes, and the mode used; hash is the project hash after the import.
- Several things that must move separately sit in one <g> (four cards in <g id="stages">)? explode: ["stages"]. The group becomes an empty group layer (kind group) with an identity transform and each child its own layer under it, exactly where the SVG drew it: animate the cards one by one, or the group layer to move them together. Children without an id are named <group>/1, <group>/2; give each child its own <g id> for names you choose. The result echoes the ids as exploded, with an explode_note.
- A real hole through a shape, to whatever is behind it (ticket notches), is geometry or a mask, never a patch in the background colour: fill-rule="evenodd" on a path whose inner subpath is the hole, a <mask> (white keeps, black cuts, grey fades) on the shape or its group, or a <clipPath> with clip-rule="evenodd". All import and render on GPU and CPU. guide svg has the runnable example.

Related: `animateProperty`, `listVectorNodes`, `setVectorFill`, `compareSvgFidelity`

CLI: `kinetic tool importSvg --params '<json>' --workspace ./out`

## inspectSvg

Parse + structural summary (no conversion).

- family: `vector` · mutating: `false` · transport: CLI `kinetic tool inspectSvg` / SDK `batch([{tool: "inspectSvg"}])` / MCP `tools/call {name: "inspectSvg"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.inspectSvg(...)` in runScript or query · inside runScript: yes
- signature: `inspectSvg {path: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `path` | string | required | — Workspace-relative .svg path |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Look before importing

  ```json
  {"path":"logo.svg"}
  ```

CLI: `kinetic tool inspectSvg --params '<json>' --workspace ./out`

## lottie

Lottie (After Effects' web format): import a .json or .lottie (path, url or json) as editable layers, placed as one nested layer; export a composition as .json or .lottie.

- family: `assets` · mutating: `true` · transport: CLI `kinetic tool lottie` / SDK `batch([{tool: "lottie"}])` / MCP `tools/call {name: "lottie"}`
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: a path or inline JSON, export; not a url
- signature: `lottie {action: import|export, at?: number[] [px], json?: any, name?: string, out?: string, path?: string, place?: bool = true, start_frame?: number = 0 [frame], text?: outlines|live = "outlines", url?: string, width?: number [px]}`

| param | type | required/default | notes |
|---|---|---|---|
| `action` | enum["import"|"export"] | required | — import or export |
| `path` | string | optional | — import: workspace .json or .lottie |
| `url` | string | optional | — import: https link (saved in the workspace) |
| `json` | any | optional | — import: the document (object or string) |
| `name` | string | optional | — import: composition name (default the file's) |
| `composition` | string\|integer | optional | — import: where to place it (default the first); export: which |
| `place` | boolean | optional default=true | — import: place it as a nested layer |
| `at` | array | optional | — import: centre, px from the comp centre (y up) |
| `width` | number | optional | — import: placed width in px (built sharp at that size) |
| `start_frame` | number | optional default=0 | — import: first frame |
| `out` | string | optional | — export: .json or .lottie path (default lottie/<composition>.json) |
| `text` | enum["outlines"|"live"] | optional default="outlines" | — export: outlines (same everywhere, animators kept) or live (editable, needs the font) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Open an After Effects / LottieFiles animation 300 px wide, starting at frame 12

  ```json
  {"action":"import","path":"loader.json","width":300,"at":[600,-300],"start_frame":12}
  ```

- Paste the animation itself

  ```json
  {"action":"import","name":"pulse","json":{"v":"5.7.4","fr":30,"ip":0,"op":30,"w":100,"h":100,"layers":[{"ty":4,"ind":1,"nm":"Dot","ip":0,"op":30,"st":0,"ks":{"p":{"a":0,"k":[50,50]},"s":{"a":1,"k":[{"t":0,"s":[60,60],"o":{"x":[0.7],"y":[0]},"i":{"x":[0.3],"y":[1]}},{"t":30,"s":[100,100]}]}},"shapes":[{"ty":"el","p":{"a":0,"k":[0,0]},"s":{"a":0,"k":[40,40]}},{"ty":"fl","c":{"a":0,"k":[1,0.3,0,1]},"o":{"a":0,"k":100}}]}]}}
  ```

- Export a composition for the web

  ```json
  {"action":"export","composition":"inset","out":"web/inset.json"}
  ```

- As a .lottie with live, editable text

  ```json
  {"action":"export","composition":"inset","out":"web/inset.lottie","text":"live"}
  ```

Tips:

- Import places the animation as one nested layer; its own layers live in the composition the result names (listLayers {composition}), editable like any others.
- Pass width to build it at the size it shows (sharp vectors) and start_frame to time it. Read the report: unsupported, approximations and dropped expressions are listed, never silent.
- Export bakes everything Miran draws into eased keys and plays the same in lottie-web: text becomes outlines unless text: "live"; particles, procedural fills, video and most effects are reported (build web pieces from shapes, SVG and text).

Related: `listLayers`, `animateProperty`, `renderFrame`, `importSvg`, `guide`

CLI: `kinetic tool lottie --params '<json>' --workspace ./out`

## findAssets

Find open-licence icons (Iconify), Lottie animations (LottieFiles) and Google Fonts by keyword; use a result to fetch, import and credit it in one step.

- family: `assets` · mutating: `true` · transport: CLI `kinetic tool findAssets` / SDK `batch([{tool: "findAssets"}])` / MCP `tools/call {name: "findAssets"}`
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: credits; not search or use (network)
- signature: `findAssets {action?: search|use|credits = "search", at?: number[] [px], author?: string, color?: string, id?: string, kind?: icon|lottie|font = "icon", limit?: int = 8, name?: string, query?: string, set?: string, start_frame?: number [frame], url?: string, width?: number [px]}`

| param | type | required/default | notes |
|---|---|---|---|
| `action` | enum["search"|"use"|"credits"] | optional default="search" | — search, use (a result) or credits |
| `query` | string | optional | — search: keywords ("rocket", "loading", "geometric sans") |
| `kind` | enum["icon"|"lottie"|"font"] | optional default="icon" | — search: icon (SVG), lottie or font |
| `set` | string | optional | — search: icon sets, comma separated (lucide, tabler, ph, ...): one set keeps a style |
| `limit` | integer | optional default=8 | (min=1, max=32) — search: results |
| `id` | string | optional | — use: a result's id |
| `url` | string | optional | — use: Lottie file link (from the result) |
| `author` | string | optional | — use: credited author (from the result) |
| `name` | string | optional | — use: layer or composition name |
| `width` | number | optional | — use: width in px |
| `at` | array | optional | — use: centre, px from the comp centre (y up) |
| `color` | string | optional | — use: icon colour #RRGGBB |
| `composition` | string\|integer | optional | — use: target composition |
| `start_frame` | number | optional | — use: animation start frame |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- An icon by keyword, from one set so a piece stays consistent

  ```json
  {"query":"rocket","set":"lucide","limit":4}
  ```

- Use it: fetched as SVG, imported as editable layers, credited

  ```json
  {"action":"use","id":"icon:lucide:rocket","width":160,"at":[-600,0],"color":"#FF4D00"}
  ```

- A Lottie animation

  ```json
  {"query":"loading","kind":"lottie","limit":3}
  ```

- Use one (pass the result's use params)

  ```json
  {"action":"use","id":"lottie:123","url":"https://assets.lottiefiles.com/packages/demo-loader.json","name":"Loader","width":200,"at":[600,-300]}
  ```

- Font families by name or style

  ```json
  {"query":"sans","kind":"font","limit":5}
  ```

- What to credit (for an end card or the description)

  ```json
  {"action":"credits"}
  ```

Tips:

- Search, then use a result with its use params: one call fetches it, imports it (icons as SVG layers, animations as Lottie, fonts with importFont) and adds it to the project's credits.
- Pick icons from one set (set: "lucide") so line weights and corners match across a piece; color recolours single-colour icons.
- Credits list source, author and licence; attribution_required names what must be credited on screen (CC BY icons).

Related: `importSvg`, `lottie`, `importFont`, `listLayers`

CLI: `kinetic tool findAssets --params '<json>' --workspace ./out`

## listVectorNodes

Walk a vector layer: ids, labels, src paths, kinds.

- family: `vector` · mutating: `false` · transport: CLI `kinetic tool listVectorNodes` / SDK `batch([{tool: "listVectorNodes"}])` / MCP `tools/call {name: "listVectorNodes"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.listVectorNodes(...)` in runScript or query · inside runScript: yes
- signature: `listVectorNodes {layer: string|int}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Node ids

  ```json
  {"layer":"Logo"}
  ```

CLI: `kinetic tool listVectorNodes --params '<json>' --workspace ./out`

## findVectorNodes

Substring search over node id/label/src_path.

- family: `vector` · mutating: `false` · transport: CLI `kinetic tool findVectorNodes` / SDK `batch([{tool: "findVectorNodes"}])` / MCP `tools/call {name: "findVectorNodes"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.findVectorNodes(...)` in runScript or query · inside runScript: yes
- signature: `findVectorNodes {layer: string|int, query: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `query` | string | required | — Substring matched against node id, label and source path |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Find a node

  ```json
  {"layer":"Logo","query":"bar"}
  ```

CLI: `kinetic tool findVectorNodes --params '<json>' --workspace ./out`

## setVectorFill

Replace a path, shape, or SVG text fill: CSS hex, none, or VectorPaint JSON.

- family: `vector` · mutating: `true` · transport: CLI `kinetic tool setVectorFill` / SDK `batch([{tool: "setVectorFill"}])` / MCP `tools/call {name: "setVectorFill"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setVectorFill(...)` in runScript · inside runScript: yes
- signature: `setVectorFill {fill: any, layer: string|int, node: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `node` | string | required | — Vector node id (listVectorNodes) |
| `fill` | any | required | — "#rgb"/"#rrggbb"/"#rrggbbaa" hex, "none", or full VectorPaint JSON (solid/linear/radial/...); RGBA channels are 0..1. See exportSvg output. |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Recolor

  ```json
  {"layer":"Logo","node":"dot","fill":"#3D5AFE"}
  ```

CLI: `kinetic tool setVectorFill --params '<json>' --workspace ./out`

## setVectorStroke

Replace/clear a path, shape, or SVG text stroke (null clears).

- family: `vector` · mutating: `true` · transport: CLI `kinetic tool setVectorStroke` / SDK `batch([{tool: "setVectorStroke"}])` / MCP `tools/call {name: "setVectorStroke"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setVectorStroke(...)` in runScript · inside runScript: yes
- signature: `setVectorStroke {layer: string|int, node: string, stroke?: any}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `node` | string | required | — Vector node id (listVectorNodes) |
| `stroke` | any | optional | — {"color":"#FFFFFF","width":2,"cap":"round","join":"round","dash":[8,4]} (cap butt|round|square, join miter|round|bevel, all optional), full VectorStroke JSON with paint (see exportSvg output), or null to clear |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Add an outline

  ```json
  {"layer":"Logo","node":"bar","stroke":{"color":"#FFFFFF","width":3,"join":"round"}}
  ```

- Remove it

  ```json
  {"layer":"Logo","node":"bar","stroke":null}
  ```

CLI: `kinetic tool setVectorStroke --params '<json>' --workspace ./out`

## setVectorText

Edit SVG text content and typography while keeping it a native text node.

- family: `vector` · mutating: `true` · transport: CLI `kinetic tool setVectorText` / SDK `batch([{tool: "setVectorText"}])` / MCP `tools/call {name: "setVectorText"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setVectorText(...)` in runScript · inside runScript: yes
- signature: `setVectorText {layer: string|int, node: string, font_family?: string, font_size?: number [px], font_style?: string, font_weight?: int, letter_spacing?: number [px], text?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `node` | string | required | — Vector node id (listVectorNodes) |
| `text` | string | optional | — New text content |
| `font_family` | string | optional | — Installed family (e.g. "Helvetica Neue", "SF Pro") or an imported font's; inspectTextCoverage reports fallbacks |
| `font_size` | number | optional | — Font size in px |
| `font_weight` | integer | optional | — CSS weight 100..900 |
| `font_style` | string | optional | — normal, italic or oblique |
| `letter_spacing` | number | optional | — Letter spacing in px |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Change the wordmark

  ```json
  {"layer":"Logo","node":"word","text":"Miran","font_weight":700}
  ```

CLI: `kinetic tool setVectorText --params '<json>' --workspace ./out`

## setVectorTransform

Replace a node local transform ([a,b,c,d,e,f]).

- family: `vector` · mutating: `true` · transport: CLI `kinetic tool setVectorTransform` / SDK `batch([{tool: "setVectorTransform"}])` / MCP `tools/call {name: "setVectorTransform"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setVectorTransform(...)` in runScript · inside runScript: yes
- signature: `setVectorTransform {layer: string|int, node: string, transform: any}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `node` | string | required | — Vector node id (listVectorNodes) |
| `transform` | any | required | — Affine [a, b, c, d, e, f] (SVG matrix order: x' = a*x + c*y + e, y' = b*x + d*y + f) in the node's parent space |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Nudge a node 10 px right

  ```json
  {"layer":"Logo","node":"bar","transform":[1,0,0,1,10,0]}
  ```

CLI: `kinetic tool setVectorTransform --params '<json>' --workspace ./out`

## animateVectorPath

Morph a path node: to_segments (+optional from), frame range.

- family: `vector` · mutating: `true` · transport: CLI `kinetic tool animateVectorPath` / SDK `batch([{tool: "animateVectorPath"}])` / MCP `tools/call {name: "animateVectorPath"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.animateVectorPath(...)` in runScript · inside runScript: yes
- signature: `animateVectorPath {end: number [frame], layer: string|int, node: string, start: number [frame], to_segments: any[], from_segments?: any[], samples?: int = 128}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `node` | string | required | — Vector node id (listVectorNodes) |
| `to_segments` | array | required | — Target path as segments in node space: [{"MoveTo":[x,y]}, {"LineTo":[x,y]}, {"CubicTo":[[c1x,c1y],[c2x,c2y],[x,y]]}, "Close"] (exportSvg shows the current ones) |
| `from_segments` | array | optional | — Start path in the same format (default: the node's current path) |
| `start` | number | required | — Frame the morph starts |
| `end` | number | required | — Frame the morph completes |
| `samples` | integer | optional default=128 | — Resample points per path for the morph (default 128) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Morph a path into a square

  ```json
  {"layer":"Logo","node":"dot","to_segments":[{"MoveTo":[20,10]},{"LineTo":[120,10]},{"LineTo":[120,110]},{"LineTo":[20,110]},"Close"],"start":0,"end":30}
  ```

CLI: `kinetic tool animateVectorPath --params '<json>' --workspace ./out`

## animateVectorTransform

Set [[frame, affine]] keys on a node.

- family: `vector` · mutating: `true` · transport: CLI `kinetic tool animateVectorTransform` / SDK `batch([{tool: "animateVectorTransform"}])` / MCP `tools/call {name: "animateVectorTransform"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.animateVectorTransform(...)` in runScript · inside runScript: yes
- signature: `animateVectorTransform {keys: any[][], layer: string|int, node: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `node` | string | required | — Vector node id (listVectorNodes) |
| `keys` | array | required | — [[frame, [a,b,c,d,e,f]], ...] |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Slide a node in

  ```json
  {"layer":"Logo","node":"bar","keys":[[0,[1,0,0,1,-40,0]],[20,[1,0,0,1,0,0]]]}
  ```

CLI: `kinetic tool animateVectorTransform --params '<json>' --workspace ./out`

## morphShape

Morph a shape layer, or a path node in a vector layer, into another outline: a shape layer (to), a path node (to_node) or SVG path data (d). Point counts, parts and holes are matched; the target fits the source's box.

- family: `vector` · mutating: `true` · transport: CLI `kinetic tool morphShape` / SDK `batch([{tool: "morphShape"}])` / MCP `tools/call {name: "morphShape"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.morphShape(...)` in runScript · inside runScript: yes
- signature: `morphShape {layer: string|int, d?: string, duration_frames?: number = 24 [frames], ease?: any, fit?: bool = true, node?: string, remove?: bool = false, samples?: int = 128, start_frame?: number = 0 [frame], to?: string|int, to_layer?: string|int, to_node?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | — Shape layer, or vector layer (with node) |
| `node` | string | optional | — Vector layers: path node id (default: the first path) |
| `to` | string\|integer | optional | — Target shape layer (its outline) |
| `to_node` | string | optional | — Or: target path node id (in to_layer, default the same layer) |
| `to_layer` | string\|integer | optional | — Layer holding to_node |
| `d` | string | optional | — Or: SVG path data, e.g. an icon's d attribute |
| `fit` | boolean | optional default=true | — Scale and centre the target on the source's box |
| `start_frame` | number | optional default=0 | — Frame the morph starts |
| `duration_frames` | number | optional default=24 | — Frames to the target outline |
| `ease` | any | optional | — Ease name or spring:<preset> (default smooth) |
| `samples` | integer | optional default=128 | — Points per outline |
| `remove` | boolean | optional default=false | — Drop the morph |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- The circle becomes the card's rounded box

  ```json
  {"layer":"Accent","to":"Card","start_frame":10,"duration_frames":20}
  ```

- Into an icon's path data, springy

  ```json
  {"layer":"Accent","d":"M12 2 L22 22 L2 22 Z","ease":"spring:snappy"}
  ```

- One SVG path into another (the logo's dot becomes its bar)

  ```json
  {"layer":"Logo","node":"dot","to_node":"bar","start_frame":0,"duration_frames":30}
  ```

Related: `convertTextToShapes`, `importSvg`, `listVectorNodes`, `renderContactSheet`

CLI: `kinetic tool morphShape --params '<json>' --workspace ./out`

## convertTextToShapes

Create Shapes from Text: a vector layer with one path per letter (char-0, char-1, ...) in one group per line (line-0, ...) where the text was, for letter morphs and per-letter or per-line edits; the text layer is disabled.

- family: `vector` · mutating: `true` · transport: CLI `kinetic tool convertTextToShapes` / SDK `batch([{tool: "convertTextToShapes"}])` / MCP `tools/call {name: "convertTextToShapes"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.convertTextToShapes(...)` in runScript · inside runScript: yes
- signature: `convertTextToShapes {layer: string|int, frame?: number [frame], keep_text?: bool = false}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | — The text layer |
| `frame` | number | optional | — Frame whose text, size and wrap to outline (default: the layer's first frame) |
| `keep_text` | boolean | optional default=false | — Leave the text layer enabled too |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Outline the headline's letters

  ```json
  {"layer":"Headline"}
  ```

Related: `morphShape`, `setVectorTransform`, `listVectorNodes`

CLI: `kinetic tool convertTextToShapes --params '<json>' --workspace ./out`

## exportSvg

Export portable SVG with embedded image bytes; refuses masks/filters that cannot round-trip faithfully.

- family: `vector` · mutating: `false` · transport: CLI `kinetic tool exportSvg` / SDK `batch([{tool: "exportSvg"}])` / MCP `tools/call {name: "exportSvg"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.exportSvg(...)` in runScript or query · inside runScript: yes
- signature: `exportSvg {layer: string|int}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Export

  ```json
  {"layer":"Logo"}
  ```

CLI: `kinetic tool exportSvg --params '<json>' --workspace ./out`

## brand

Brand kits: save a palette, font, logo, motion style and voice once (~/.miran/brands) and apply it to any project; kit pieces, captions, templates and voiceovers then use it.

- family: `design` · mutating: `true` · transport: CLI `kinetic tool brand` / SDK `batch([{tool: "brand"}])` / MCP `tools/call {name: "brand"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.brand(...)` in runScript · inside runScript: all but capture (network; a rollback undoes save and delete)
- signature: `brand {action: save|list|show|apply|delete|capture, fonts?: string[], logo?: string, motion?: crisp|smooth|playful|punchy|calm, name?: string, pronounce?: object, theme?: object, url?: string, voice?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `action` | enum["save"|"list"|"show"|"apply"|"delete"|"capture"] | required | — What to do (capture: read a brand from a public page's url) |
| `url` | string | optional | — capture: the page to read |
| `name` | string | optional | — Brand name |
| `theme` | object | optional | — save: {font, text, muted, accent, accent2, bg, surface, line} (default: the project's kit theme) |
| `motion` | enum["crisp"|"smooth"|"playful"|"punchy"|"calm"] | optional | — save: the motion style (eases and springs) |
| `logo` | string | optional | — save: a workspace .svg or image |
| `fonts` | array | optional | — save: Google Fonts, e.g. ["Inter 400", "Inter 700"] (default: the project's) |
| `voice` | string | optional | — save: voiceover voice (default: the project's) |
| `pronounce` | object | optional | — save: word → respelling (default: the project's) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Save a brand: palette, font and motion

  ```json
  {"action":"save","name":"Northwind","theme":{"font":"Helvetica Neue","accent":"#7C4DFF","accent2":"#22D3EE","bg":"#0A0A12"},"motion":"crisp"}
  ```

- Use it in this project

  ```json
  {"action":"apply","name":"Northwind"}
  ```

- Saved brands

  ```json
  {"action":"list"}
  ```

Related: `template`, `runScript`, `importFont`

CLI: `kinetic tool brand --params '<json>' --workspace ./out`

## template

Templates with controls (AE Essential Graphics): list, describe and apply the starter library (lower third, title card, stat callout, chart beat, UI notification, end card) or saved ones; save a script or a composition as a template; batch renders one video per CSV/JSON row.

- family: `design` · mutating: `true` · transport: CLI `kinetic tool template` / SDK `batch([{tool: "template"}])` / MCP `tools/call {name: "template"}`
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: list, describe, apply, save, delete (a rollback undoes save and delete); not batch
- signature: `template {action: list|describe|apply|batch|save|delete, at?: number[], brand?: string, controls?: object[], data?: string|array, description?: string, duration_frames?: number, map?: object, name?: string, out?: string, place?: bool = true, quality?: string, source?: string, start_frame?: number = 0 [frame], validate?: bool, values?: object}`

| param | type | required/default | notes |
|---|---|---|---|
| `action` | enum["list"|"describe"|"apply"|"batch"|"save"|"delete"] | required | — What to do (batch: one video per data row) |
| `name` | string | optional | — Template id or name |
| `values` | object | optional | — apply: control values (describe lists them) |
| `start_frame` | number | optional default=0 | — apply: frame it starts |
| `composition` | string\|integer | optional | — apply: target composition; save: the composition to save |
| `brand` | string | optional | — apply: apply this brand first |
| `at` | array | optional | — apply (composition templates): where the nested layer sits |
| `place` | boolean | optional default=true | — apply (composition templates): add it as a nested layer |
| `source` | string | optional | — save: kit script reading controls.<key> (and controls.start) |
| `controls` | array | optional | — save: [{key, type, default, label, options}] for a script; [{key, layer, field: text|fill|stroke|enabled|opacity}] for a composition |
| `description` | string | optional | — save: what it is |
| `duration_frames` | number | optional | — save (script): typical length |
| `data` | string\|array | optional | — batch: rows as CSV, JSON or a .csv/.json file; columns name controls |
| `map` | object | optional | — batch: control → column |
| `out` | string | optional | — batch: path pattern, {row} {template} {<column>} |
| `quality` | string | optional | — batch: renderVideo quality |
| `validate` | boolean | optional | — batch: check the rows only |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- The starter library

  ```json
  {"action":"list"}
  ```

- A template's controls

  ```json
  {"action":"describe","name":"lower-third"}
  ```

- A lower third at frame 30

  ```json
  {"action":"apply","name":"lower-third","start_frame":30,"values":{"name":"Maya Chen","role":"Head of Product","style":"glass"}}
  ```

- A stat that counts up in a ring

  ```json
  {"action":"apply","name":"stat-callout","values":{"value":3.2,"decimals":1,"suffix":"x","label":"Faster renders"}}
  ```

- Save a script with controls as a template

  ```json
  {"action":"save","name":"Big word","source":"return kit.title(controls.word, {size: 160, start: controls.start, style: 'pop'}).id;","controls":[{"key":"word","type":"text","default":"Launch"}]}
  ```

- Save a composition as a template

  ```json
  {"action":"save","name":"Inset card","composition":"inset","controls":[]}
  ```

- Check a CSV before rendering one video per row

  ```json
  {"action":"batch","name":"lower-third","data":"name,role\nAda Lovelace,Engineer\nAlan Turing,Codebreaker","out":"renders/{row}-{name}.mp4","validate":true}
  ```

Related: `brand`, `runScript`, `renderContactSheet`

CLI: `kinetic tool template --params '<json>' --workspace ./out`

## setProtectedArtwork

Mark a vector layer as protected brand artwork with a settle frame.

- family: `vector` · mutating: `true` · transport: CLI `kinetic tool setProtectedArtwork` / SDK `batch([{tool: "setProtectedArtwork"}])` / MCP `tools/call {name: "setProtectedArtwork"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setProtectedArtwork(...)` in runScript · inside runScript: yes
- signature: `setProtectedArtwork {layer: string|int, protected?: bool = true, settle_frame?: number [frame]}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `protected` | boolean | optional default=true | — true (default) marks the artwork protected; false clears it |
| `settle_frame` | number | optional | — Frame by which the artwork must be back at its exact authored pose |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Brand mark must be exact at frame 90

  ```json
  {"layer":"Logo","settle_frame":90}
  ```

CLI: `kinetic tool setProtectedArtwork --params '<json>' --workspace ./out`

## setVectorImageAsset

Replace an SVG image node with a registered image asset while retaining its placement.

- family: `vector` · mutating: `true` · transport: CLI `kinetic tool setVectorImageAsset` / SDK `batch([{tool: "setVectorImageAsset"}])` / MCP `tools/call {name: "setVectorImageAsset"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setVectorImageAsset(...)` in runScript · inside runScript: yes
- signature: `setVectorImageAsset {asset: string, layer: string|int, node: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `node` | string | required | — Vector node id (listVectorNodes) |
| `asset` | string | required | — Registered image asset id to place in the node |
CLI: `kinetic tool setVectorImageAsset --params '<json>' --workspace ./out`

## groupVectorNodes

Wrap nodes in a new group (recursive take).

- family: `vector` · mutating: `true` · transport: CLI `kinetic tool groupVectorNodes` / SDK `batch([{tool: "groupVectorNodes"}])` / MCP `tools/call {name: "groupVectorNodes"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.groupVectorNodes(...)` in runScript · inside runScript: yes
- signature: `groupVectorNodes {layer: string|int, nodes: string[], group_id?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `nodes` | array | required | — Node ids to wrap into the new group |
| `group_id` | string | optional | — Id for the new group (default: generated) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Group two nodes

  ```json
  {"layer":"Logo","nodes":["dot","bar"],"group_id":"pair"}
  ```

CLI: `kinetic tool groupVectorNodes --params '<json>' --workspace ./out`

## convertVectorGroupToComposition

Copy a group into a new composition; returns its index.

- family: `vector` · mutating: `true` · transport: CLI `kinetic tool convertVectorGroupToComposition` / SDK `batch([{tool: "convertVectorGroupToComposition"}])` / MCP `tools/call {name: "convertVectorGroupToComposition"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.convertVectorGroupToComposition(...)` in runScript · inside runScript: yes
- signature: `convertVectorGroupToComposition {group: string, layer: string|int, name?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `group` | string | required | — Group node id to copy out |
| `name` | string | optional | — Name of the new composition |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Pull a group into its own comp

  ```json
  {"layer":"Logo","group":"mark","name":"Mark"}
  ```

CLI: `kinetic tool convertVectorGroupToComposition --params '<json>' --workspace ./out`

## reimportSvg

Reimport only the matching SVG layer; refresh unedited source fields and retain recorded edits by id.

- family: `vector` · mutating: `true` · transport: CLI `kinetic tool reimportSvg` / SDK `batch([{tool: "reimportSvg"}])` / MCP `tools/call {name: "reimportSvg"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.reimportSvg(...)` in runScript · inside runScript: yes
- signature: `reimportSvg {layer: string|int, path: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `path` | string | required | — Workspace-relative .svg path |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Refresh from the file, keep edits

  ```json
  {"layer":"Logo","path":"logo.svg"}
  ```

CLI: `kinetic tool reimportSvg --params '<json>' --workspace ./out`

