---
name: miran-camera
description: Directs the Miran Fann 3D camera and real 3D - rig and lens setup, lights and shadows, extruded 3D type and logos, dolly, orbit, crane, follow, framing, rack focus, reveal and settle, the two execution modes (editable keyframe channels vs composable constraint clips), and the validation tools that catch flips, spikes and off-frame subjects. Use when moving the camera, adding depth or parallax, or framing subjects in Miran.
---

# Camera in Miran Fann

> **Lean tool list:** a call written below as `name {params}` is `miran.name({params})` inside `runScript` (or `query` when it only looks) unless `name` is in your tool list; exports, jobs, voiceover, downloads and panels stay direct calls.

The camera is a dual-null rig: a dolly (translation) and a head (rotation),
plus lens state. Moves are generated as keyframes you can edit afterwards, or
composed as named constraints you can stack and remove — both are first-class,
and which one you pick changes which parameters apply.

For scene building see **miran-create**; for the physics of the settle
**miran-motion**; for checking a move **miran-critique**.

## Before anything: only 3D layers see the camera

Layers are 2D by default and ignore the camera entirely. Set
`setLayerField {layer, field:"is_3d", value:true}` on every layer the
camera should move; keep titles/UI 2D so they stay put. Camera tools warn
when a composition has no 3D layer.

- World units: +x right, +y up; the camera looks toward +z. Positive z
  pushes a layer away (smaller).
- The default camera is pixel-true (50 mm at z = -height x 50 / 24, -2250
  at 1080p): a 3D layer at z = 0 renders at its pixel size and 2D/3D
  layouts line up. Lenses behave like real full-frame lenses.
- Parallax needs depth: spread layers over z (background 800-2000,
  content 0, foreground accents -300..-600).
- Units reference: `guide {topic:"camera"}` and `guide {topic:"units"}`.

## Real 3D: lights, shadows and extruded solids

Lights work like After Effects lights: with none, 3D layers are unlit
cards; add one and 3D layers are lit by the lights only, so add a key and
a fill. `createLayer {kind:"light", light:{type:"spot"|"point"|"parallel"|
"ambient", ...}, position:[x,y,z]}`; the first light with
`casts_shadows: true` throws shadows onto the layers that accept them.
`setLayerField {field:"extrude", value: depth}` turns text, shapes and SVG
logos into lit solids with a bevel; `field:"material"` sets specular,
shininess and metal. In a script, `kit.lit({style, camera:"high"})` builds
the whole stage (key, fill, rim, floor), `kit.text3d` and `kit.logo3d`
stand extruded type and logos on its floor, and `kit.spin` keeps a solid
turning. Craft and parameters: `guide {topic:"camera"}`, section Lights,
materials and extrusion. Both renderers draw it alike.

## Guardrails

- **Preserve user changes.** A camera may already have constraints stacked
  from earlier work. `listCameraConstraints` first; `removeCameraConstraint`
  by id, never by guessing.
- Camera tools are mutating and undoable.

## The rig

`createCameraRig` replaces the rest pose: dolly position, head euler, focal
length, sensor, focus distance, aperture, near/far, projection
(perspective/orthographic), `look_at`. `setCameraPose` patches individual
fields. `setCameraTarget` sets/clears a static look-at target (a point, or a
layer with an optional frame).

A sensible 1080p start: 50 mm (natural) or 85 mm (compressed, elegant),
focus at the subject depth, aperture 0 unless depth of field is a choice
(10-40 gives visible blur). Motion craft: one motivated, slow, continuous
move per shot; the default `camera` ease starts from rest and lands softly
(`smooth` starts fast and jolts a still camera); motion blur on. Orbits aim
at their centre and continue from the current pose when you omit `radius`,
`height` and `from_deg`; a crane without `pitch_to` keeps the subject framed.

## Two execution modes — pick deliberately

Most move tools have two paths, selected by `mode`:

- **channels** (default) — generates keyframe channels on the camera. Fully
  editable as keyframes afterwards, visible in `inspectCameraTrajectory`.
- **`mode:"constraint"` + `id`** — routes to the weighted stack: named clips
  with priority, weight, axis locks, screen offset, roll, hold-after. Stackable
  and individually removable. This is what you want when several behaviours
  must coexist (track a target *while* maintaining framing).

`end` wins over `duration` in constraint mode; channels mode takes `duration`.

## The four archetypes

Named moves that cover most films:

1. **Axial push-through** — `cameraDolly` along Z, penetrating toward the
   subject to reveal detail. Channels path: `to_z`, `start`, `duration`.
   Finish with `cameraSettle`.
2. **Lateral whip-track** — fast horizontal move matched to exiting elements:
   `cameraCrane {mode:"constraint", id, x_to}` (channels mode moves y and pitch
   only), or `cameraFollowPath` through waypoints for a real dolly path.
3. **Occlusion wipe** — a large structural layer crosses frame and becomes the
   cut. Build the layer in **miran-create**, then `cameraRevealComposition`
   pulls back from a close-up to the full fit and settles head-on.
4. **Graphic match-cut** — align a motif across the cut:
   `cameraFrameLayers` to fit named layers at a frame, then match the framing
   on the next side.

## Framing and following

- `cameraFitBounds` — animate the dolly to fit a world-space rect
  (`bounds:[x0,y0,x1,y1]`, `plane_z`, `margin` default 0.1).
- `cameraFrameLayers` — fit named layers (default: all visual) at a frame.
- `cameraMaintainFraming` — weighted-stack clip holding a layer at a screen
  point (default centre); `size_px`+`world_size` holds projected size.
- `cameraTrackTarget` — aim the head at a layer over `[start, end]`, with
  `offset`, `look_ahead` and `lag` for organic pursuit.
- `cameraOrbit` — orbit a centre at radius/height across a yaw sweep.
  Angles: **180 = in front** (the default camera side), 90 = right, 0 =
  behind the layers. A premium arc is 20-40 degrees (`from_deg:195,
  to_deg:165`); `height` is the camera's absolute world Y.
- `cameraRackFocus` — focus/aperture/focal pulls over `[start, end]`.
- `blendCameraTargets` / `animateCameraTarget` — move the look-at between
  sources; this is how you hand attention from one subject to another.

## Pushes, zooms and tilted screens

A genuinely 2D comp (UI, lower thirds) leaves `is_3d` off: pixel-exact layout
beats a camera you are not using. Its camera is the scene group (`kit.frame`,
`kit.zoom`, `kit.drift`). For it, a `kit.screen3d` push and the 3D camera:

- **Measure, never guess.** `kit.frame` reads the target's bounds;
  `cameraFitBounds` and `cameraFrameLayers` leave it 1/(1+`margin`) of the
  frame (0.1 = 91%: pass 0.2).
- **Land with air round the target** (about 70-85% of the frame). Look at all
  four edges of the landing frame at full size: a cut word, line or half-card
  there reads as a mistake, unless the crop is the design. Lower `fill`, shift
  `offset`, or let the neighbours go soft.
- **Land in 1-2 s** (30-60 frames at 30 fps) and rest before the next move;
  words are read after the landing, not during it. A camera never bounces.
- **A tilted screen is one rigid plane.** `kit.screen3d` lifts its panels as
  one sheet, fits the screen inside the frame and racks focus on a push
  (`bleed: true`, `focus: false` opt out). Tilt 12-20 degrees at the default
  `width`: real-size UI type is ~10 px there, so swing it flat (`face`) and
  push to read it at 28 px or more.

## Verify the move, not the intention

- `inspectCameraTrajectory` — samples the *exact renderer evaluation* over
  `[start, end]`: pose, target, screen state, active moves. If the render
  surprises you, this is ground truth.
- `inspectCameraCoverage` — projects focal layers over the range and reports
  bounds plus off-frame violations. A subject leaving frame at frame 40 is
  found here, not after the encode.
- `validateCameraMove` — the pre-flight: flips, spikes, margins, focal jumps,
  discontinuities, conflicts, story order. Optional profile
  `restrained | cinematic | energetic | handheld` sets the thresholds.

Run all three on any move longer than a second. They are read-only and cheap;
a re-render is not.

## Failures you will hit

- **Camera move does nothing** — no layer is `is_3d`; the tool's `warning`
  says so.
- **"missing `id`"** or **"constraint 'x' already exists"** — constraint mode
  needs a unique clip id; name one, or `removeCameraConstraint` first.
- **Two constraints fight** — same property, overlapping range. The validator
  reports it; resolve by priority/weight or split the range.
- **Camera moves but the subject is static** — the dolly moved while the
  look-at held: add `setCameraTarget` or `cameraTrackTarget`.
- **Everything wobbles** — lock the axes the move does not mean.
- **Focus pulls look wrong** — rack focus needs `focus_distance` headroom in
  the rig.

## Tool reference (generated)

`TOOLS.md` in this folder lists every camera tool with its exact parameters,
generated from the runtime registry — including which parameters belong to
which mode.
