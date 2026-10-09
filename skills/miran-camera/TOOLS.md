# Camera work — tool reference (generated)

Source: the runtime registry via `kinetic schemas` — do not hand-edit; run `bun scripts/gen-docs.ts`.
22 of 165 tools — the ones this skill teaches. The full registry lives in docs/TOOLS.md.

## createCameraRig

Create/replace the rest rig: dolly, head euler, lens, sensor, focus, aperture, clipping, projection, look-at.

- family: `camera` · mutating: `true` · transport: CLI `kinetic tool createCameraRig` / SDK `batch([{tool: "createCameraRig"}])` / MCP `tools/call {name: "createCameraRig"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.createCameraRig(...)` in runScript · inside runScript: yes
- signature: `createCameraRig {aperture?: number, far?: number, focal_length?: number, focus_distance?: number, look_at?: number[], near?: number, ortho_half_height?: number [frame], position?: number[], projection?: perspective|orthographic, rotation_euler?: number[] [deg], sensor_height?: number, sensor_scale?: number, sensor_width?: number [frame]}`

| param | type | required/default | notes |
|---|---|---|---|
| `aperture` | number | optional | — 0 = pinhole (all sharp); 10-40 = visible depth of field around focus_distance |
| `composition` | string\|integer | optional | |
| `far` | number | optional | — Far clip distance, world units (default 20000) |
| `focal_length` | number | optional | — mm on 36x24: 24 wide, 50 natural (default), 85+ telephoto |
| `focus_distance` | number | optional | — Camera to sharp plane, world units (default 1000 = the Z=0 plane) |
| `look_at` | array | optional | — [x, y, z] world point the camera aims at (overrides pitch/yaw; roll kept); null clears (setCamera) |
| `near` | number | optional | — Near clip distance, world units (default 1) |
| `ortho_half_height` | number | optional | — Orthographic only: world units shown from frame centre to top edge |
| `position` | array | optional | — Camera position [x, y, z] in world units, looking toward +z. z = -comp height x focal / 24 (-2250 at 1080p, 50 mm; the default) makes 3D layers at z=0 appear at their pixel size |
| `projection` | enum["perspective"|"orthographic"] | optional | — perspective (default) or orthographic (no size change with depth) |
| `rotation_euler` | array | optional | — Camera head rotation [pitch, yaw, roll] in degrees |
| `sensor_height` | number | optional | — Sensor height in mm (default 24) |
| `sensor_scale` | number | optional | — Sensor scale (default 1 = real full-frame optics; projects made before 0.3.3 keep 3.5, a much wider view); leave it unless you know why |
| `sensor_width` | number | optional | — Sensor width in mm (default 36, full frame) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- 85 mm, still pixel-true at 1080p

  ```json
  {"position":[0,0,-3825],"focal_length":85}
  ```

Related: `cameraDolly`, `cameraFrameLayers`, `setCameraTarget`

CLI: `kinetic tool createCameraRig --params '<json>' --workspace ./out`

## setCameraPose

Patch rest-pose fields (position, rotation_euler, focal_length, focus_distance, aperture).

- family: `camera` · mutating: `true` · transport: CLI `kinetic tool setCameraPose` / SDK `batch([{tool: "setCameraPose"}])` / MCP `tools/call {name: "setCameraPose"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setCameraPose(...)` in runScript · inside runScript: yes
- signature: `setCameraPose {aperture?: number, focal_length?: number, focus_distance?: number, position?: number[], rotation_euler?: number[] [deg]}`

| param | type | required/default | notes |
|---|---|---|---|
| `aperture` | number | optional | — 0 = pinhole (all sharp); 10-40 = visible depth of field around focus_distance |
| `composition` | string\|integer | optional | |
| `focal_length` | number | optional | — mm on 36x24: 24 wide, 50 natural (default), 85+ telephoto |
| `focus_distance` | number | optional | — Camera to sharp plane, world units (default 1000 = the Z=0 plane) |
| `position` | array | optional | — Camera position [x, y, z] in world units, looking toward +z. z = -comp height x focal / 24 (-2250 at 1080p, 50 mm; the default) makes 3D layers at z=0 appear at their pixel size |
| `rotation_euler` | array | optional | — Camera head rotation [pitch, yaw, roll] in degrees |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Wider lens

  ```json
  {"focal_length":35}
  ```

Related: `cameraDolly`, `cameraFrameLayers`, `setCameraTarget`

CLI: `kinetic tool setCameraPose --params '<json>' --workspace ./out`

## setCameraTarget

Set or clear the static look-at target: {point} | {layer, frame?} | {clear:true}.

- family: `camera` · mutating: `true` · transport: CLI `kinetic tool setCameraTarget` / SDK `batch([{tool: "setCameraTarget"}])` / MCP `tools/call {name: "setCameraTarget"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setCameraTarget(...)` in runScript · inside runScript: yes
- signature: `setCameraTarget {clear?: bool, frame?: number = 0 [frame], layer?: string|int, point?: array|null}`

| param | type | required/default | notes |
|---|---|---|---|
| `clear` | boolean | optional | — true removes the look-at target |
| `composition` | string\|integer | optional | |
| `frame` | number | optional default=0 | — Frame at which the target layer's position is sampled (default 0) |
| `layer` | string\|integer | optional | |
| `point` | array\|null | optional | — Aim at this fixed world point [x, y, z] (null clears, same as clear:true) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Look at a layer

  ```json
  {"layer":"Card"}
  ```

- Look at a point

  ```json
  {"point":[0,0,0]}
  ```

- Stop looking at anything

  ```json
  {"clear":true}
  ```

CLI: `kinetic tool setCameraTarget --params '<json>' --workspace ./out`

## animateCameraTarget

Blend the camera target from 'from' (default: current) to 'to' over [start, end] (weighted-stack clip).

- family: `camera` · mutating: `true` · transport: CLI `kinetic tool animateCameraTarget` / SDK `batch([{tool: "animateCameraTarget"}])` / MCP `tools/call {name: "animateCameraTarget"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.animateCameraTarget(...)` in runScript · inside runScript: yes
- signature: `animateCameraTarget {id: string, start: number [frame], to: object, axis_locks?: bool[], duration?: number [frames], ease?: string = "camera", end?: number [frame], from?: object, hold_after?: bool = true, priority?: int = 0, roll_to?: number [deg], screen_offset?: number[] [px], weight?: number, weight_to?: number}`

| param | type | required/default | notes |
|---|---|---|---|
| `axis_locks` | array | optional | — [x, y, z] booleans: a locked camera-position axis keeps its incoming value |
| `composition` | string\|integer | optional | |
| `duration` | number | optional | — Frames (30 = 1 s at 30 fps) |
| `ease` | string | optional default="camera" | — Ease name (list: animateProperty.ease) |
| `end` | number | optional | — End frame (or give duration: end = start + duration) |
| `from` | object | optional | — Starting target (default: the current aim): a target source as in animateCameraTarget.to ({point} | {layer, offset, axes, look_ahead, lag} | {blend}) |
| `hold_after` | boolean | optional default=true | — Keep the final state after end (default true; false releases) |
| `id` | string | required | — Clip id, unique per composition; removeCameraConstraint deletes it |
| `priority` | integer | optional default=0 | — Higher wins where clips overlap (default 0) |
| `roll_to` | number | optional | — Camera roll target in degrees, blended in by the clip envelope |
| `screen_offset` | array | optional | — [x, y] screen px (y down) nudge of the aim, for off-centre framing |
| `start` | number | required | — Start frame |
| `to` | object | required | — Target to move the aim to |
| `weight` | number | optional | — Clip blend weight 0..1 (default 1 = full effect) |
| `weight_to` | number | optional | — Fade the weight to this across [start, end] |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Swing the aim onto the card

  ```json
  {"id":"look-card","to":{"layer":"Card"},"start":0,"end":45}
  ```

Related: `listCameraConstraints`, `removeCameraConstraint`, `inspectCameraTrajectory`

CLI: `kinetic tool animateCameraTarget --params '<json>' --workspace ./out`

## blendCameraTargets

Blend the camera target between sources a and b over [start, end] (weighted-stack clip).

- family: `camera` · mutating: `true` · transport: CLI `kinetic tool blendCameraTargets` / SDK `batch([{tool: "blendCameraTargets"}])` / MCP `tools/call {name: "blendCameraTargets"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.blendCameraTargets(...)` in runScript · inside runScript: yes
- signature: `blendCameraTargets {a: object, b: object, id: string, start: number [frame], axis_locks?: bool[], duration?: number [frames], ease?: string = "camera", end?: number [frame], hold_after?: bool = true, priority?: int = 0, roll_to?: number [deg], screen_offset?: number[] [px], weight?: number, weight_to?: number}`

| param | type | required/default | notes |
|---|---|---|---|
| `a` | object | required | — First target source: a target source as in animateCameraTarget.to ({point} | {layer, offset, axes, look_ahead, lag} | {blend}) |
| `axis_locks` | array | optional | — [x, y, z] booleans: a locked camera-position axis keeps its incoming value |
| `b` | object | required | — Second target source: a target source as in animateCameraTarget.to ({point} | {layer, offset, axes, look_ahead, lag} | {blend}) |
| `composition` | string\|integer | optional | |
| `duration` | number | optional | — Frames (30 = 1 s at 30 fps) |
| `ease` | string | optional default="camera" | — Ease name (list: animateProperty.ease) |
| `end` | number | optional | — End frame (or give duration: end = start + duration) |
| `hold_after` | boolean | optional default=true | — Keep the final state after end (default true; false releases) |
| `id` | string | required | — Clip id, unique per composition; removeCameraConstraint deletes it |
| `priority` | integer | optional default=0 | — Higher wins where clips overlap (default 0) |
| `roll_to` | number | optional | — Camera roll target in degrees, blended in by the clip envelope |
| `screen_offset` | array | optional | — [x, y] screen px (y down) nudge of the aim, for off-centre framing |
| `start` | number | required | — Start frame |
| `weight` | number | optional | — Clip blend weight 0..1 (default 1 = full effect) |
| `weight_to` | number | optional | — Fade the weight to this across [start, end] |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Aim between two subjects

  ```json
  {"id":"between","a":{"layer":"Card"},"b":{"point":[300,0,0]},"start":30,"end":90}
  ```

Related: `listCameraConstraints`, `removeCameraConstraint`, `inspectCameraTrajectory`

CLI: `kinetic tool blendCameraTargets --params '<json>' --workspace ./out`

## cameraDolly

Dolly along Z to a target depth: to_z, start and duration (baked channels), or mode='constraint' with an id for a weighted clip.

- family: `camera` · mutating: `true` · transport: CLI `kinetic tool cameraDolly` / SDK `batch([{tool: "cameraDolly"}])` / MCP `tools/call {name: "cameraDolly"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.cameraDolly(...)` in runScript · inside runScript: yes
- signature: `cameraDolly {duration?: number [frames], ease?: string = "camera", end?: number [frame], from_z?: number, id?: string, mode?: channels|constraint = "channels", start?: number [frame], to_z?: number}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `duration` | number | optional | — Duration in frames (required unless mode='constraint') |
| `ease` | string | optional default="camera" | — Ease name (list: animateProperty.ease) |
| `end` | number | optional | — End frame (or give duration) |
| `from_z` | number | optional | — Start depth, world Z (default: the camera's evaluated Z at start) |
| `id` | string | optional | — Constraint id (required when mode='constraint') |
| `mode` | enum["channels"|"constraint"] | optional default="channels" | — Omit or 'channels' for the to_z/start/duration path; 'constraint' for the weighted-stack path |
| `start` | number | optional | — Start frame (required unless mode='constraint') |
| `to_z` | number | optional | — Target depth (required unless mode='constraint') |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Slow push-in (baked keys)

  ```json
  {"to_z":-1900,"start":0,"duration":90}
  ```

- Same move as a blendable clip

  ```json
  {"mode":"constraint","id":"push-in","to_z":-1900,"start":0,"end":90}
  ```

Tips:

- The camera only affects layers with is_3d=true (setLayerField). 2D layers ignore it.
- channels mode bakes keyframes you can inspect; constraint mode adds a weighted clip that can overlap and blend with others.

Related: `validateCameraMove`, `inspectCameraCoverage`, `listCameraConstraints`, `createCameraRig`, `setLayerField`

CLI: `kinetic tool cameraDolly --params '<json>' --workspace ./out`

## cameraOrbit

Orbit a center at radius/height across a yaw sweep, aimed at the center. mode='constraint' (+id) routes to the weighted-stack path.

- family: `camera` · mutating: `true` · transport: CLI `kinetic tool cameraOrbit` / SDK `batch([{tool: "cameraOrbit"}])` / MCP `tools/call {name: "cameraOrbit"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.cameraOrbit(...)` in runScript · inside runScript: yes
- signature: `cameraOrbit {duration: number [frames], start: number [frame], to_deg: number [deg], center?: number[], ease?: string = "camera", end?: number [frame], from_deg?: number, height?: number, id?: string, mode?: channels|constraint = "channels", radius?: number, segments?: int = 8}`

| param | type | required/default | notes |
|---|---|---|---|
| `center` | array | optional | — [x, y, z] world point to orbit (default [0, 0, 0]) |
| `composition` | string\|integer | optional | |
| `duration` | number | required | — Frames (30 = 1 s at 30 fps) |
| `ease` | string | optional default="camera" | — Ease name (list: animateProperty.ease) |
| `end` | number | optional | — End frame (or give duration) |
| `from_deg` | number | optional | — Default: the camera's current angle |
| `height` | number | optional | — Camera y; default: current |
| `id` | string | optional | — Clip id (constraint mode) |
| `mode` | enum["channels"|"constraint"] | optional default="channels" | — channels (default): baked keys; constraint: a blendable clip (needs id) |
| `radius` | number | optional | — Default: the camera's current distance |
| `segments` | integer | optional default=8 | — Channels mode: linear pieces approximating the arc (default 8, max 64) |
| `start` | number | required | — Start frame |
| `to_deg` | number | required | — End angle in degrees (same convention as from_deg; 180 -> 150 is a gentle 30-degree arc) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- 30-degree arc from where the camera is

  ```json
  {"to_deg":210,"start":0,"duration":120}
  ```

Tips:

- The camera only affects layers with is_3d=true (setLayerField). 2D layers ignore it.
- channels mode bakes keyframes you can inspect; constraint mode adds a weighted clip that can overlap and blend with others.

Related: `validateCameraMove`, `inspectCameraCoverage`, `listCameraConstraints`, `createCameraRig`, `setLayerField`

CLI: `kinetic tool cameraOrbit --params '<json>' --workspace ./out`

## cameraCrane

Crane vertically with an optional head-pitch move. mode='constraint' (+id) routes to the weighted-stack path.

- family: `camera` · mutating: `true` · transport: CLI `kinetic tool cameraCrane` / SDK `batch([{tool: "cameraCrane"}])` / MCP `tools/call {name: "cameraCrane"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.cameraCrane(...)` in runScript · inside runScript: yes
- signature: `cameraCrane {duration: number [frames], start: number [frame], ease?: string = "camera", end?: number [frame], id?: string, look?: object, mode?: channels|constraint = "channels", pitch_to?: number [deg], x_to?: number, y_to?: number, z_to?: number}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `duration` | number | required | — Frames (30 = 1 s at 30 fps) |
| `ease` | string | optional default="camera" | — Ease name (list: animateProperty.ease) |
| `end` | number | optional | — End frame (or give duration) |
| `id` | string | optional | — Clip id (constraint mode) |
| `look` | object | optional | — Constraint mode: keep aiming at this target while craning: a target source as in animateCameraTarget.to ({point} | {layer, offset, axes, look_ahead, lag} | {blend}) |
| `mode` | enum["channels"|"constraint"] | optional default="channels" | — channels (default): baked keys; constraint: a blendable clip (needs id) |
| `pitch_to` | number | optional | — Degrees, + tilts down; omit to keep the subject framed |
| `start` | number | required | — Start frame |
| `x_to` | number | optional | — Constraint mode: target camera world X |
| `y_to` | number | optional | — Target camera world Y (up is +) |
| `z_to` | number | optional | — Constraint mode: target camera world Z |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Rise, keeping the scene framed

  ```json
  {"y_to":400,"start":0,"duration":75}
  ```

Tips:

- The camera only affects layers with is_3d=true (setLayerField). 2D layers ignore it.
- channels mode bakes keyframes you can inspect; constraint mode adds a weighted clip that can overlap and blend with others.

Related: `validateCameraMove`, `inspectCameraCoverage`, `listCameraConstraints`, `createCameraRig`, `setLayerField`

CLI: `kinetic tool cameraCrane --params '<json>' --workspace ./out`

## cameraSettle

Ease dolly/head/focus/aperture back to the rest pose. mode='constraint' (+id) routes to the weighted-stack path.

- family: `camera` · mutating: `true` · transport: CLI `kinetic tool cameraSettle` / SDK `batch([{tool: "cameraSettle"}])` / MCP `tools/call {name: "cameraSettle"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.cameraSettle(...)` in runScript · inside runScript: yes
- signature: `cameraSettle {at_frame: number [frame], duration: number [frames], ease?: string = "camera", end?: number [frame], id?: string, mode?: channels|constraint = "channels"}`

| param | type | required/default | notes |
|---|---|---|---|
| `at_frame` | number | required | — Frame at which the settle starts (the camera eases from its pose there back to the rest rig) |
| `composition` | string\|integer | optional | |
| `duration` | number | required | — Frames (30 = 1 s at 30 fps) |
| `ease` | string | optional default="camera" | — Ease name (list: animateProperty.ease) |
| `end` | number | optional | — End frame (or give duration) |
| `id` | string | optional | — Clip id (constraint mode) |
| `mode` | enum["channels"|"constraint"] | optional default="channels" | — channels (default): baked keys; constraint: a blendable clip (needs id) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Return to the rest pose

  ```json
  {"at_frame":120,"duration":24}
  ```

Related: `validateCameraMove`, `inspectCameraCoverage`, `listCameraConstraints`, `createCameraRig`, `setLayerField`

CLI: `kinetic tool cameraSettle --params '<json>' --workspace ./out`

## cameraFitBounds

Animate the dolly to fit a world bounds rect. mode='constraint' (+id) routes to the weighted-stack path.

- family: `camera` · mutating: `true` · transport: CLI `kinetic tool cameraFitBounds` / SDK `batch([{tool: "cameraFitBounds"}])` / MCP `tools/call {name: "cameraFitBounds"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.cameraFitBounds(...)` in runScript · inside runScript: yes
- signature: `cameraFitBounds {bounds: number[] [px], duration: number [frames], plane_z: number, start: number [frame], ease?: string = "camera", end?: number [frame], id?: string, margin?: number = 0.1 [frame], mode?: channels|constraint = "channels"}`

| param | type | required/default | notes |
|---|---|---|---|
| `bounds` | array | required | — [x0, y0, x1, y1] world-space rectangle to fit (y up; for 2D-placed content these are the layer px coordinates) |
| `composition` | string\|integer | optional | |
| `duration` | number | required | — Frames (30 = 1 s at 30 fps) |
| `ease` | string | optional default="camera" | — Ease name (list: animateProperty.ease) |
| `end` | number | optional | — End frame (or give duration) |
| `id` | string | optional | — Clip id (constraint mode) |
| `margin` | number | optional default=0.1 | — Headroom, fraction of the frame (0.1 = 10% each side) |
| `mode` | enum["channels"|"constraint"] | optional default="channels" | — channels (default): baked keys; constraint: a blendable clip (needs id) |
| `plane_z` | number | required | — World Z of the bounds plane (0 for layers at default depth) |
| `start` | number | required | — Start frame |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Frame a 960x540 region

  ```json
  {"bounds":[-480,-270,480,270],"plane_z":0,"start":0,"duration":45}
  ```

Tips:

- The camera only affects layers with is_3d=true (setLayerField). 2D layers ignore it.
- channels mode bakes keyframes you can inspect; constraint mode adds a weighted clip that can overlap and blend with others.

Related: `validateCameraMove`, `inspectCameraCoverage`, `listCameraConstraints`, `createCameraRig`, `setLayerField`

CLI: `kinetic tool cameraFitBounds --params '<json>' --workspace ./out`

## cameraFrameLayers

Fit named layers (default: all visual) at a frame. mode='constraint' (+id) routes to the weighted-stack path.

- family: `camera` · mutating: `true` · transport: CLI `kinetic tool cameraFrameLayers` / SDK `batch([{tool: "cameraFrameLayers"}])` / MCP `tools/call {name: "cameraFrameLayers"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.cameraFrameLayers(...)` in runScript · inside runScript: yes
- signature: `cameraFrameLayers {duration: number [frames], frame: number [frame], start: number [frame], ease?: string = "camera", end?: number [frame], id?: string, layers?: string|int[], margin?: number = 0.1 [frame], mode?: channels|constraint = "channels"}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `duration` | number | required | — Frames (30 = 1 s at 30 fps) |
| `ease` | string | optional default="camera" | — Ease name (list: animateProperty.ease) |
| `end` | number | optional | — End frame (or give duration) |
| `frame` | number | required | — Frame at which the layers' positions are measured |
| `id` | string | optional | — Clip id (constraint mode) |
| `layers` | array | optional | — Layer ids or names (default: every visual layer) |
| `margin` | number | optional default=0.1 | — Headroom, fraction of the frame (0.1 = 10% each side) |
| `mode` | enum["channels"|"constraint"] | optional default="channels" | — channels (default): baked keys; constraint: a blendable clip (needs id) |
| `start` | number | required | — Start frame |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Push in until the card fills the frame

  ```json
  {"layers":["Card"],"frame":0,"start":0,"duration":45,"margin":0.15}
  ```

Tips:

- The camera only affects layers with is_3d=true (setLayerField). 2D layers ignore it.
- channels mode bakes keyframes you can inspect; constraint mode adds a weighted clip that can overlap and blend with others.

Related: `validateCameraMove`, `inspectCameraCoverage`, `listCameraConstraints`, `createCameraRig`, `setLayerField`

CLI: `kinetic tool cameraFrameLayers --params '<json>' --workspace ./out`

## cameraRevealComposition

Pull back from a close-up to the full fit, settling head-on. mode='constraint' (+id) routes to the weighted-stack path.

- family: `camera` · mutating: `true` · transport: CLI `kinetic tool cameraRevealComposition` / SDK `batch([{tool: "cameraRevealComposition"}])` / MCP `tools/call {name: "cameraRevealComposition"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.cameraRevealComposition(...)` in runScript · inside runScript: yes
- signature: `cameraRevealComposition {duration: number [frames], start: number [frame], closeup?: number = 0.45, ease?: string = "camera", end?: number [frame], id?: string, margin?: number = 0.1 [frame], mode?: channels|constraint = "channels", sweep_deg?: number = 0 [deg]}`

| param | type | required/default | notes |
|---|---|---|---|
| `closeup` | number | optional default=0.45 | — Channels mode: starting distance as a fraction of the final fit distance (default 0.45 = starts ~2.2x closer) |
| `composition` | string\|integer | optional | |
| `duration` | number | required | — Frames (30 = 1 s at 30 fps) |
| `ease` | string | optional default="camera" | — Ease name (list: animateProperty.ease) |
| `end` | number | optional | — End frame (or give duration) |
| `id` | string | optional | — Clip id (constraint mode) |
| `margin` | number | optional default=0.1 | — Headroom, fraction of the frame (0.1 = 10% each side) |
| `mode` | enum["channels"|"constraint"] | optional default="channels" | — channels (default): baked keys; constraint: a blendable clip (needs id) |
| `start` | number | required | — Start frame |
| `sweep_deg` | number | optional default=0 | — Channels mode: yaw sweep in degrees during the pull-back, settling head-on (default 0 = straight pull-back) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Pull back from a close-up with a slight sweep

  ```json
  {"start":0,"duration":75,"closeup":0.5,"sweep_deg":12}
  ```

Tips:

- The camera only affects layers with is_3d=true (setLayerField). 2D layers ignore it.
- channels mode bakes keyframes you can inspect; constraint mode adds a weighted clip that can overlap and blend with others.

Related: `validateCameraMove`, `inspectCameraCoverage`, `listCameraConstraints`, `createCameraRig`, `setLayerField`

CLI: `kinetic tool cameraRevealComposition --params '<json>' --workspace ./out`

## cameraTrackTarget

Aim the head at a layer over [start, end] (yaw/pitch segments). mode='constraint' (+id) routes to the weighted-stack path.

- family: `camera` · mutating: `true` · transport: CLI `kinetic tool cameraTrackTarget` / SDK `batch([{tool: "cameraTrackTarget"}])` / MCP `tools/call {name: "cameraTrackTarget"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.cameraTrackTarget(...)` in runScript · inside runScript: yes
- signature: `cameraTrackTarget {end: number [frame], layer: string|int, start: number [frame], id?: string, lag?: number [frames], look_ahead?: number [frames], mode?: channels|constraint = "channels", offset?: number[], step?: number = 4 [frames]}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `end` | number | required | — End frame (or give duration) |
| `id` | string | optional | — Clip id (constraint mode) |
| `lag` | number | optional | — Constraint mode: aim at where the layer was this many frames earlier (trailing follow) |
| `layer` | string\|integer | required | |
| `look_ahead` | number | optional | — Constraint mode: aim at where the layer will be this many frames later (anticipation) |
| `mode` | enum["channels"|"constraint"] | optional default="channels" | — channels (default): baked keys; constraint: a blendable clip (needs id) |
| `offset` | array | optional | — Constraint mode: [x, y, z] world offset added to the layer origin |
| `start` | number | required | — Start frame |
| `step` | number | optional default=4 | — Channels mode: aim is re-solved every `step` frames (default 4; lower = tighter, more keys) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Keep the card centred

  ```json
  {"layer":"Card","start":0,"end":90}
  ```

Tips:

- The camera only affects layers with is_3d=true (setLayerField). 2D layers ignore it.
- channels mode bakes keyframes you can inspect; constraint mode adds a weighted clip that can overlap and blend with others.

Related: `validateCameraMove`, `inspectCameraCoverage`, `listCameraConstraints`, `createCameraRig`, `setLayerField`

CLI: `kinetic tool cameraTrackTarget --params '<json>' --workspace ./out`

## cameraFollowPath

Dolly through [{frame, position}] waypoints. mode='constraint' (+id) routes to the weighted-stack path.

- family: `camera` · mutating: `true` · transport: CLI `kinetic tool cameraFollowPath` / SDK `batch([{tool: "cameraFollowPath"}])` / MCP `tools/call {name: "cameraFollowPath"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.cameraFollowPath(...)` in runScript · inside runScript: yes
- signature: `cameraFollowPath {keys: any[], ease?: string = "linear", id?: string, mode?: channels|constraint = "channels"}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `ease` | string | optional default="linear" | — Ease name (list: animateProperty.ease) |
| `id` | string | optional | — Clip id (constraint mode) |
| `keys` | array | required | — [{frame, position:[x,y,z]}], ascending |
| `mode` | enum["channels"|"constraint"] | optional default="channels" | — channels (default): baked keys; constraint: a blendable clip (needs id) |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Drift through two waypoints

  ```json
  {"keys":[{"frame":0,"position":[-200,0,-2400]},{"frame":90,"position":[200,40,-2000]}],"ease":"camera"}
  ```

Tips:

- The camera only affects layers with is_3d=true (setLayerField). 2D layers ignore it.
- channels mode bakes keyframes you can inspect; constraint mode adds a weighted clip that can overlap and blend with others.

Related: `validateCameraMove`, `inspectCameraCoverage`, `listCameraConstraints`, `createCameraRig`, `setLayerField`

CLI: `kinetic tool cameraFollowPath --params '<json>' --workspace ./out`

## cameraMaintainFraming

Weighted-stack clip holding a layer at a screen point (default: center); size_px+world_size holds projected size.

- family: `camera` · mutating: `true` · transport: CLI `kinetic tool cameraMaintainFraming` / SDK `batch([{tool: "cameraMaintainFraming"}])` / MCP `tools/call {name: "cameraMaintainFraming"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.cameraMaintainFraming(...)` in runScript · inside runScript: yes
- signature: `cameraMaintainFraming {id: string, layer: string|int, start: number [frame], axis_locks?: bool[], duration?: number [frames], ease?: string = "camera", end?: number [frame], hold_after?: bool = true, priority?: int = 0, roll_to?: number [deg], screen?: number[] [frame], screen_offset?: number[] [px], size_px?: number [px], weight?: number, weight_to?: number, world_size?: number}`

| param | type | required/default | notes |
|---|---|---|---|
| `axis_locks` | array | optional | — [x, y, z] booleans: a locked camera-position axis keeps its incoming value |
| `composition` | string\|integer | optional | |
| `duration` | number | optional | — Frames (30 = 1 s at 30 fps) |
| `ease` | string | optional default="camera" | — Ease name (list: animateProperty.ease) |
| `end` | number | optional | — End frame (or give duration: end = start + duration) |
| `hold_after` | boolean | optional default=true | — Keep the final state after end (default true; false releases) |
| `id` | string | required | — Clip id, unique per composition; removeCameraConstraint deletes it |
| `layer` | string\|integer | required | |
| `priority` | integer | optional default=0 | — Higher wins where clips overlap (default 0) |
| `roll_to` | number | optional | — Camera roll target in degrees, blended in by the clip envelope |
| `screen` | array | optional | — [x, y] screen point in pixels from the TOP-LEFT (y down) where the layer should sit (default: frame centre) |
| `screen_offset` | array | optional | — [x, y] screen px (y down) nudge of the aim, for off-centre framing |
| `size_px` | number | optional | — Hold the layer at this projected size in px (needs world_size) |
| `start` | number | required | — Start frame |
| `weight` | number | optional | — Clip blend weight 0..1 (default 1 = full effect) |
| `weight_to` | number | optional | — Fade the weight to this across [start, end] |
| `world_size` | number | optional | — The layer's real size in world units that size_px refers to |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Hold the card on the right third

  ```json
  {"id":"hold-card","layer":"Card","screen":[1280,540],"start":0,"end":120}
  ```

Related: `listCameraConstraints`, `removeCameraConstraint`, `inspectCameraTrajectory`

CLI: `kinetic tool cameraMaintainFraming --params '<json>' --workspace ./out`

## cameraRackFocus

Weighted-stack lens clip: focus/aperture/focal pulls over [start, end].

- family: `camera` · mutating: `true` · transport: CLI `kinetic tool cameraRackFocus` / SDK `batch([{tool: "cameraRackFocus"}])` / MCP `tools/call {name: "cameraRackFocus"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.cameraRackFocus(...)` in runScript · inside runScript: yes
- signature: `cameraRackFocus {id: string, start: number [frame], duration?: number [frames], ease?: string = "camera", end?: number [frame], hold_after?: bool = true, priority?: int = 0, to_aperture?: number, to_focal?: number, to_focus?: number, weight?: number, weight_to?: number}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `duration` | number | optional | — Frames (30 = 1 s at 30 fps) |
| `ease` | string | optional default="camera" | — Ease name (list: animateProperty.ease) |
| `end` | number | optional | — End frame (or give duration: end = start + duration) |
| `hold_after` | boolean | optional default=true | — Keep the final state after end (default true; false releases) |
| `id` | string | required | — Clip id, unique per composition; removeCameraConstraint deletes it |
| `priority` | integer | optional default=0 | — Higher wins where clips overlap (default 0) |
| `start` | number | required | — Start frame |
| `to_aperture` | number | optional | — Target aperture (0 = everything sharp) |
| `to_focal` | number | optional | — Target focal length in mm (a zoom) |
| `to_focus` | number | optional | — Target focus distance, world units |
| `weight` | number | optional | — Clip blend weight 0..1 (default 1 = full effect) |
| `weight_to` | number | optional | — Fade the weight to this across [start, end] |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Pull focus back with shallow depth

  ```json
  {"id":"pull","to_focus":1400,"to_aperture":20,"start":30,"end":60}
  ```

Related: `listCameraConstraints`, `removeCameraConstraint`, `inspectCameraTrajectory`

CLI: `kinetic tool cameraRackFocus --params '<json>' --workspace ./out`

## inspectCameraTrajectory

Sample the exact renderer camera evaluation over [start, end]: pose, target, screen state, active moves.

- family: `camera` · mutating: `false` · transport: CLI `kinetic tool inspectCameraTrajectory` / SDK `batch([{tool: "inspectCameraTrajectory"}])` / MCP `tools/call {name: "inspectCameraTrajectory"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.inspectCameraTrajectory(...)` in runScript or query · inside runScript: yes
- signature: `inspectCameraTrajectory {end: number [frame], start: number [frame], step?: number = 1 [frames], target_layer?: string|int}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `end` | number | required | — End frame |
| `start` | number | required | — Start frame |
| `step` | number | optional default=1 | — Sampling step in frames |
| `target_layer` | string\|integer | optional | — Also report this layer's screen position and size per sample |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Sample the evaluated camera

  ```json
  {"start":0,"end":90,"step":15,"target_layer":"Card"}
  ```

CLI: `kinetic tool inspectCameraTrajectory --params '<json>' --workspace ./out`

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

## listCameraConstraints

List weighted-stack clips with kinds, ranges, weights, and affected properties.

- family: `camera` · mutating: `false` · transport: CLI `kinetic tool listCameraConstraints` / SDK `batch([{tool: "listCameraConstraints"}])` / MCP `tools/call {name: "listCameraConstraints"}`
- history: read-only, no history entry
- lean tool list: not listed: call it as `miran.listCameraConstraints(...)` in runScript or query · inside runScript: yes
- signature: `listCameraConstraints {}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |

Example: `{}`

CLI: `kinetic tool listCameraConstraints --params '<json>' --workspace ./out`

## removeCameraConstraint

Remove a weighted-stack clip by id (undoable).

- family: `camera` · mutating: `true` · transport: CLI `kinetic tool removeCameraConstraint` / SDK `batch([{tool: "removeCameraConstraint"}])` / MCP `tools/call {name: "removeCameraConstraint"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.removeCameraConstraint(...)` in runScript · inside runScript: yes
- signature: `removeCameraConstraint {id: string}`

| param | type | required/default | notes |
|---|---|---|---|
| `composition` | string\|integer | optional | |
| `id` | string | required | — Clip id to remove |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Delete a clip

  ```json
  {"id":"push"}
  ```

CLI: `kinetic tool removeCameraConstraint --params '<json>' --workspace ./out`

## setCamera

Set composition camera fields (dolly, rotation, lens, focus, aperture, projection, look_at).

- family: `content` · mutating: `true` · transport: CLI `kinetic tool setCamera` / SDK `batch([{tool: "setCamera"}])` / MCP `tools/call {name: "setCamera"}`
- history: creates an undo step
- lean tool list: not listed: call it as `miran.setCamera(...)` in runScript · inside runScript: yes
- signature: `setCamera {aperture?: number, dolly?: number[], focal_length?: number, focus_distance?: number, look_at?: array|null, projection?: perspective|orthographic, rotation?: number[] [deg]}`

| param | type | required/default | notes |
|---|---|---|---|
| `aperture` | number | optional | — 0 = pinhole (all sharp); 10-40 = visible depth of field around focus_distance |
| `composition` | string\|integer | optional | |
| `dolly` | array | optional | — Camera position [x, y, z] in world units (default: pixel-true, [0, 0, -2250] at 1080p) |
| `focal_length` | number | optional | — mm on 36x24: 24 wide, 50 natural (default), 85+ telephoto |
| `focus_distance` | number | optional | — Camera to sharp plane, world units (default 1000 = the Z=0 plane) |
| `look_at` | array\|null | optional | — [x, y, z] world point the camera aims at (overrides pitch/yaw; roll kept); null clears (setCamera) |
| `projection` | enum["perspective"|"orthographic"] | optional | — perspective (default) or orthographic (no size change with depth) |
| `rotation` | array | optional | — Camera head rotation [pitch, yaw, roll] in degrees |

Examples (they run as written on the starter scene from `guide {topic:"start"}`):

- Longer lens, pulled back

  ```json
  {"dolly":[0,0,-1400],"focal_length":85}
  ```

Related: `cameraDolly`, `cameraFrameLayers`, `setCameraTarget`

CLI: `kinetic tool setCamera --params '<json>' --workspace ./out`

