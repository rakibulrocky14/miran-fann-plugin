---
name: miran-motion
description: Animates Miran Fann layers like an animator - A-to-B moves, moves on arcs and through points (auto-orient), anticipation, follow-through and overlap, squash and stretch, keyframe/spring/noise/expression drivers with auto (smooth-through) keys, text animators per character, word or line, the named easing vocabulary (Material 3, IBM Carbon, expo, anticipate) and Apple-style springs, speed ramps, time remap, posterize (on twos), echo trails, stagger and choreography, motion blur, and the critiqueMotion review. Use when animating anything in Miran, choosing eases or durations, or wiring drivers.
---

# Motion in Miran Fann

> **Lean tool list:** a call written below as `name {params}` is `miran.name({params})` inside `runScript` (or `query` when it only looks) unless `name` is in your tool list; exports, jobs, voiceover, downloads and panels stay direct calls.

Units: time in **frames**, position px with **y up**, rotation degrees,
scale 1 = 100%, opacity 0..1. Values in moves are absolute, not offsets.
Full reference: `guide {topic:"motion"}` (easing catalogue with curves,
spring table with overshoot percentages, timing table). Exact parameters:
`describeTool {tool}` or `TOOLS.md` in this folder.

## Three tools, pick by shape of the motion

| motion | tool |
|---|---|
| One A-to-B move (entrance, exit, push) | `animateProperty {layer, property, from, to, start_frame, duration_frames, ease}` |
| Several keys, holds, loops, springs to a value, organic noise, math | `setPropertyDriver {layer, path, driver}` |
| Text entrance per character, word or line | `addTextAnimator {layer, animator:{from, by, start_frame, stagger_frames, spring}}` |

Many moves at once (a grid, a chart, a cascade): put them in one
`runScript` and compute the timing in a loop.

`animateProperty` properties: `position.x|y|z`, `rotation` (2D spin) or
`rotation.x|y|z`, `scale` (uniform) or `scale.x|y|z`, `opacity`,
`procedural.<param>`; for the camera `layer:"camera"` with
`dolly.x|y|z`, `crane.pitch|yaw|roll`, `focal_length`; `volume` (dB) on
audio layers. Unknown names are refused. Chain moves on one property by
calling again with a later `start_frame`. To retime, pass `replace: true`
(the overlapping move goes) or `clearAnimation {layer, property}` first.
Before its first move a property holds that move's `from`, so start a
flash or pop with the layer's `in_frame`.

Keyframes (driver): `{"keyframes":{"keys":[{"frame":0,"value":-420,"ease":"smooth"},{"frame":30,"value":0}]}}`.
A key's `ease` shapes the segment that **starts** at that key; `"hold"`
freezes; `"spring:snappy"` etc. work too (a curve fitted to that spring,
landing exactly on the next key). A driver on a path overrides
`animateProperty` moves on it.

## Move like an animator (what separates it from computed motion)

- **Arcs.** Move both axes at once on a curve, not x and y channels in a
  straight diagonal:
  `animateProperty {layer, property:"position", to:[420,-120], arc:0.2, start_frame, duration_frames, ease}`
  (`arc` = bow as a fraction of the distance; `via:[[x,y]...]` curves
  through points; `orient:true` turns the layer along the path).
- **Anticipation.** `ease:"anticipate"` winds up ~6% before the move;
  `"anticipate-overshoot"` also overshoots; `"back-in"` for exits.
- **Follow-through and overlap.**
  `setLayerField {layer:"Label", field:"follow", value:{layer:"Card", delay:3, spring:"snappy"}}`:
  the label repeats the card's motion 3 frames late and settles. Without
  `layer` it runs the layer's own moves through the spring (every stop
  overshoots and settles). Place followers where they rest; don't also
  animate them on the same property.
- **Squash and stretch.** `setLayerField {field:"squash", value:0.5}`:
  stretches along the travel with speed, squashes on hard stops. Soft,
  round things; never type or UI.
- **Keys that flow.** Keyframe `ease:"auto"` carries speed through middle
  keys (no stop at each key, no overshoot).
- **Time.** `setLayerField` `speed` (speed ramp: `[[frame, speed], ...]`),
  `time_remap` (`[[frame, source_frame(, ease|"hold")], ...]`),
  `posterize` (12 = on twos), `echo` ({count, interval, decay}: trails).
- In scripts: `kit.move`, `kit.follow`, `kit.overlap`, `kit.inertia`,
  `kit.squash`, `kit.fan`, `kit.slowmo`, `kit.posterize`, `kit.trail`.

Then run `critiqueMotion {}`: it flags straight diagonals, constant speed,
entrances that accelerate into a dead stop, lockstep timing, one tempo,
nothing following through, jumps, an empty first frame, empty frames
between scenes and words read on the move, with a fix for each. Aim for 85+.

## Easing: use names

`smooth` (expo-out, the confident default), `snap`, `standard`,
`emphasized-decelerate` / `emphasized-accelerate` (Material 3 hero in/out),
`productive` / `expressive` (IBM Carbon), `expo-in-out` (wipes), `linear`
(only for continuous drift), `back-out` (cartoon overshoot; prefer a
spring), `anticipate` (wind-up, soft landing). Custom:
`interp: {"bezier":[x1,y1,x2,y2]}`.

- Entrances decelerate, exits accelerate and run ~30% shorter.
- Moves that start and end on screen: `standard` or `expressive`.
- Never `linear` on something that starts or stops in view.
- A camera (push, pan, orbit) eases in and lands soft (`camera`,
  `expo-in-out`); springs and overshoot belong to objects, never the lens.

## Springs: physical settle

Presets (Apple's duration/bounce model): `smooth` (no overshoot),
`snappy` (under 1%, crisp, the premium default), `bouncy` (~5%, playful only),
`gentle` (slow, soft), `punchy` (fast kick). Use as `ease:"spring:snappy"`;
on `animateProperty` a named spring settles over `duration_frames`. Custom:
`{"spring":{"duration":0.5,"bounce":0.15}}` (seconds; bounce 0 none, 0.15
crisp, 0.3 playful).

Bounce scales with size: 5% overshoot on a badge is charm, on a full-frame
card it is a malfunction.

## Timing (30 fps; double at 60)

- Fades 6-10 f; element entrances 12-24 f; hero moves 20-36 f;
  transitions 10-22 f (`kit.transition`: whip 10, push 18, zoom 22).
- Stagger related elements 2-4 f, so a list or grid lands as one gesture
  (about half a second in all); start the next element when the previous
  is 50-70% done.
- Distance follows the style: calm pieces travel 20-80 px and scale from
  0.9-0.96; energetic pieces arrive from off-frame, slam from 2x, whip out.
  Scaling from exactly 0 reads as a template unless it is a deliberate pop.
- Hold for reading: 0.3 s per word + 0.5 s; ≥ 45 f for a headline. People
  read at rest: keep a push, zoom or swing out from under words being read.
- Keep a hold alive around the words, not in them: a drifting gradient
  (`procedural.angle`), a `noise` driver on background art (4-10 px), a
  slow `kit.drift` push on the scene group (a tilt only when you ask for
  one), `kit.punch` on a hit. The words themselves sit still while read.

## Decouple channels

Things landing with every property arriving on the same frame is the
clearest machine tell. Offset rotation or scale from position by 4-10
frames and give them their own ease:

```json
{"layer":"Card","property":"position.y","from":-80,"to":0,"start_frame":0,"duration_frames":22,"ease":"emphasized-decelerate"}
{"layer":"Card","property":"rotation","from":-4,"to":0,"start_frame":4,"duration_frames":26,"ease":"spring:snappy"}
```

## Text animators

```json
{"layer":"Headline","animator":{"from":{"y":-40,"opacity":0},"start_frame":8,"stagger_frames":2,"spring":"snappy"}}
```

`from` is the starting pose relative to rest (px y up, degrees) with
absolute `scale` and `opacity`. Stagger 1-2 f reads premium, 3-4
deliberate, more is a typewriter. `by: "words"` or `"lines"` moves whole
words or lines, scaling each about its own centre, in one layer
(`kit.words(text, {one_layer: true})` builds on it). Recipes: `guide
{topic:"text"}`.

## Motion blur

`setCompositionField {field:"motion_blur_enabled", value:true}`, then
`shutter_angle` (degrees: 180 film, 90 crisp). Sampling is adaptive (about
one sample per 1.5 px of on-screen travel), so leave it on for fast moves.

## Check the motion

`critiqueMotion {}` (how the moves read, with fixes), `motionSummary
{layer}` (every move, path, follow and timing), `getProperty {layer, path,
frame}` (the evaluated value), and a `renderContactSheet` at the key poses.
`inspectMotionActivity` measures the whole piece's energy over time and
flags dead holds, a weak opening and a frozen ending. Judge feel with a
`quality:"draft"` MP4 of the section.
