# Building scenes — tool reference (generated)

Source: the runtime registry via `kinetic schemas` — do not hand-edit; run `bun scripts/gen-docs.ts`.
51 of 178 tools — the ones this skill teaches. The full registry lives in docs/TOOLS.md.

## newProject

Create a fresh project (rebases session history). Not valid inside batch.

- family: `project` · mutating: `true` · transport: CLI `kinetic tool newProject` only — rebases history, so it is rejected inside `batch`; MCP `tools/call {name: "newProject"}` is a single call and is fine
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `newProject {name: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `name` | string | required | — Project name |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Start fresh

  ```json
  {"name":"launch-teaser"}
  ```

CLI: `kinetic tool newProject --params '<json>' --workspace ./out`

## openProject

Open a project JSON file from the workspace, or the autosave of the last session (autosave: true, after a server restart). Not valid inside batch.

- family: `project` · mutating: `true` · transport: CLI `kinetic tool openProject` only — rebases history, so it is rejected inside `batch`; MCP `tools/call {name: "openProject"}` is a single call and is fine
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `openProject {autosave?: boolean|string, path?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `path` | string | optional | — Workspace-relative .json path |
| `autosave` | boolean\|string | optional | — true: restore the autosave instead of a file; "previous": the one before this session's first change |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Open a saved project

  ```json
  {"path":"project.json"}
  ```

CLI: `kinetic tool openProject --params '<json>' --workspace ./out`

## saveProject

Save the project as deterministic JSON.

- family: `project` · mutating: `false` · transport: CLI `kinetic tool saveProject` / SDK `batch([{tool: "saveProject"}])` / MCP `tools/call {name: "saveProject"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `saveProject {path: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `path` | string | required | — Workspace-relative .json path to write |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Save

  ```json
  {"path":"project.json"}
  ```

CLI: `kinetic tool saveProject --params '<json>' --workspace ./out`

## projectHash

Content hash of the deterministic project JSON.

- family: `project` · mutating: `false` · transport: CLI `kinetic tool projectHash` / SDK `batch([{tool: "projectHash"}])` / MCP `tools/call {name: "projectHash"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.projectHash(...)` in runScript or query · inside runScript: yes
- signature: `projectHash {}`

No parameters.

Example: `{}`

CLI: `kinetic tool projectHash --params '<json>' --workspace ./out`

## batch

Run tool calls atomically: all commit or all roll back. newProject and openProject rebase history and must be separate calls.

- family: `project` · mutating: `true` · transport: CLI `kinetic tool batch` / SDK `batch([{tool: "batch"}])` / MCP `tools/call {name: "batch"}`
- history: creates an undo step
- lean tool list: not listed (a script is already atomic) · inside runScript: none (a script is already atomic)
- signature: `batch {calls: object[]}`

| param | type | required/default | notes |
|---|---|---|---|
| `calls` | array | required | — Calls run in order; later calls see earlier effects. newProject and openProject are rejected |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Build and animate a layer atomically

  ```json
  {"calls":[{"tool":"createLayer","params":{"name":"Eyebrow","kind":"text","text":"INTRODUCING","font_size":28,"position":[0,150]}},{"tool":"setTextStyle","params":{"layer":"Eyebrow","font_weight":600,"tracking":0.2,"color":"#8A8F98"}},{"tool":"animateProperty","params":{"layer":"Eyebrow","property":"opacity","from":0,"to":1,"start_frame":6,"duration_frames":12,"ease":"standard"}}]}
  ```

Tips:

- A failed call rolls back the whole batch, so partial scenes never linger. runScript does the same with loops, math and access to each call's result.

CLI: `kinetic tool batch --params '<json>' --workspace ./out`

## createComposition

Add a composition (rational fps supported).

- family: `scene` · mutating: `true` · transport: CLI `kinetic tool createComposition` / SDK `batch([{tool: "createComposition"}])` / MCP `tools/call {name: "createComposition"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.createComposition(...)` in runScript · inside runScript: yes
- signature: `createComposition {height: int [px], width: int [px], background?: string|array, duration?: number [s], duration_frames?: int [frames], fps?: number, fps_den?: int, fps_num?: int, id?: string, name?: string, transparent?: bool}`

| param | type | required/default | notes |
|---|---|---|---|
| `id` | string | optional | — Unique id (default comp_N) |
| `name` | string | optional | — Display name (tools also accept it as a selector) |
| `width` | integer | required | (min=1) — Frame width in px (even numbers for video export; 1920 for 1080p, 3840 for 4K) |
| `height` | integer | required | (min=1) — Frame height in px (even numbers for video export; 1080 for 1080p, 1920 for a 9:16 vertical) |
| `fps` | number | optional | — Float fps; ignored when fps_num given |
| `fps_num` | integer | optional | — Rational numerator, e.g. 30000 |
| `fps_den` | integer | optional | — Rational denominator, e.g. 1001 |
| `duration_frames` | integer | optional | (min=1) — Length in frames (default 60; e.g. 300 = 10 s at 30 fps) |
| `duration` | number | optional | — Length in seconds (when duration_frames is not given); setCompositionField changes it later |
| `background` | string\|array | optional | — "#rrggbb" (#rgb, #rrggbbaa) or [r,g,b,a] 0..1 |
| `transparent` | boolean | optional | — Alpha output: no background fill (default false) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- 1080p, 30 fps, 6 s

  ```json
  {"id":"promo","name":"Promo","width":1920,"height":1080,"fps":30,"duration_frames":180,"background":"#0B0B0F"}
  ```

- Vertical 9:16 at 60 fps

  ```json
  {"id":"reel","width":1080,"height":1920,"fps":60,"duration_frames":600,"background":"#000000"}
  ```

Related: `createLayer`, `setCompositionField`, `guide`

CLI: `kinetic tool createComposition --params '<json>' --workspace ./out`

## setCompositionField

Set a composition field: its length (duration in s or duration_frames; layers that ran to the end follow it, layers past a shorter end are trimmed) and name, motion blur (enabled, shutter angle/phase in degrees, max samples, adaptive sampling), background color, transparent flag, and the sound's master bus (gain, loudness target).

- family: `scene` · mutating: `true` · transport: CLI `kinetic tool setCompositionField` / SDK `batch([{tool: "setCompositionField"}])` / MCP `tools/call {name: "setCompositionField"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setCompositionField(...)` in runScript · inside runScript: yes
- signature: `setCompositionField {field: duration|duration_frames|name|motion_blur_enabled|shutter_angle|shutter_phase|motion_blur_samples|motion_blur_adaptive|…, value: any}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `field` | enum["duration"|"duration_frames"|"name"|"motion_blur_enabled"|"shutter_angle"|"shutter_phase"|"motion_blur_samples"|"motion_blur_adaptive"|"background"|"transparent"|"master_gain_db"|"loudness_target"] | required | — Which composition field to set; see value |
| `value` | any | required | — duration: seconds; duration_frames: frames; name: text. motion_blur_enabled/motion_blur_adaptive/transparent: bool. shutter_angle: degrees 1..=720 (180 = film look). shutter_phase: degrees -720..=720 like After Effects (0 = shutter centered on the frame; -90 with a 180 shutter = shutter ends on the frame). motion_blur_samples: taps 2..=64 where motion can't be measured (else 1 per 1.5 px, up to 64). background: color. master_gain_db: dB on the whole mix. loudness_target: LUFS the mix is normalised to (-14 punchy, -16 calm; true peak kept under -1 dBTP), null for off. |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Film-style motion blur

  ```json
  {"field":"motion_blur_enabled","value":true}
  ```

- 180-degree shutter

  ```json
  {"field":"shutter_angle","value":180}
  ```

- Background color

  ```json
  {"field":"background","value":"#101014"}
  ```

- Master the mix to -14 LUFS

  ```json
  {"field":"loudness_target","value":-14}
  ```

Tips:

- Motion blur is adaptive: still frames cost one sample, measured motion one per 1.5 px of travel (up to 64); motion_blur_samples is the count where motion cannot be measured (particles, trims, animated masks).

CLI: `kinetic tool setCompositionField --params '<json>' --workspace ./out`

## listCompositions

List compositions with layer counts.

- family: `scene` · mutating: `false` · transport: CLI `kinetic tool listCompositions` / SDK `batch([{tool: "listCompositions"}])` / MCP `tools/call {name: "listCompositions"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.listCompositions(...)` in runScript or query · inside runScript: yes
- signature: `listCompositions {}`

No parameters.

Example: `{}`

CLI: `kinetic tool listCompositions --params '<json>' --workspace ./out`

## createLayer

Add a layer (shape, text, solid, null, image, video, nested, particle, adjustment, procedural, audio, light, model). Lights shade 3D layers; model draws an imported glTF (guide camera).

- family: `scene` · mutating: `true` · transport: CLI `kinetic tool createLayer` / SDK `batch([{tool: "createLayer"}])` / MCP `tools/call {name: "createLayer"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.createLayer(...)` in runScript · inside runScript: yes
- signature: `createLayer {kind: shape|text|solid|null|image|video|nested|particle|…, name: string, align?: string, asset?: string, fill?: string|array, fit?: contain|cover|stretch|crop, font?: string, font_family?: string, font_size?: number [px], font_style?: string, font_weight?: number, gradient?: object, height?: number [px], light?: object, line_height?: number, max_width?: number [px], model?: object, opacity?: number, params?: object, path?: string, position?: number[] [px], proc?: string, radius?: number [px], seed?: int, shape?: rect|rounded|circle|ellipse|star|polygon, specular_angle?: number [deg], specular_intensity?: number, speed?: number, src_in?: number [s], src_out?: number [s], start_frame?: number [frame], stroke_color?: string|array, stroke_width?: number, target?: string, text?: string, tracking?: number, width?: number [px]}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | — Comp id, name, or index (default 0) |
| `name` | string | required | — Layer name; give every layer a unique, descriptive name ("Headline", "Card BG") and address it by name later |
| `kind` | string | required | — shape (vector geometry), text, solid (flat rect), null (invisible parent/controller), image (needs asset), nested (another composition, needs target), particle, adjustment (applies its effects to everything below), procedural (gradient/noise/pattern generator, see proc), audio (needs asset) |
| `path` | string | optional | — image/video/audio: a workspace file to import |
| `src_in` | number | optional | — video: clip seconds to start at |
| `src_out` | number | optional | — video: clip seconds to stop at |
| `speed` | number | optional | — video: playback rate (1) |
| `start_frame` | number | optional | — video/nested: timeline frame it starts on |
| `fit` | enum["contain"|"cover"|"stretch"|"crop"] | optional | — image/video: how it fills its box (contain); with no width/height the box is the composition, so cover fills the frame |
| `model` | object | optional | — kind=model: {size (px of its largest side, default 400), animation (name or index; "none" holds the rest pose; default the first), speed, loop, offset (s)} |
| `light` | object | optional | — kind=light: {type: point|spot|parallel|ambient, color, intensity, target [x,y,z], cone_angle, cone_feather, falloff, radius, casts_shadows, shadow_darkness, shadow_diffusion} |
| `shape` | enum["rect"|"rounded"|"circle"|"ellipse"|"star"|"polygon"] | optional | — Shape geometry (default rect) |
| `width` | number | optional | — Width in px: shape/solid size (default 100); circle diameter; star/polygon outer diameter |
| `height` | number | optional | — Height in px for shape rect/rounded/ellipse and solid (default 100) |
| `radius` | number | optional | — Corner radius in px for shape=rounded (default 8); radius for shape=circle (default width/2) |
| `fill` | string\|array | optional | — "#rrggbb" (#rgb, #rrggbbaa) or [r,g,b,a] 0..1 |
| `stroke_width` | number | optional | (min=0) — Stroke width; omit for no stroke (shape) |
| `stroke_color` | string\|array | optional | — as fill |
| `specular_angle` | number | optional | (min=0, max=360) — Micro-chamfer light angle in degrees (default 45) |
| `specular_intensity` | number | optional | (min=0, max=1) — Micro-chamfer highlight: 0 matte, 1 full sweep (default 0.5) |
| `gradient` | object | optional | — shape: a gradient fill (as setShapes) |
| `text` | string | optional | — Text content for kind=text (default "Text"); \n breaks lines. Style it with setTextStyle |
| `font_size` | number | optional | — Font size in px for kind=text (default 32) |
| `font_family` | string | optional | — text: family |
| `font` | string | optional | — text: font_family |
| `font_weight` | number | optional | — text: 100-900 |
| `font_style` | string | optional | — text: normal or italic |
| `align` | string | optional | — text: left, center or right |
| `tracking` | number | optional | — text: em letter-spacing |
| `line_height` | number | optional | — text: multiplier of size |
| `max_width` | number | optional | — text: wrap width in px |
| `asset` | string | optional | — Asset id or name for image, video, audio and model layers |
| `target` | string | optional | — Composition id for nested layers |
| `proc` | string | optional | — Procedural kind for kind=procedural: linear_gradient, radial_gradient, angular_gradient, mesh_gradient, grid, dot_grid, noise, contours, turbulence, stripes, wave_field, sdf_shape, checker, scanlines (default noise) |
| `seed` | integer | optional | (min=0) — Deterministic seed for kind=procedural (default 1) |
| `params` | object | optional | — Procedural params for kind=procedural (name -> number), e.g. {"scale": 6.0} |
| `position` | array | optional | — Layer centre in px, +x right, +y UP; a 3rd element is z depth (3D layers) |
| `opacity` | number | optional | (min=0, max=1) — 0..1 (default 1) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Headline text (style with setTextStyle)

  ```json
  {"name":"Title","kind":"text","text":"Built for speed","font_size":120,"position":[0,40]}
  ```

- Rounded card

  ```json
  {"name":"Panel","kind":"shape","shape":"rounded","width":800,"height":480,"radius":32,"fill":"#1C1C24"}
  ```

- Outlined ring

  ```json
  {"name":"Ring","kind":"shape","shape":"circle","width":300,"fill":"#00000000","stroke_width":4,"stroke_color":"#FF4D00","position":[520,0]}
  ```

- Gradient background

  ```json
  {"name":"Sky","kind":"procedural","proc":"linear_gradient","params":{"c0":"#0B1020","c1":"#3D5AFE","angle":90,"span":1080}}
  ```

- Null to parent a group under

  ```json
  {"name":"Group","kind":"null"}
  ```

- Nested composition

  ```json
  {"name":"Inset","kind":"nested","target":"inset","position":[480,-200]}
  ```

- Image from an imported asset

  ```json
  {"name":"Photo","kind":"image","asset":"photo"}
  ```

- Key light: a soft spot that casts shadows (3D layers are then lit)

  ```json
  {"name":"Key","kind":"light","light":{"type":"spot","color":"#FFF4E8","intensity":1.2,"target":[0,-150,0],"cone_angle":70,"cone_feather":0.6,"casts_shadows":true,"shadow_diffusion":8},"position":[-700,900,-900]}
  ```

- Ambient fill light

  ```json
  {"name":"Fill","kind":"light","light":{"type":"ambient","color":"#DDE5FF","intensity":0.3}}
  ```

- A 3D model 500 px across, playing its animation at half speed

  ```json
  {"name":"Product","kind":"model","asset":"cube","model":{"size":500,"animation":"Spin","speed":0.5},"position":[0,-40,200]}
  ```

- A model holding its rest pose (no animation)

  ```json
  {"name":"Still","kind":"model","asset":"cube","model":{"animation":"none"}}
  ```

Tips:

- Layers are 2D by default: position is px from the composition centre with y UP. Camera tools only move layers with is_3d=true.
- Give each layer a unique name and address it by name in later calls.
- Paint order is creation order (last created on top, index 0 is the bottom); reorderLayer changes it: to top|bottom, above or below a layer, or index.
- kind model: importAsset {path, kind: "model"} a .glb (or .gltf) first; the model is scaled so its largest side is model.size px and centred on the layer origin, and plays its first animation unless model.animation says otherwise. Lit like any 3D layer.
- kind light: once a composition has a light, 3D layers are lit by the lights only (none = unlit cards, the After Effects rule): add a key (spot or parallel, casts_shadows) and an ambient fill. In a script, kit.lit builds the whole stage with a floor (guide {topic:"camera"}).

Related: `setLayerField`, `setTextStyle`, `setShapes`, `animateProperty`, `addEffect`, `parentLayer`

CLI: `kinetic tool createLayer --params '<json>' --workspace ./out`

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
| `value` | any | required | — position: [x, y] or [x, y, z] px from comp centre, y up. scale: number (uniform) or [sx, sy(, sz)], 1 = 100%. rotation: degrees, a number (2D spin) or [x, y, z]. anchor: [x, y] px in layer space (the pivot). opacity: 0..1. in_frame/out_frame: frames (visible when in_frame <= f < out_frame). blend_mode: normal, multiply, screen, overlay, add, darken, lighten, subtract, difference, color_dodge, color_burn, hue, saturation, color, luminosity. is_3d, motion_blur_enabled, enabled, solo, locked, shy: bool. intended: bool (true: critiqueMotion leaves this layer's motion out; false clears it). behind: the cutout layer (id or name) a text sits behind (kit.textBehind sets it; critiqueLayout reports the text if it paints over that cutout); null clears. depth (an image layer, a 3D photo): {asset: gray map, white near, scale_px: 120, near: 1, far: 0} or the asset name; null clears. name: string. follow: {layer (leader; omit = own motion), delay (frames, 3), spring, amount, axes (separate dimensions: ["x", {axis:"rotation", delay, spring, amount}])}. squash: 0.5 (1 cartoon). speed: [[frame, speed], ...]. time_remap: [[frame, source_frame(, ease|"hold")], ...] (frame in this composition; source_frame in the nested composition's own frames). posterize: fps (12). echo: {count, interval, decay}. puppet: {pins: [{name, at: [x, y] layer px from its centre, y up | "top"/"left"/... | node: svg id, kind: position|bend|advanced}], starch: [{at, radius, amount}], detail: coarse|normal|fine}, then animateProperty puppet.<pin>.x / .y (.rotation, .scale for bend pins). null removes a motion field |

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

## setShapes

Replace a shape layer's geometry/style; add After Effects shape operators (ops: repeater, offset, wiggle, wiggle_transform, zig_zag, pucker_bloat, twist, round_corners, merge, path), gradient fills and strokes, and stroke styling (caps, dashes, tapered strokes). guide topic=shapes.

- family: `content` · mutating: `true` · transport: CLI `kinetic tool setShapes` / SDK `batch([{tool: "setShapes"}])` / MCP `tools/call {name: "setShapes"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setShapes(...)` in runScript · inside runScript: yes
- signature: `setShapes {layer: string|int, fill?: string|array, geometry?: any, gradient?: object|null, ops?: object[], specular_angle?: number [deg], specular_intensity?: number, stroke_color?: string|array, stroke_gradient?: object|null, stroke_style?: object, stroke_width?: number, trim?: object}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `geometry` | any | optional | — One of: {"Rect":{width,height}}, {"RoundedRect":{width,height,radius}}, {"Circle":{radius}}, {"Ellipse":{radius_x,radius_y}}, {"Polygon":{sides,radius}}, {"Star":{points,outer_radius,inner_radius}}, {"Path":{points:[{pos:[x,y],in_tangent:[dx,dy],out_tangent:[dx,dy]}],closed}}, {"Polyline":{points:[...]}}. px, y up, centred on the layer origin; tangents are offsets from pos |
| `fill` | string\|array | optional | — "#rrggbb" (#rgb, #rrggbbaa) or [r,g,b,a] 0..1 |
| `stroke_width` | number | optional | (min=0) — Stroke width in layer units; omit for no stroke (kind=shape only) |
| `stroke_color` | string\|array | optional | — "#rrggbb" (#rgb, #rrggbbaa) or [r,g,b,a] 0..1 |
| `specular_angle` | number | optional | (min=0, max=360) — Micro-chamfer light angle in degrees (default 45) |
| `specular_intensity` | number | optional | (min=0, max=1) — Micro-chamfer highlight strength: 0 matte, 1 full specular sweep (default 0.5) |
| `trim` | object | optional | — Resting stroke trim (After Effects Trim Paths): start/end are fractions 0..1 of the outline (closed shapes start at the top centre and run clockwise); offset turns the range around closed shapes (1 = one loop). Draw a stroke on with animateProperty {property:"trim.end", from:0, to:1}. A trimmed shape draws only its stroke. |
| `ops` | array | optional | — Operators in order, replacing the current list ([] removes): {type:repeater, copies, position:[x,y], rotation, scale, anchor, start_opacity, end_opacity}, {type:offset, amount, copies, copy_offset, join}, {type:wiggle, size, detail, speed, seed, smooth, boil_fps, temporal_phase, spatial_phase}, {type:wiggle_transform, position, rotation, scale, speed}, {type:zig_zag, size, ridges, smooth, per_segment}, {type:pucker_bloat, amount}, {type:twist, angle, center}, {type:round_corners, radius}, {type:path, geometry, position} then {type:merge, mode: union|subtract|intersect|exclude}. Animate params as op.<type>.<param> |
| `gradient` | object\|null | optional | — Gradient fill: {colors:[...], angle} (0 = left to right, 90 = bottom to top), or {stops:[[offset, colour]...], from:[x,y], to:[x,y]}; type radial: center, radius. null removes |
| `stroke_gradient` | object\|null | optional | — Gradient stroke, same form as gradient |
| `stroke_style` | object | optional | — {cap: butt|round|square, join: miter|round|bevel, dash:[on, off, ...] px, dash_offset, taper:{start, end (fractions of the length), start_width, end_width, ease, wave, wavelength, phase}} |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Resize a rounded rect

  ```json
  {"layer":"Card","geometry":{"RoundedRect":{"width":800,"height":460,"radius":36}},"fill":"#1C1C24"}
  ```

- Star

  ```json
  {"layer":"Accent","geometry":{"Star":{"points":5,"outer_radius":90,"inner_radius":40}}}
  ```

- Outline ring, 70% drawn (animate trim.end to draw it on)

  ```json
  {"layer":"Accent","geometry":{"Circle":{"radius":80}},"fill":"#00000000","stroke_width":10,"stroke_color":"#FF4D00","specular_intensity":0,"trim":{"end":0.7}}
  ```

- Ring of 12 copies (repeater; animate op.repeater.copies)

  ```json
  {"layer":"Accent","ops":[{"type":"repeater","copies":12,"rotation":30,"anchor":[0,-160],"end_opacity":0.3}]}
  ```

- Liquid blob with a gradient

  ```json
  {"layer":"Accent","ops":[{"type":"wiggle","size":14,"detail":4,"speed":0.5}],"gradient":{"colors":["#FF3D7F","#3D5AFE"],"angle":60}}
  ```

- Ring cut-out (merge paths)

  ```json
  {"layer":"Accent","ops":[{"type":"path","geometry":{"Circle":{"radius":40}}},{"type":"merge","mode":"subtract"}]}
  ```

- Badge edge (zig zag)

  ```json
  {"layer":"Accent","ops":[{"type":"zig_zag","size":8,"ridges":20,"smooth":true}]}
  ```

- Brush stroke: tapered, dashed, gradient

  ```json
  {"layer":"Accent","stroke_width":14,"stroke_color":"#FFFFFF","stroke_style":{"cap":"round","taper":{"start":0.3,"end":0.5}},"stroke_gradient":{"colors":["#FFD60A","#FF3D7F"]}}
  ```

Tips:

- trim draws a shape's STROKE on (After Effects Trim Paths): give the shape a stroke, a transparent fill (#00000000) and animate trim.end 0 -> 1. Closed shapes start at the top centre and run clockwise.
- ops apply in order and replace the list ([] removes); trim applies just before the first repeater. Animate any operator param as op.<type>.<param> (op.repeater.copies, op.wiggle.size, op.offset.amount). guide topic=shapes has recipes.

CLI: `kinetic tool setShapes --params '<json>' --workspace ./out`

## setTextContent

Set a text layer's string (keeps style), or make it a counter: counter {from, to, start_frame, duration_frames, ease, format keys} animates the number it shows (a clock or a duration: style duration).

- family: `content` · mutating: `true` · transport: CLI `kinetic tool setTextContent` / SDK `batch([{tool: "setTextContent"}])` / MCP `tools/call {name: "setTextContent"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setTextContent(...)` in runScript · inside runScript: yes
- signature: `setTextContent {layer: string|int, counter?: object, number_format?: object, text?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `text` | string | optional | — New string (style is kept); \n breaks lines |
| `counter` | object | optional | — Count a number: {from (default 0), to, start_frame (default 0), duration_frames (default 45), ease (default emphasized-decelerate), decimals, prefix, suffix, thousands}. Adds an animateProperty move on text.value (chain more with animateProperty property text.value). |
| `number_format` | object | optional | — How an animated text.value is written: the counter's format keys |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- New copy

  ```json
  {"layer":"Headline","text":"Render at the\nspeed of thought"}
  ```

- Counter: 0 -> 98% over 1.5 s

  ```json
  {"layer":"Headline","counter":{"to":98,"start_frame":10,"duration_frames":45,"suffix":"%"}}
  ```

- Money with grouping

  ```json
  {"layer":"Headline","counter":{"from":0,"to":12480,"duration_frames":40,"prefix":"$","thousands":true}}
  ```

- Countdown in words: 14 min 10 s -> 1 min 42 s (the value is seconds)

  ```json
  {"layer":"Headline","counter":{"from":850,"to":102,"start_frame":0,"duration_frames":90,"ease":"linear","style":"duration","pattern":"words"}}
  ```

- A clock, m:ss (patterns: m:ss, mm:ss, h:mm:ss, words)

  ```json
  {"layer":"Headline","number_format":{"style":"duration","pattern":"m:ss"},"counter":{"from":0,"to":75,"duration_frames":60,"ease":"linear"}}
  ```

Tips:

- counter animates text.value with an ease; the layer's text becomes the formatted number each frame. Keep tabular-looking digits steady with align center or a monospace family.
- Times: style "duration" reads the number as seconds. pattern m:ss (1:15), mm:ss (01:15), h:mm:ss (1:02:05) or words (14 min 10 s); decimals counts fractions of a second. Works in counter, in number_format, and as kit.counter {style: "duration", pattern}. No expression needed for a countdown: from > to.
- Motion blur keeps the words of the rendered frame on every shutter tap (counters, expressions, links): only where the text moves blurs, so a counting number never smears into another string.

Related: `addTextAnimator`, `measureText`, `inspectTextCoverage`, `createLayer`

CLI: `kinetic tool setTextContent --params '<json>' --workspace ./out`

## setTextStyle

Set font family, size, weight, style, tracking, alignment, line height, wrap width, color.

- family: `content` · mutating: `true` · transport: CLI `kinetic tool setTextStyle` / SDK `batch([{tool: "setTextStyle"}])` / MCP `tools/call {name: "setTextStyle"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setTextStyle(...)` in runScript · inside runScript: yes
- signature: `setTextStyle {layer: string|int, align?: left|center|right, axes?: object, color?: string|array, colors?: object[], font_family?: string, font_size?: number [px], font_style?: normal|italic, font_weight?: int, line_height?: number, max_width?: number [px], tracking?: number}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `font_family` | string | optional | — Installed family, or one added with importFont |
| `axes` | object | optional | — Variable font axes, e.g. {"wdth": 80, "opsz": 32}; wght follows font_weight (any 100-900, not only named weights) |
| `font_size` | number | optional | — Glyph size in px |
| `tracking` | number | optional | — Letter-spacing in em (fraction of font size, e.g. 0.1); range [-2, 2] |
| `font_weight` | integer | optional | (min=100, max=900) — CSS weight 100..900 (400 regular, 700 bold); the nearest installed face is used |
| `font_style` | enum["normal"|"italic"] | optional | — normal or italic |
| `align` | enum["left"|"center"|"right"] | optional | — Paragraph alignment about the layer origin |
| `line_height` | number | optional | (min=0) — Line-height multiplier of font size; 0 = automatic (1.2) |
| `max_width` | number | optional | (min=0) — Wrap width in px; 0 disables wrapping |
| `color` | string\|array | optional | — "#rrggbb" (#rgb, #rrggbbaa) or [r,g,b,a] 0..1 |
| `colors` | array | optional | — Per-character colour, replacing earlier ones ([] clears): {match:".", color} (every occurrence; nth: 1 for only the first) or {start, end, color} (character indices, end exclusive) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Display headline

  ```json
  {"layer":"Headline","font_family":"Helvetica Neue","font_size":110,"font_weight":700,"tracking":-0.03,"color":"#F5F5F7","align":"center"}
  ```

- Wrapped body copy

  ```json
  {"layer":"Headline","font_size":36,"font_weight":400,"line_height":1.35,"max_width":900,"align":"left"}
  ```

- One word in the accent colour

  ```json
  {"layer":"Headline","colors":[{"match":"thought","color":"#D97757"}]}
  ```

- A weight between named ones, narrower (variable SF Pro)

  ```json
  {"layer":"Headline","font_family":"SF Pro","font_weight":860,"axes":{"wdth":85}}
  ```

Tips:

- tracking is in em: -0.02..-0.04 for large display type, +0.1..+0.2 for small uppercase labels.
- Text tools (createLayer, setTextStyle, setTextContent, measureText) put warnings in the result: a family that is not installed (what drew instead, installed look-alikes) and characters the font has no glyph for (they draw as a gap). Read them; installed families work offline, importFont needs the network.

Related: `addTextAnimator`, `measureText`, `inspectTextCoverage`, `createLayer`

CLI: `kinetic tool setTextStyle --params '<json>' --workspace ./out`

## setTextPath

Text on a path (AE Path Options): letters ride a shape's outline, a circle or a curve through points. text.path.offset animates them along it.

- family: `content` · mutating: `true` · transport: CLI `kinetic tool setTextPath` / SDK `batch([{tool: "setTextPath"}])` / MCP `tools/call {name: "setTextPath"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setTextPath(...)` in runScript · inside runScript: yes
- signature: `setTextPath {layer: string|int, align?: left|center|right, circle?: number, closed?: bool = false, frame?: number = 0 [frame], offset?: number = 0 [px], path?: string|int, perpendicular?: bool = true, points?: number[][], remove?: bool = false, reverse?: bool = false, smooth?: bool = true}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | — The text layer |
| `path` | string\|integer | optional | — Shape layer to follow |
| `circle` | number | optional | — Or: circle radius around the layer origin (starts on top, clockwise) |
| `points` | array | optional | — Or: [x, y] points in layer space |
| `smooth` | boolean | optional default=true | — points: smooth curve |
| `closed` | boolean | optional default=false | — points: loop |
| `offset` | number | optional default=0 | — px along the path (text.path.offset) |
| `align` | enum["left"|"center"|"right"] | optional | — On the path (closed paths centre on the start) |
| `perpendicular` | boolean | optional default=true | — Letters turn with the path |
| `reverse` | boolean | optional default=false | — Run the other way (bottom of a circle) |
| `frame` | number | optional default=0 | — Frame to read the path layer at |
| `remove` | boolean | optional default=false | — Back to a straight line |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- A label around the top of a circle

  ```json
  {"layer":"Headline","circle":320}
  ```

- Along the bottom, reading left to right

  ```json
  {"layer":"Headline","circle":320,"reverse":true,"offset":1005}
  ```

- Follow a shape layer's outline

  ```json
  {"layer":"Headline","path":"Accent","align":"center"}
  ```

- A wave through points

  ```json
  {"layer":"Headline","points":[[-600,0],[-300,120],[0,0],[300,-120],[600,0]]}
  ```

Related: `animateProperty`, `setTextStyle`, `addTextAnimator`

CLI: `kinetic tool setTextPath --params '<json>' --workspace ./out`

## measureText

Measure a text layer (or a string in a given style) exactly as it renders: overall size plus a box per line and per word, in the layer's space (px from its origin, +y up) and in composition space. Use it to place underlines, highlight pills, cursors, icons or per-word layers against real text.

- family: `content` · mutating: `false` · transport: CLI `kinetic tool measureText` / SDK `batch([{tool: "measureText"}])` / MCP `tools/call {name: "measureText"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.measureText(...)` in runScript or query · inside runScript: yes
- signature: `measureText {align?: left|center|right, font_family?: string, font_size?: number [px], font_style?: normal|italic, font_weight?: int, layer?: string|int, line_height?: number, max_width?: number [px], text?: string, tracking?: number}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | optional | — Text layer to measure (its current string and style); other fields override it |
| `text` | string | optional | — String to measure (required without layer) |
| `font_family` | string | optional | — Font family (default Helvetica Neue, or the layer's) |
| `font_size` | number | optional | — Size in px |
| `font_weight` | integer | optional | (min=100, max=900) — 100..900 |
| `font_style` | enum["normal"|"italic"] | optional | — normal or italic |
| `tracking` | number | optional | — Letter spacing in em |
| `line_height` | number | optional | — Line-height multiplier (0 = automatic) |
| `max_width` | number | optional | — Wrap width in px (0 = no wrap) |
| `align` | enum["left"|"center"|"right"] | optional | — Alignment about the layer origin |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Where each word of a layer sits

  ```json
  {"layer":"Headline"}
  ```

- Size a string before creating it

  ```json
  {"text":"Ship faster","font_family":"Helvetica Neue","font_size":120,"font_weight":700}
  ```

Tips:

- Boxes are in the layer's own space (px from its origin, +y up); comp_center adds the layer position, so a highlight pill goes at comp_center with the word's width + padding.
- Build per-word layers (kinetic type, one word colored) from words[]: one text layer per word at its comp_center, same style, align center.

Related: `setTextStyle`, `createLayer`, `runScript`

CLI: `kinetic tool measureText --params '<json>' --workspace ./out`

## addTextAnimator

Append a range animator (full RangeAnimator JSON).

- family: `content` · mutating: `true` · transport: CLI `kinetic tool addTextAnimator` / SDK `batch([{tool: "addTextAnimator"}])` / MCP `tools/call {name: "addTextAnimator"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.addTextAnimator(...)` in runScript · inside runScript: yes
- signature: `addTextAnimator {animator: object, layer: string|int}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `animator` | object | required | — Per-character, per-word or per-line entrance (After Effects' range selector). Friendly form: every unit starts at `from` and springs to its resting pose, staggered left to right. Example: {"from":{"y":-40,"opacity":0},"start_frame":10,"stagger_frames":2,"spring":"snappy"}; add "by":"words" to move whole words. (The native RangeAnimator JSON with offset_* fields and based_on is also accepted.) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Characters rise and fade in

  ```json
  {"layer":"Headline","animator":{"from":{"y":-40,"opacity":0},"start_frame":8,"stagger_frames":2,"spring":"snappy"}}
  ```

- Scale pop, playful

  ```json
  {"layer":"Headline","animator":{"from":{"scale":0.6,"opacity":0},"start_frame":0,"stagger_frames":1,"spring":"bouncy"}}
  ```

- Word by word, one layer

  ```json
  {"layer":"Headline","animator":{"by":"words","from":{"y":-30,"opacity":0},"start_frame":0,"stagger_frames":5,"spring":"smooth"}}
  ```

- Line by line for a paragraph

  ```json
  {"layer":"Headline","animator":{"by":"lines","from":{"y":-24,"opacity":0},"stagger_frames":10}}
  ```

Tips:

- Premium text: stagger 1-2 frames, spring snappy, rise 20-60 px with a fade. More than ~4 frames of stagger reads as a typewriter.

Related: `setTextStyle`, `animateProperty`, `renderContactSheet`

CLI: `kinetic tool addTextAnimator --params '<json>' --workspace ./out`

## addEffect

Push a validated effect (params filled from defaults).

- family: `content` · mutating: `true` · transport: CLI `kinetic tool addEffect` / SDK `batch([{tool: "addEffect"}])` / MCP `tools/call {name: "addEffect"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.addEffect(...)` in runScript · inside runScript: yes
- signature: `addEffect {effect: string, layer: string|int, enabled?: bool = true, params?: object}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `effect` | string | required | — Effect id from listRenderCapabilities |
| `params` | object | optional | — Effect parameters, name -> value: numbers, booleans, hex colors ("#FFB300"), or [x, y] arrays; unspecified params keep their defaults. guide {topic:"effects"} lists every effect's params with units and ranges |
| `enabled` | boolean | optional default=true | — Effect on/off (default true) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Soft glow

  ```json
  {"layer":"Accent","effect":"glow","params":{"radius":24,"intensity":0.8}}
  ```

- Drop shadow (offset in screen px, y down)

  ```json
  {"layer":"Card","effect":"drop_shadow","params":{"offset":[0,18],"radius":36,"opacity":0.5,"color":"#000000"}}
  ```

- Film grain

  ```json
  {"layer":"Background","effect":"grain","params":{"amount":0.02,"size":2}}
  ```

- Wipe, ready to reveal (animate effect.linear_wipe.completion 1 -> 0)

  ```json
  {"layer":"Headline","effect":"linear_wipe","params":{"completion":1,"angle":90,"feather":60}}
  ```

- Outlined type

  ```json
  {"layer":"Headline","effect":"stroke","params":{"size":4,"color":"#FFFFFF","position":"outside"}}
  ```

Tips:

- Effect params: guide {topic:"effects"} lists every effect with units, defaults and ranges.

Related: `guide`, `inspectRenderGraph`, `createLayer`

CLI: `kinetic tool addEffect --params '<json>' --workspace ./out`

## addMask

Add a polygon mask.

- family: `content` · mutating: `true` · transport: CLI `kinetic tool addMask` / SDK `batch([{tool: "addMask"}])` / MCP `tools/call {name: "addMask"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.addMask(...)` in runScript · inside runScript: yes
- signature: `addMask {layer: string|int, points: number[][], closed?: bool = true, expansion?: number = 0 [px], feather?: number = 0 [px], inverted?: bool = false, mode?: add|subtract|intersect|difference, name?: string, opacity?: number = 1}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `name` | string | optional | — Mask name |
| `mode` | enum["add"|"subtract"|"intersect"|"difference"] | optional | — How the mask combines with earlier masks: add (default), subtract, intersect, difference |
| `points` | array | required | — 3+ [x,y] vertices, layer-local |
| `closed` | boolean | optional default=true | — Close the polygon (default true) |
| `inverted` | boolean | optional default=false | — Keep the outside instead of the inside (default false) |
| `opacity` | number | optional default=1 | — Mask strength 0..1 (default 1) |
| `feather` | number | optional default=0 | — Edge softness in px (default 0 = hard edge) |
| `expansion` | number | optional default=0 | — Grow (+) or shrink (-) the mask outline in px (default 0) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Reveal the top half (layer space, y up)

  ```json
  {"layer":"Card","points":[[-360,0],[360,0],[360,210],[-360,210]],"feather":12}
  ```

Related: `createLayer`, `renderFrame`

CLI: `kinetic tool addMask --params '<json>' --workspace ./out`

## setMatte

Set/clear a track matte from another layer.

- family: `content` · mutating: `true` · transport: CLI `kinetic tool setMatte` / SDK `batch([{tool: "setMatte"}])` / MCP `tools/call {name: "setMatte"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setMatte(...)` in runScript · inside runScript: yes
- signature: `setMatte {layer: string|int, matte?: string|null|int, type?: alpha|alpha-inverted|luma|luma-inverted = "alpha"}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `matte` | string\|null\|integer | optional | — Matte layer id/name, or null to clear |
| `type` | enum["alpha"|"alpha-inverted"|"luma"|"luma-inverted"] | optional default="alpha" | — alpha (default): show where the matte is opaque; alpha-inverted; luma: show where the matte is bright; luma-inverted |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Text as a window onto the card

  ```json
  {"layer":"Card","matte":"Headline","type":"alpha"}
  ```

- Clear

  ```json
  {"layer":"Card","matte":null}
  ```

Related: `createLayer`, `renderFrame`

CLI: `kinetic tool setMatte --params '<json>' --workspace ./out`

## setParticles

Set a particle layer's system: emit from shapes or a layer's art (dissolves), forces, swirl, colliders, sprites, palettes, trails. kit.particles/confetti/dissolve for common looks.

- family: `content` · mutating: `true` · transport: CLI `kinetic tool setParticles` / SDK `batch([{tool: "setParticles"}])` / MCP `tools/call {name: "setParticles"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setParticles(...)` in runScript · inside runScript: yes
- signature: `setParticles {layer: string|int, system: object, collide?: object}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `system` | object | required | — All optional. px, layer seconds, degrees (0 right, 90 up). guide particles |
| `collide` | object | optional | — Particles hit a layer's alpha (a person's cutout, a logo, any art): {matte: layer, threshold?: 0.5, bounce?: 0.35, friction?: 0.4, on_hit?: bounce|stick|die}. The layer is drawn alone each frame, opacity ignored (a hidden one still collides), so a cutout's nested layer works. Same as one more entry in system.colliders: [{matte: layer, ...}]. |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Upward embers

  ```json
  {"layer":"Sparks","system":{"emitter":{"line":{"length":900,"angle_deg":0}},"birth_rate":60,"lifetime":2.5,"speed":90,"direction_deg":90,"spread_deg":25,"gravity":[0,20],"flow":{"strength":60},"size":4,"color_over_life":[{"t":0,"color":"#FFB300"},{"t":1,"color":"#FF4D0000"}],"sprite":{"spark":{"length_scale":3}}}}
  ```

- Confetti that lands on the card

  ```json
  {"layer":"Sparks","system":{"emitter":{"rect":{"width":500,"height":20}},"birth_rate":0,"bursts":[{"time":0,"count":150}],"lifetime":4,"speed":250,"direction_deg":90,"spread_deg":70,"gravity":[0,-700],"drag":1,"size":14,"sprites":["confetti","square","triangle"],"tumble":1.5,"colors":["#FF5A5F","#3D5AFE","#FFC107","#22C55E"],"colliders":[{"layer":"Card","bounce":0.25,"friction":0.7}]}}
  ```

- The headline dissolves left to right into its own colours

  ```json
  {"layer":"Sparks","system":{"source":{"layer":"Headline","count":2500,"sweep":{"from":"left","start":0.5,"duration":1.2}},"lifetime":1.6,"speed":80,"direction_deg":60,"spread_deg":60,"drag":0.6,"flow":{"strength":90,"scale":160},"size":3,"opacity_over_life":[{"t":0,"v":1},{"t":1,"v":0}]}}
  ```

- Comets with trails

  ```json
  {"layer":"Sparks","system":{"birth_rate":8,"lifetime":2,"speed":400,"direction_deg":30,"spread_deg":20,"gravity":[0,-200],"size":8,"trail":{"length":0.3,"segments":10},"colors":["#FFFFFF","#7DD3FC"]}}
  ```

- Rain that runs off a shape: collide with any layer's alpha (a cutout's layer: rain on the person)

  ```json
  {"layer":"Sparks","system":{"emitter":{"line":{"length":1200,"angle_deg":0}},"birth_rate":120,"lifetime":2,"speed":600,"direction_deg":-90,"spread_deg":4,"size":3,"colors":["#BFD8FF"]},"collide":{"matte":"Card","bounce":0.2,"friction":0.6}}
  ```

- Rising stars, pulled into a swirl

  ```json
  {"layer":"Sparks","system":{"emitter":{"circle":{"radius":200}},"birth_rate":40,"lifetime":3,"speed":40,"sprite":"star","size":12,"angular_velocity":90,"attractors":[{"at":[0,0],"strength":300,"radius":250,"vortex":1.5}],"colors":["#FFD54F"]}}
  ```

Related: `createLayer`, `setCompositionField`

CLI: `kinetic tool setParticles --params '<json>' --workspace ./out`

## parentLayer

Set/clear a layer's parent (cycle-checked). Position stays local (a child at [0,0] sits on the parent's origin) unless keep_world: true keeps it where it is on screen (at its resting pose; frame measures another).

- family: `scene` · mutating: `true` · transport: CLI `kinetic tool parentLayer` / SDK `batch([{tool: "parentLayer"}])` / MCP `tools/call {name: "parentLayer"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.parentLayer(...)` in runScript · inside runScript: yes
- signature: `parentLayer {layer: string|int, frame?: number|string, keep_world?: bool = false, parent?: string|null|int}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `parent` | string\|null\|integer | optional | — Parent layer id/name, or null to clear |
| `keep_world` | boolean | optional default=false | — Keep the layer where it is on screen (its position and moves shift into the parent's space) |
| `frame` | number\|string | optional | — keep_world: measure at this frame or "in" (the layer's in point); default the resting poses |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Group under a null

  ```json
  {"layer":"Headline","parent":"Rig"}
  ```

- Unparent

  ```json
  {"layer":"Headline","parent":null}
  ```

CLI: `kinetic tool parentLayer --params '<json>' --workspace ./out`

## reorderLayer

Change a layer's paint order (index 0 = bottom, highest = top): give one of index, to (top|bottom), above or below (a layer).

- family: `scene` · mutating: `true` · transport: CLI `kinetic tool reorderLayer` / SDK `batch([{tool: "reorderLayer"}])` / MCP `tools/call {name: "reorderLayer"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.reorderLayer(...)` in runScript · inside runScript: yes
- signature: `reorderLayer {layer: string|int, above?: string|int, below?: string|int, index?: int, to?: top|bottom|front|back}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `index` | integer | optional | (min=0) — 0 = bottom (painted first); past the end = top |
| `to` | enum["top"|"bottom"|"front"|"back"] | optional | — top (front) or bottom (back) |
| `above` | string\|integer | optional | — Directly over this layer |
| `below` | string\|integer | optional | — Directly under this layer |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Send to the back (index 0 is the bottom; the highest index paints on top)

  ```json
  {"layer":"Background","to":"bottom"}
  ```

- Paint a layer directly over another

  ```json
  {"layer":"Headline","above":"Card"}
  ```

- Tuck a layer directly under another

  ```json
  {"layer":"Accent","below":"Card"}
  ```

- An exact paint index (0 = bottom)

  ```json
  {"layer":"Card","index":2}
  ```

CLI: `kinetic tool reorderLayer --params '<json>' --workspace ./out`

## duplicateLayer

Copy a layer (new id, same content).

- family: `scene` · mutating: `true` · transport: CLI `kinetic tool duplicateLayer` / SDK `batch([{tool: "duplicateLayer"}])` / MCP `tools/call {name: "duplicateLayer"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.duplicateLayer(...)` in runScript · inside runScript: yes
- signature: `duplicateLayer {layer: string|int, name?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `name` | string | optional | — Name for the copy (default: original name + " copy") |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Copy for a shadow

  ```json
  {"layer":"Card","name":"Card Shadow"}
  ```

CLI: `kinetic tool duplicateLayer --params '<json>' --workspace ./out`

## deleteLayer

Remove a layer by id or name.

- family: `scene` · mutating: `true` · transport: CLI `kinetic tool deleteLayer` / SDK `batch([{tool: "deleteLayer"}])` / MCP `tools/call {name: "deleteLayer"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.deleteLayer(...)` in runScript · inside runScript: yes
- signature: `deleteLayer {layer: string|int}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Remove

  ```json
  {"layer":"Accent"}
  ```

CLI: `kinetic tool deleteLayer --params '<json>' --workspace ./out`

## precompose

AE Pre-compose: move layers into a new composition shown by one layer in their place; the frame renders the same (links across the group are kept).

- family: `scene` · mutating: `true` · transport: CLI `kinetic tool precompose` / SDK `batch([{tool: "precompose"}])` / MCP `tools/call {name: "precompose"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.precompose(...)` in runScript · inside runScript: yes
- signature: `precompose {layers: string|int[], name: string, move_attributes?: bool = true, trim?: bool = false}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layers` | array | required | — Layer ids or names to move |
| `name` | string | required | — Name of the new composition and its layer |
| `move_attributes` | boolean | optional default=true | — false: one layer's content moves in; its transform, effects, masks and matte stay outside (AE: leave all attributes) |
| `trim` | boolean | optional default=false | — Only as long as the layers |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Group a card and its label so one effect and one move apply to both

  ```json
  {"layers":["Headline","Accent"],"name":"Title group"}
  ```

- Precompose one layer, keeping its motion and effects outside (AE: leave all attributes)

  ```json
  {"layers":["Accent"],"name":"Accent art","move_attributes":false}
  ```

Related: `listCompositions`, `addEffect`, `animateProperty`, `undo`

CLI: `kinetic tool precompose --params '<json>' --workspace ./out`

## setLayout

Auto layout (flexbox): a null, shape or solid places its children in a row, column or grid every frame; text re-flows and a shape container hugs its content. Child positions become offsets on their slots.

- family: `scene` · mutating: `true` · transport: CLI `kinetic tool setLayout` / SDK `batch([{tool: "setLayout"}])` / MCP `tools/call {name: "setLayout"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setLayout(...)` in runScript · inside runScript: yes
- signature: `setLayout {layer: string|int, align?: start|center|end|stretch = "center", children?: string|int[], direction?: row|column = "row", frame?: number = 0 [frame], gap?: number [px], height?: number|null, justify?: start|center|end|space-between|space-around|space-evenly = "start", line_gap?: number [px], padding?: any, remove?: bool = false, resize?: bool = true, width?: number|null, wrap?: bool = false}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | — Container: null, shape or solid |
| `children` | array | optional | — Layers to place, in flow order (parented; their position moves become offsets from the slot) |
| `direction` | enum["row"|"column"] | optional default="row" | — row: left to right; column: top down |
| `gap` | number | optional | (min=0) — px between children (layout.gap) |
| `padding` | any | optional | — px: n, [v, h] or [t, r, b, l] (layout.padding) |
| `align` | enum["start"|"center"|"end"|"stretch"] | optional default="center" | — Across the flow; stretch fills it (text wraps) |
| `justify` | enum["start"|"center"|"end"|"space-between"|"space-around"|"space-evenly"] | optional default="start" | — Free space along a fixed size |
| `wrap` | boolean | optional default=false | — Wrap into lines (a grid; needs a fixed size) |
| `width` | number\|null | optional | — Fixed px; null hugs |
| `height` | number\|null | optional | — Fixed px; null hugs |
| `line_gap` | number | optional | (min=0) — px between lines (default gap) |
| `resize` | boolean | optional default=true | — Shape/solid container takes the box size |
| `frame` | number | optional default=0 | — Frame the result measures |
| `remove` | boolean | optional default=false | — Turn auto layout off |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- A card that hugs its copy (a column with padding; text wraps to its width)

  ```json
  {"layer":"Card","children":["Headline"],"direction":"column","padding":[40,56],"width":720,"align":"stretch"}
  ```

- Spread a row across a fixed width

  ```json
  {"layer":"Menu","width":600,"justify":"space-between"}
  ```

- A wrapping grid

  ```json
  {"layer":"Rig","children":["Accent","Card"],"wrap":true,"width":900,"gap":24,"align":"start"}
  ```

- Animate the gap after setting it up

  ```json
  {"layer":"Menu","gap":0}
  ```

Related: `setLayoutItem`, `animateProperty`, `measureText`, `critiqueLayout`

CLI: `kinetic tool setLayout --params '<json>' --workspace ./out`

## setLayoutItem

One auto layout child: out of the flow (absolute), a size to lay out with, grow, or its own alignment.

- family: `scene` · mutating: `true` · transport: CLI `kinetic tool setLayoutItem` / SDK `batch([{tool: "setLayoutItem"}])` / MCP `tools/call {name: "setLayoutItem"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setLayoutItem(...)` in runScript · inside runScript: yes
- signature: `setLayoutItem {layer: string|int, absolute?: bool, align?: start|center|end|stretch|null, frame?: number = 0 [frame], grow?: number, height?: number|null, width?: number|null}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | — A child of a setLayout container |
| `absolute` | boolean | optional | — Placed by its own position |
| `width` | number\|null | optional | — px to lay out with (null: measured) |
| `height` | number\|null | optional | — px to lay out with (null: measured) |
| `grow` | number | optional | (min=0) — Share of free space (flex-grow) |
| `align` | enum["start"|"center"|"end"|"stretch"|null] | optional | — align-self (null: the container's) |
| `frame` | number | optional default=0 | — Frame the result measures |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Pin a child out of the flow (placed by its own position)

  ```json
  {"layer":"Item B","absolute":true}
  ```

- Take the free space along the flow

  ```json
  {"layer":"Item A","grow":1}
  ```

- Align one child differently

  ```json
  {"layer":"Item A","align":"start"}
  ```

Related: `setLayout`, `setTextContent`

CLI: `kinetic tool setLayoutItem --params '<json>' --workspace ./out`

## listLayers

Layer summaries (id, name, kind, enabled).

- family: `scene` · mutating: `false` · transport: CLI `kinetic tool listLayers` / SDK `batch([{tool: "listLayers"}])` / MCP `tools/call {name: "listLayers"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.listLayers(...)` in runScript or query · inside runScript: yes
- signature: `listLayers {}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |

Example: `{}`

CLI: `kinetic tool listLayers --params '<json>' --workspace ./out`

## inspectLayer

Full layer JSON (transform, content, anims, effects, masks), its paint index, and with frame its on-screen bounds (a null: everything parented under it).

- family: `scene` · mutating: `false` · transport: CLI `kinetic tool inspectLayer` / SDK `batch([{tool: "inspectLayer"}])` / MCP `tools/call {name: "inspectLayer"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.inspectLayer(...)` in runScript or query · inside runScript: yes
- signature: `inspectLayer {layer: string|int, frame?: number [frame]}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `frame` | number | optional | — Also report bounds at this frame |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Everything about a layer

  ```json
  {"layer":"Headline"}
  ```

CLI: `kinetic tool inspectLayer --params '<json>' --workspace ./out`

## sceneGraph

Parenting forest + matte edges for a composition.

- family: `scene` · mutating: `false` · transport: CLI `kinetic tool sceneGraph` / SDK `batch([{tool: "sceneGraph"}])` / MCP `tools/call {name: "sceneGraph"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.sceneGraph(...)` in runScript or query · inside runScript: yes
- signature: `sceneGraph {}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |

Example: `{}`

CLI: `kinetic tool sceneGraph --params '<json>' --workspace ./out`

## importAsset

Validate and register a workspace file as an asset (dedup by kind and fingerprint).

- family: `content` · mutating: `true` · transport: CLI `kinetic tool importAsset` / SDK `batch([{tool: "importAsset"}])` / MCP `tools/call {name: "importAsset"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.importAsset(...)` in runScript · inside runScript: yes
- signature: `importAsset {kind: image|video|audio|font|lut|vector|data|model, path: string, name?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `path` | string | required | — Workspace-relative file |
| `kind` | enum["image"|"video"|"audio"|"font"|"lut"|"vector"|"data"|"model"] | required | — image (png/jpg/webp), video, audio (wav/mp3/aac/flac), font (ttf/otf), lut (.cube), vector (svg), data (json), model (glTF 2.0: .glb, or .gltf with its files) |
| `name` | string | optional | — Display name for the asset |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Register an image

  ```json
  {"path":"photo.png","kind":"image","name":"hero-photo"}
  ```

- Register a 3D model (lists its animations and size)

  ```json
  {"path":"cube.glb","kind":"model","name":"product"}
  ```

Related: `createLayer`, `inspectAsset`, `findAssetsByTag`

CLI: `kinetic tool importAsset --params '<json>' --workspace ./out`

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

## importFont

Add a Google Fonts family by name ("Inter Tight 600"): cached in ~/.miran/fonts (network only the first time), licence recorded. Then setTextStyle {font_family, font_weight}. {list: true, query?} names the installed families instead (offline).

- family: `assets` · mutating: `true` · transport: CLI `kinetic tool importFont` / SDK `batch([{tool: "importFont"}])` / MCP `tools/call {name: "importFont"}`
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: list (installed families) and families already downloaded
- signature: `importFont {family?: string, italic?: bool = false, limit?: int, list?: bool = false, query?: string, weights?: int[]}`

| param | type | required/default | notes |
|---|---|---|---|
| `family` | string | optional | — Family, optionally with a weight: "Inter Tight 600" (required unless list) |
| `weights` | array | optional | — Default [400, 700] or the named weight |
| `italic` | boolean | optional default=false | — Also the italics |
| `list` | boolean | optional default=false | — Name the installed families (offline) instead of importing |
| `query` | string | optional | — With list: a name or style (rounded, mono, serif, condensed, script) |
| `limit` | integer | optional | (min=1, max=400) — With list: max names (60) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- A Google font by name and weight

  ```json
  {"family":"Inter Tight 600"}
  ```

- Several weights of a family

  ```json
  {"family":"Inter","weights":[400,700]}
  ```

- Which installed families are rounded (they work offline; no download)

  ```json
  {"list":true,"query":"rounded"}
  ```

Tips:

- Installed families (guide fonts) work offline and need no import: importFont {list: true, query: "rounded"} names them (query: a name or a style word: rounded, mono, serif, condensed, grotesk, script).
- A family that is not installed or cached needs the network. When the font service is unreachable the error says so and lists installed look-alikes; the family name is usually fine.

CLI: `kinetic tool importFont --params '<json>' --workspace ./out`

## importAssetFolder

Validate and register renderable folder files as project assets (dedup by fingerprint), tagged with kind/role. Skips unsupported or malformed files with reasons.

- family: `assets` · mutating: `true` · transport: CLI `kinetic tool importAssetFolder` / SDK `batch([{tool: "importAssetFolder"}])` / MCP `tools/call {name: "importAssetFolder"}`
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `importAssetFolder {path: string, recursive?: bool = true}`

| param | type | required/default | notes |
|---|---|---|---|
| `path` | string | required | — Workspace-relative folder |
| `recursive` | boolean | optional default=true | — Include subfolders (default true) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Import everything usable

  ```json
  {"path":"."}
  ```

CLI: `kinetic tool importAssetFolder --params '<json>' --workspace ./out`

## scanAssetFolder

List files in a workspace folder with kind classification (vector/raster/video/audio/font/lut/manifest/unknown).

- family: `assets` · mutating: `false` · transport: CLI `kinetic tool scanAssetFolder` / SDK `batch([{tool: "scanAssetFolder"}])` / MCP `tools/call {name: "scanAssetFolder"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.scanAssetFolder(...)` in runScript or query · inside runScript: yes
- signature: `scanAssetFolder {path: string, recursive?: bool = true}`

| param | type | required/default | notes |
|---|---|---|---|
| `path` | string | required | — Workspace-relative folder |
| `recursive` | boolean | optional default=true | — Include subfolders (default true) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- What is in the folder

  ```json
  {"path":"."}
  ```

CLI: `kinetic tool scanAssetFolder --params '<json>' --workspace ./out`

## inspectAsset

Asset record + live file metadata (dims, streams).

- family: `content` · mutating: `false` · transport: CLI `kinetic tool inspectAsset` / SDK `batch([{tool: "inspectAsset"}])` / MCP `tools/call {name: "inspectAsset"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.inspectAsset(...)` in runScript or query · inside runScript: yes
- signature: `inspectAsset {asset: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `asset` | string | required | — Asset id |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Details

  ```json
  {"asset":"photo"}
  ```

CLI: `kinetic tool inspectAsset --params '<json>' --workspace ./out`

## inspectAssetFolder

Deep per-file metadata: dimensions, duration, alpha, vector groups/nodes, colors, fonts, LUT size.

- family: `assets` · mutating: `false` · transport: CLI `kinetic tool inspectAssetFolder` / SDK `batch([{tool: "inspectAssetFolder"}])` / MCP `tools/call {name: "inspectAssetFolder"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.inspectAssetFolder(...)` in runScript or query · inside runScript: yes
- signature: `inspectAssetFolder {path: string, recursive?: bool = true}`

| param | type | required/default | notes |
|---|---|---|---|
| `path` | string | required | — Workspace-relative folder |
| `recursive` | boolean | optional default=true | — Include subfolders (default true) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Deep metadata

  ```json
  {"path":".","recursive":false}
  ```

CLI: `kinetic tool inspectAssetFolder --params '<json>' --workspace ./out`

## inferAssetRoles

Semantic role per file (symbol, wordmark, background, screen, product, shadow, highlight, cta, footage, music, sfx, audio, font, lut, data, unknown) with confidence + reasons.

- family: `assets` · mutating: `false` · transport: CLI `kinetic tool inferAssetRoles` / SDK `batch([{tool: "inferAssetRoles"}])` / MCP `tools/call {name: "inferAssetRoles"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.inferAssetRoles(...)` in runScript or query · inside runScript: yes
- signature: `inferAssetRoles {path: string, recursive?: bool = true}`

| param | type | required/default | notes |
|---|---|---|---|
| `path` | string | required | — Workspace-relative folder |
| `recursive` | boolean | optional default=true | — Include subfolders (default true) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Guess roles

  ```json
  {"path":"."}
  ```

CLI: `kinetic tool inferAssetRoles --params '<json>' --workspace ./out`

## findAssetsByTag

Find project assets by tag, role, kind, name, or id substring (case-insensitive).

- family: `assets` · mutating: `false` · transport: CLI `kinetic tool findAssetsByTag` / SDK `batch([{tool: "findAssetsByTag"}])` / MCP `tools/call {name: "findAssetsByTag"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.findAssetsByTag(...)` in runScript or query · inside runScript: yes
- signature: `findAssetsByTag {tag: string, kind?: string, role?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `tag` | string | required | — Tag substring to match (case-insensitive) |
| `kind` | string | optional | — Only assets of this kind (image, video, audio, font, lut, vector, data) |
| `role` | string | optional | — Role filter (e.g. wordmark, background, product, music) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Find audio

  ```json
  {"tag":"music"}
  ```

CLI: `kinetic tool findAssetsByTag --params '<json>' --workspace ./out`

## buildAssetComposition

Create a composition from a folder with editable SVG layers, role-ordered visual layers, and explicit import reports.

- family: `assets` · mutating: `true` · transport: CLI `kinetic tool buildAssetComposition` / SDK `batch([{tool: "buildAssetComposition"}])` / MCP `tools/call {name: "buildAssetComposition"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.buildAssetComposition(...)` in runScript · inside runScript: yes
- signature: `buildAssetComposition {path: string, duration_frames?: number [frames], height?: number [px], name?: string, recursive?: bool = true, svg_mode?: string = "explode_top_level_group…}, text_policy?: string = "keep_text", width?: number [px]}`

| param | type | required/default | notes |
|---|---|---|---|
| `path` | string | required | — Workspace-relative folder |
| `name` | string | optional | — Composition id |
| `width` | number | optional | — Composition width in px (default: from the assets) |
| `height` | number | optional | — Composition height in px |
| `duration_frames` | number | optional | — Composition length in frames |
| `recursive` | boolean | optional default=true | — Include subfolders (default true) |
| `svg_mode` | string | optional default="explode_top_level_groups" | — SVG split mode; preserve_groups keeps one editable vector tree |
| `text_policy` | string | optional default="keep_text" | — keep_text or outline for SVG text |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Comp from a brand folder

  ```json
  {"path":".","name":"brand","width":1920,"height":1080,"duration_frames":150}
  ```

CLI: `kinetic tool buildAssetComposition --params '<json>' --workspace ./out`

## undo

Step back one commit.

- family: `project` · mutating: `true` · transport: CLI `kinetic tool undo` / SDK `batch([{tool: "undo"}])` / MCP `tools/call {name: "undo"}`
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `undo {}`

No parameters.

Example: `{}`

CLI: `kinetic tool undo --params '<json>' --workspace ./out`

## redo

Step forward one commit.

- family: `project` · mutating: `true` · transport: CLI `kinetic tool redo` / SDK `batch([{tool: "redo"}])` / MCP `tools/call {name: "redo"}`
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `redo {}`

No parameters.

Example: `{}`

CLI: `kinetic tool redo --params '<json>' --workspace ./out`

## createCheckpoint

Name the current state for later restore.

- family: `project` · mutating: `true` · transport: CLI `kinetic tool createCheckpoint` / SDK `batch([{tool: "createCheckpoint"}])` / MCP `tools/call {name: "createCheckpoint"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.createCheckpoint(...)` in runScript · inside runScript: yes
- signature: `createCheckpoint {name: string, note?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `name` | string | required | — Checkpoint name |
| `note` | string | optional | — Free-text note stored with the checkpoint |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Mark an approved state

  ```json
  {"name":"layout-approved","note":"before camera work"}
  ```

CLI: `kinetic tool createCheckpoint --params '<json>' --workspace ./out`

## restoreCheckpoint

Restore a checkpoint (itself undoable).

- family: `project` · mutating: `true` · transport: CLI `kinetic tool restoreCheckpoint` / SDK `batch([{tool: "restoreCheckpoint"}])` / MCP `tools/call {name: "restoreCheckpoint"}`
- history: creates an undo step
- lean tool list: listed (a direct call) · inside runScript: none
- signature: `restoreCheckpoint {name: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `name` | string | required | — Checkpoint name |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Go back to it

  ```json
  {"name":"v1"}
  ```

CLI: `kinetic tool restoreCheckpoint --params '<json>' --workspace ./out`

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

## listTools

List every tool with its JSON schema.

- family: `project` · mutating: `false` · transport: CLI `kinetic tool listTools` / SDK `batch([{tool: "listTools"}])` / MCP `tools/call {name: "listTools"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.listTools(...)` in runScript or query · inside runScript: yes
- signature: `listTools {family?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `family` | string | optional | — Filter: project, scene, props, content, design, render |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Full schemas for one family

  ```json
  {"family":"camera"}
  ```

CLI: `kinetic tool listTools --params '<json>' --workspace ./out`

