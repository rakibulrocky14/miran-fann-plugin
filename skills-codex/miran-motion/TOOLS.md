# Animation and timing — tool reference (generated)

Source: the runtime registry via `kinetic schemas` — do not hand-edit; run `bun scripts/gen-docs.ts`.
19 of 181 tools — the ones this skill teaches. The full registry lives in docs/TOOLS.md.

## setProperty

Set a property's static value (canonical path).

- family: `props` · mutating: `true` · transport: CLI `kinetic tool setProperty` / SDK `batch([{tool: "setProperty"}])` / MCP `tools/call {name: "setProperty"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setProperty(...)` in runScript · inside runScript: yes
- signature: `setProperty {layer: string|int, path: string, value: any}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `path` | string | required | — e.g. transform.position.x |
| `value` | any | required | — Tagged value or bare number |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Static value

  ```json
  {"layer":"Accent","path":"transform.rotation.z","value":15}
  ```

- Opacity

  ```json
  {"layer":"Accent","path":"transform.opacity","value":0.6}
  ```

- Leave a stroke 70% drawn (a gauge)

  ```json
  {"layer":"Card","path":"trim.end","value":0.7}
  ```

- Resting colour

  ```json
  {"layer":"Card","path":"fill.color","value":"#3D5AFE"}
  ```

Related: `setPropertyDriver`, `animateProperty`

CLI: `kinetic tool setProperty --params '<json>' --workspace ./out`

## setPropertyDriver

Install/replace a property driver (keyframes, expression, link, spring, noise).

- family: `props` · mutating: `true` · transport: CLI `kinetic tool setPropertyDriver` / SDK `batch([{tool: "setPropertyDriver"}])` / MCP `tools/call {name: "setPropertyDriver"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setPropertyDriver(...)` in runScript · inside runScript: yes
- signature: `setPropertyDriver {driver: object, layer: string|int, path: string, replace?: bool = false}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `path` | string | required | — transform.position.x|y|z, transform.rotation.x|y|z, transform.scale.x|y|z, transform.opacity, transform.anchor.x|y; fill.color, stroke.color, stroke.width, shape.width|height|radius, text.size|tracking|line_height|weight, effect.<effect>.<param>; trim.start|end|offset; text.value; text.source (the words, for expressions). Short forms (position.x, color, blur) work; expressions also take AE vectors (transform.position, scale, anchorPoint) on both axes |
| `driver` | object | required | — Exactly ONE of these keys. keyframes: multi-key animation. spring: physical settle from `from` to the property's static value. noise: organic wobble. expression: math on time/other properties. link: copy another property. relative: offset an inner driver. static: remove animation |
| `replace` | boolean | optional default=false | — Replace a different active driver |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Multi-key move (frames, px, per-segment ease)

  ```json
  {"layer":"Accent","path":"transform.position.x","driver":{"keyframes":{"keys":[{"frame":0,"value":-420,"ease":"smooth"},{"frame":30,"value":0,"ease":"hold"},{"frame":60,"value":0,"ease":"emphasized-accelerate"},{"frame":80,"value":420}]}}}
  ```

- Gentle float (a sine bob: noise this slow creeps through its lattice points)

  ```json
  {"layer":"Accent","path":"transform.position.y","driver":{"expression":{"source":"Math.sin(time * 2.5) * 14"}}}
  ```

- Physical settle to the static value

  ```json
  {"layer":"Accent","path":"transform.scale.x","driver":{"spring":{"from":0.5,"start_frame":0,"spring":"bouncy"}}}
  ```

- Expression

  ```json
  {"layer":"Accent","path":"transform.rotation.z","driver":{"expression":{"source":"wiggle(2, 10)"}}}
  ```

- Loop the keys already on the property

  ```json
  {"layer":"Accent","path":"transform.rotation.z","driver":{"expression":{"source":"loopOut(\"pingpong\")"}}}
  ```

- AE position expression on both axes, in AE units

  ```json
  {"layer":"Accent","path":"transform.position","driver":{"expression":{"source":"[thisComp.width / 2, 120 + Math.sin(time * 3) * 20]","ae_units":true}}}
  ```

- Counter text (Source Text)

  ```json
  {"layer":"Headline","path":"text.source","driver":{"expression":{"source":"Math.round(linear(time, 0, 2, 0, 1250)) + \" users\""}}}
  ```

- Copy another layer's value every frame (composition defaults to this layer's)

  ```json
  {"layer":"Accent","path":"transform.position.y","driver":{"link":{"target":{"layer":"Card","path":"transform.position.y"}}}}
  ```

Tips:

- Use it for 3+ keyframes, holds, loops, springs to a value, noise and expressions. A driver on a path overrides animateProperty moves on the same path.
- A key's ease shapes the segment that STARTS at that key.

Related: `animateProperty`, `setProperty`, `getProperty`, `validateExpression`

CLI: `kinetic tool setPropertyDriver --params '<json>' --workspace ./out`

## animateProperty

Animate one property from A to B with an ease or spring. Almost everything animates, like CSS: transform, colours, size, corner radius, stroke, type size and tracking, any effect param (blur-in, glow). Omit from to start from the current value. Chain moves with later start frames; layer "camera" animates the camera.

- family: `props` · mutating: `true` · transport: CLI `kinetic tool animateProperty` / SDK `batch([{tool: "animateProperty"}])` / MCP `tools/call {name: "animateProperty"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.animateProperty(...)` in runScript · inside runScript: yes
- signature: `animateProperty {layer: string|int, property: string, arc?: number, by?: any, duration_frames?: number [frames], ease?: linear|arrive|depart|glide|easy-ease|smooth|camera|snap|…, from?: any, handoff?: bool = true, interp?: string|object, join?: bool = true, keyframes?: any[], orient?: boolean|number, replace?: bool = false, start_frame?: number = 0 [frame], to?: any, v0?: number, value_interp?: linear|log, via?: number[][]}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | — Layer name/id, or "camera" for comp camera |
| `property` | string | required | — position (to:[x,y], on a curve), position.x|y|z (px, y up), rotation, scale, opacity; fill.color, stroke.color, stroke.width, shape.width|height|radius, text.size|tracking|weight, effect.<effect>.<param> (blur, glow shortcuts); op.<type>.<param> (shape operators); puppet.<pin>.x|y|rotation|scale (puppet pins); trim.end, text.value; procedural.<param>; camera: dolly.x|y|z, crane.pitch|yaw|roll, focal_length |
| `from` | any | optional | — Start value in the property's units (px, degrees, 1 = 100%, 0..1 opacity, em tracking); "#RRGGBB" for colours, [x, y] for offsets. Omit to start from the current value |
| `to` | any | optional | — End value, same units as from |
| `by` | any | optional | — Instead of to: move relative to the value at start_frame (number, or [dx, dy] for position; camera: one number) |
| `arc` | number | optional | — position: bow the move into an arc, as a fraction of its length (0.15-0.3 reads natural; + bows left of travel, - right) |
| `via` | array | optional | — position: [[x,y], ...] points the move curves through |
| `orient` | boolean\|number | optional | — position: turn the layer along the path (true, or an extra angle in degrees) |
| `start_frame` | number | optional default=0 | — Frame the move starts (default 0); before it the layer holds `from` of the earliest move |
| `duration_frames` | number | optional | — Move length in frames (e.g. 18 = 0.6 s at 30 fps) |
| `interp` | string\|object | optional | — Easing: an ease name (as ease), {"bezier":[x1,y1,x2,y2]} or {"spring":"bouncy"|{"duration":0.5,"bounce":0.2}}. Omitted, the move is ease-in-out. interp: "log" (or value_interp) spaces scale by ratio, timing from ease. A spring preset settles over duration_frames; an explicit {duration, bounce} keeps its own duration |
| `value_interp` | enum["linear"|"log"] | optional | — Value spacing: linear (default) or log (constant zoom rate on scale; same as interp: "log" with ease) |
| `replace` | boolean | optional default=false | — Remove this property's moves that overlap the new one (retime without clearAnimation) |
| `handoff` | boolean | optional default=true | — Leave at the speed the property already has; false starts from rest |
| `join` | boolean | optional default=true | — Pass a shared end point at one speed; false stops at each |
| `v0` | number | optional | — Incoming speed (units/frame) instead of the measured one |
| `keyframes` | array | optional | — Instead of from/to/start_frame/duration_frames: [{frame, value, ease}] or [[frame, value], ...]; chained as one move per pair (ease of the earlier key; none = standard, so ease linear for measured curves). setPropertyDriver keyframes for holds and loops (no ease = auto) |
| `ease` | string | optional | — Easing name (alias of interp as a string; spring:<preset> too). Omitted, a single move is ease-in-out (leaves and lands at rest). arrive: a visible layer gliding into place; smooth and emphasized-decelerate: strong ease-outs (most of the move in the first third) for entrances that begin hidden. See guide topic=motion |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Rise 60 px into place

  ```json
  {"layer":"Headline","property":"position.y","from":-60,"to":0,"start_frame":0,"duration_frames":24,"ease":"arrive"}
  ```

- Fade in

  ```json
  {"layer":"Headline","property":"opacity","from":0,"to":1,"start_frame":0,"duration_frames":15,"ease":"standard"}
  ```

- Move up 120 px from wherever it is (relative)

  ```json
  {"layer":"Accent","property":"position.y","by":120,"start_frame":30,"duration_frames":20,"ease":"standard"}
  ```

- Springy scale pop

  ```json
  {"layer":"Accent","property":"scale","from":0.6,"to":1,"start_frame":10,"duration_frames":30,"ease":"spring:bouncy"}
  ```

- Camera push via the camera channel

  ```json
  {"layer":"camera","property":"dolly.z","from":-2250,"to":-2000,"start_frame":0,"duration_frames":150,"ease":"camera"}
  ```

- Draw a stroke on (trim paths; the shape needs a stroke)

  ```json
  {"layer":"Card","property":"trim.end","from":0,"to":1,"start_frame":0,"duration_frames":30,"ease":"emphasized-decelerate"}
  ```

- Count a number up (text layer)

  ```json
  {"layer":"Headline","property":"text.value","from":0,"to":1200,"start_frame":0,"duration_frames":45,"ease":"emphasized-decelerate"}
  ```

- Colour shift from the current colour

  ```json
  {"layer":"Card","property":"fill.color","to":"#FF4D00","start_frame":20,"duration_frames":12,"ease":"standard"}
  ```

- Blur in (adds a gaussian_blur if the layer has none)

  ```json
  {"layer":"Headline","property":"blur","from":30,"to":0,"start_frame":0,"duration_frames":18,"ease":"emphasized-decelerate"}
  ```

- A card that widens and rounds

  ```json
  {"layer":"Card","property":"shape.width","from":120,"to":720,"start_frame":0,"duration_frames":24,"ease":"spring:snappy"}
  ```

- Type that tightens as it lands

  ```json
  {"layer":"Headline","property":"text.tracking","from":0.25,"to":-0.02,"start_frame":0,"duration_frames":30,"ease":"emphasized-decelerate"}
  ```

- Glow swells on a hit

  ```json
  {"layer":"Accent","property":"effect.glow.intensity","from":0,"to":1.5,"start_frame":30,"duration_frames":8,"ease":"standard"}
  ```

- Raise a puppet pin (setLayerField puppet first)

  ```json
  {"layer":"Arm","property":"puppet.hand.y","to":120,"start_frame":0,"duration_frames":18,"ease":"smooth"}
  ```

- Shadow lifts (offset [x, y])

  ```json
  {"layer":"Card","property":"effect.drop_shadow.offset","from":[0,4],"to":[0,24],"start_frame":0,"duration_frames":20,"ease":"standard"}
  ```

- Swing into place on an arc (both axes, one move)

  ```json
  {"layer":"Accent","property":"position","to":[300,-120],"arc":0.25,"start_frame":0,"duration_frames":26,"ease":"glide"}
  ```

- Fly through a point, turning with the path

  ```json
  {"layer":"Accent","property":"position","from":[-600,-200],"to":[600,200],"via":[[0,260]],"orient":true,"start_frame":0,"duration_frames":40,"ease":"ease-in-out"}
  ```

- Wind up, then go

  ```json
  {"layer":"Card","property":"position.x","to":400,"start_frame":10,"duration_frames":24,"ease":"anticipate"}
  ```

- Retime a move (replaces the overlapping one)

  ```json
  {"layer":"Headline","property":"opacity","from":0,"to":1,"start_frame":14,"duration_frames":10,"ease":"standard","replace":true}
  ```

- Music swells in under the intro (audio layer volume, dB)

  ```json
  {"layer":"Music","property":"volume","from":-18,"to":0,"start_frame":0,"duration_frames":90,"ease":"smooth"}
  ```

Tips:

- Animatable names: position.x|y|z, rotation.x|y|z (degrees; rotation = z), scale (uniform), scale.x|y|z, opacity; the look: fill.color and stroke.color (hex), fill.opacity, stroke.width, stroke.opacity, shape.width, shape.height, shape.radius (corner radius), shape.inner_radius, text.size, text.tracking (em), text.line_height; any effect parameter as effect.<effect>.<param> (added if missing; shortcuts blur, glow, saturation, exposure, hue); trim.start|trim.end|trim.offset (draw strokes on, shapes and SVG layers), text.value (a counter); procedural.<param>. For the camera: dolly.x|y|z, crane.pitch|yaw|roll, focal_length.
- Motion design is more than transforms: blur 20 -> 0 with a rise, tracking 0.2 -> 0 as a word lands, a colour flip on the beat, a card that grows from a pill.
- Several moves on one property chain: each takes over at its start_frame, so an entrance and a later exit are two calls. Before its first move a property holds that move's `from`: start a flash or a pop with the layer's in_frame, not frames earlier.
- To retime, pass replace: true (the overlapping move on that property goes) or clearAnimation first; a new move on the same start frame otherwise leaves the old one stored underneath.
- volume (audio.gain_db, dB) animates an audio layer: swell a bed in, duck it under a hit, drop it before the beat lands.
- Values are absolute (to = final value), not offsets.

Related: `clearAnimation`, `setPropertyDriver`, `addTextAnimator`, `motionSummary`, `renderContactSheet`

CLI: `kinetic tool animateProperty --params '<json>' --workspace ./out`

## clearAnimation

Remove moves (animateProperty channels and curved paths) and drivers from a layer: one property (position, scale, fill.color, effect.glow.intensity, ...) or all, optionally only moves starting in [from_frame, to_frame). Use it to retime or undo a move; the property then rests at its base value.

- family: `props` · mutating: `true` · transport: CLI `kinetic tool clearAnimation` / SDK `batch([{tool: "clearAnimation"}])` / MCP `tools/call {name: "clearAnimation"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.clearAnimation(...)` in runScript · inside runScript: yes
- signature: `clearAnimation {layer: string|int, from_frame?: number [frame], property?: string, to_frame?: number [frame]}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | — Layer name/id, or "camera" |
| `property` | string | optional | — Omit for every property. A group clears its parts: position (x, y, z and paths), scale, rotation, fill.color |
| `from_frame` | number | optional | — Only moves starting at or after this frame |
| `to_frame` | number | optional | — Only moves starting before this frame |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Drop every move on one property

  ```json
  {"layer":"Headline","property":"position"}
  ```

- Remove the moves that start after frame 60

  ```json
  {"layer":"Headline","from_frame":60}
  ```

Related: `animateProperty`, `motionSummary`, `critiqueMotion`

CLI: `kinetic tool clearAnimation --params '<json>' --workspace ./out`

## editKeyframes

Move, change, add or delete one property's keys in place: animateProperty moves (keys = each move's ends), curved position moves (property position: keys = ends) and keyframe drivers. Edits name a key by frame: {frame,to} moves, {frame,value} sets, {frame,delete:true}, {frame,add:true} (splits a move, keeping the motion).

- family: `props` · mutating: `true` · transport: CLI `kinetic tool editKeyframes` / SDK `batch([{tool: "editKeyframes"}])` / MCP `tools/call {name: "editKeyframes"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.editKeyframes(...)` in runScript · inside runScript: yes
- signature: `editKeyframes {edits: object[], layer: string|int, property: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | — Layer name/id, or "camera" |
| `property` | string | required | — As inspectLayer names it: transform.position.x, opacity, fill.color (parts move together), ... |
| `edits` | array | required | — Edits applied in order, each naming a key by its frame |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Land the headline later (move its end key)

  ```json
  {"layer":"Headline","property":"transform.position.y","edits":[{"frame":24,"to":36}]}
  ```

- A key mid-move that keeps the motion, then a new value on it

  ```json
  {"layer":"Headline","property":"position.y","edits":[{"frame":12,"add":true},{"frame":12,"value":-10}]}
  ```

- Delete the start key

  ```json
  {"layer":"Headline","property":"position.y","edits":[{"frame":0,"delete":true}]}
  ```

Related: `animateProperty`, `motionSummary`, `shiftLayerTime`, `clearAnimation`

CLI: `kinetic tool editKeyframes --params '<json>' --workspace ./out`

## shiftLayerTime

Move a layer in time, keys and all: in/out, moves, keyframes, mask keys, text animators and time remap shift together, and so do the sounds that score only it.

- family: `props` · mutating: `true` · transport: CLI `kinetic tool shiftLayerTime` / SDK `batch([{tool: "shiftLayerTime"}])` / MCP `tools/call {name: "shiftLayerTime"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.shiftLayerTime(...)` in runScript · inside runScript: yes
- signature: `shiftLayerTime {frames: number [frames], layer: string|int}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `frames` | number | required | — Frames to move by (negative = earlier) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Start the headline half a second later, moves and all

  ```json
  {"layer":"Headline","frames":15}
  ```

Related: `setLayerField`, `editKeyframes`, `animateProperty`

CLI: `kinetic tool shiftLayerTime --params '<json>' --workspace ./out`

## getProperty

Static value, driver, and sampled value at a frame.

- family: `props` · mutating: `false` · transport: CLI `kinetic tool getProperty` / SDK `batch([{tool: "getProperty"}])` / MCP `tools/call {name: "getProperty"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.getProperty(...)` in runScript or query · inside runScript: yes
- signature: `getProperty {layer: string|int, path: string, frame?: number = 0 [frame]}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |
| `path` | string | required | — transform.position.x, fill.color, ...; transform.position (or scale, rotation, anchor) gives [x, y, z] |
| `frame` | number | optional default=0 | — Frame to sample at (default 0) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Sample a property

  ```json
  {"layer":"Accent","path":"transform.position.y","frame":30}
  ```

- A colour at a frame

  ```json
  {"layer":"Card","path":"fill.color","frame":30}
  ```

- Whole position [x, y, z]

  ```json
  {"layer":"Accent","path":"transform.position","frame":30}
  ```

Related: `setPropertyDriver`, `animateProperty`

CLI: `kinetic tool getProperty --params '<json>' --workspace ./out`

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

## inspectDependencies

Every prop driver + expression/link dependencies on a layer.

- family: `props` · mutating: `false` · transport: CLI `kinetic tool inspectDependencies` / SDK `batch([{tool: "inspectDependencies"}])` / MCP `tools/call {name: "inspectDependencies"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.inspectDependencies(...)` in runScript or query · inside runScript: yes
- signature: `inspectDependencies {layer: string|int}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `layer` | string\|integer | required | |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- What drives this layer

  ```json
  {"layer":"Accent"}
  ```

CLI: `kinetic tool inspectDependencies --params '<json>' --workspace ./out`

## validateExpression

Parse an expression (syntax + dependency refs); with layer and path also run it there and return its value at frame, or the error with a hint.

- family: `props` · mutating: `false` · transport: CLI `kinetic tool validateExpression` / SDK `batch([{tool: "validateExpression"}])` / MCP `tools/call {name: "validateExpression"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.validateExpression(...)` in runScript or query · inside runScript: yes
- signature: `validateExpression {source: string, ae_units?: bool = false, frame?: number = 0 [frame], layer?: string|int, path?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `source` | string | required | — Expression source to parse, e.g. "sin(time * 2) * 40" |
| `composition` | string\|integer | optional | — Current comp id for bare refs (default main) |
| `layer` | string\|integer | optional | — Evaluate on this layer |
| `path` | string | optional | — Property it would drive (transform.position.x, or AE's transform.position / scale for both axes) |
| `frame` | number | optional default=0 | — Frame to evaluate at (default 0) |
| `ae_units` | boolean | optional default=false | — Read and write After Effects units |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Check syntax

  ```json
  {"source":"sin(time * 2) * 40"}
  ```

- Run it on a layer at a frame

  ```json
  {"source":"loopOut(\"cycle\")","layer":"Accent","path":"transform.rotation.z","frame":45}
  ```

CLI: `kinetic tool validateExpression --params '<json>' --workspace ./out`

## setCompositionField

Set a composition field: its length (duration in s or duration_frames; layers that ran to the end follow it, layers past a shorter end are trimmed) and name, motion blur (enabled, shutter angle/phase in degrees, max samples, adaptive sampling), background color, transparent flag, and the sound's master bus (gain, loudness target).

- family: `scene` · mutating: `true` · transport: CLI `kinetic tool setCompositionField` / SDK `batch([{tool: "setCompositionField"}])` / MCP `tools/call {name: "setCompositionField"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setCompositionField(...)` in runScript · inside runScript: yes
- signature: `setCompositionField {field: duration|duration_frames|name|motion_blur_enabled|shutter_angle|shutter_phase|motion_blur_samples|motion_blur_adaptive|…, value: any}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `field` | string | required | — Which composition field to set; see value |
| `value` | any | required | — duration: seconds; duration_frames: frames; name: text. motion_blur_enabled/motion_blur_adaptive/transparent: bool. shutter_angle: degrees 1..=720 (180 = film look). shutter_phase: degrees -720..=720 like After Effects (0 = shutter centered on the frame; -90 with a 180 shutter = shutter ends on the frame). motion_blur_samples: taps 2..=64 where motion can't be measured (else 1 per 1.5 px, up to 64). background: color. master_gain_db: dB on the whole mix. loudness_target: LUFS the mix is normalised to (-14 punchy, -16 calm; true peak kept under -1 dBTP), null for off. noise_sampler: "v1" or "v2"; "v1" keeps 0.1.5's wiggle and noise, "v2" (the default) moves across. edges: "v1" or "v2"; "v1" keeps 0.1.5's shape edges (what a project saved before 0.1.6 loads on), "v2" (the default) measures them in pixels: strokes exactly their width, a one-pixel edge at any scale. text_spring: "v1" or "v2"; "v1" keeps 0.1.5's clamped text-spring overshoot (what a project saved before 0.1.6 loads on), "v2" (the default) lets translation, scale and rotation pass rest and settle; opacity is clamped either way. text_contrast: bool, off by default; true gamma-encodes a light glyph's coverage so antialiased edges keep their ink on a dark background (opt-in 0.1.6 approximation; 2D text only, no effect on extruded 3D text or the legacy outline path). |

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

- Move an old project to the fixed wiggle sampler

  ```json
  {"field":"noise_sampler","value":"v2"}
  ```

- Move an old project to edges in pixels

  ```json
  {"field":"edges","value":"v2"}
  ```

Tips:

- Motion blur is adaptive: still frames cost one sample, measured motion one per 1.5 px of travel (up to 64); motion_blur_samples is the count where motion cannot be measured (particles, trims, animated masks).

CLI: `kinetic tool setCompositionField --params '<json>' --workspace ./out`

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

## motions

Look up 637 motion techniques before inventing a move; get one for its Miran mapping and reference clip.

- family: `content` · mutating: `false` · transport: CLI `kinetic tool motions` / SDK `batch([{tool: "motions"}])` / MCP `tools/call {name: "motions"}`
- history: read-only, no history entry
- lean tool list: listed (a direct call) · inside runScript: search, get, and previews already in the cache
- signature: `motions {action?: search|get, family?: string, frames?: int [frames], id?: string, kind?: kit|effect|tool|recipe|gap, limit?: int, preview?: bool, query?: string, sheet?: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `action` | enum["search"|"get"] | optional | — get if id, else search |
| `query` | string | optional | — words, e.g. "mask reveal" |
| `family` | string | optional | — type, ui, transitions, data, ... |
| `kind` | enum["kit"|"effect"|"tool"|"recipe"|"gap"] | optional | — mapping kind |
| `limit` | integer | optional | (min=1, max=50) — results (12) |
| `id` | string | optional | — get: an id from search |
| `preview` | boolean | optional | — get: fetch the reference clip |
| `frames` | integer | optional | (min=1, max=16) — get: sheet of n clip frames |
| `sheet` | string | optional | — get: sheet path |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Families and tags: where to start

  ```json
  {}
  ```

- A launch-film move by words

  ```json
  {"query":"mask reveal","limit":5}
  ```

- Only techniques that map to a kit piece

  ```json
  {"query":"stagger","kind":"kit"}
  ```

- Only runnable recipes, in transitions

  ```json
  {"family":"transitions","kind":"recipe"}
  ```

- One technique: mapping, args, recipe, parameters

  ```json
  {"id":"wipe"}
  ```

Tips:

- A kit mapping carries call, ready for a script: put your own layers in for layer, leader and textLayer. Read mapping.note first: it says where the mapping is partial (a straight edge standing in for a splatter edge).
- Every recipe is a complete runScript body and is executed by the test suite; run it as-is on a fresh session, then adapt its constants.
- The 128 preview clips are study material pinned to the upstream commit; fetch one with preview: true and frames: 6, then read the sheet.
- A gap has no single call: a note starting "No preset:" lists the calls that build it, any other note names what Miran lacks. Do not dress a gap up as a kit call.

CLI: `kinetic tool motions --params '<json>' --workspace ./out`

