---
name: miran-motion
description: Animates Miran Fann layers like an animator - A-to-B moves, moves on arcs and through points (auto-orient), anticipation, follow-through and overlap, squash and stretch, keyframe/spring/noise/expression drivers with auto (smooth-through) keys, text animators per character, word or line, the named easing vocabulary (Material 3, IBM Carbon, expo, anticipate) and Apple-style springs, speed ramps, time remap, posterize (on twos), echo trails, stagger and choreography, motion blur, and the critiqueMotion review. Use when animating anything in Miran, choosing eases or durations, or wiring drivers.
---

# Motion in Miran Fann

For every video/animation task, search for suitable reference videos unless the
user opts out, requests offline work, or limits reference research to supplied videos. Load
the sibling **miran-reference** skill; inspect and adapt the references to this
task, reusing an existing study during the same task.
When the film or study is done, call `reference {action: "release", video: ID}`
for each reference to delete its copied video and keep sheets, frames and notes.

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

## Start from the directory, not from a blank timeline

Miran ships **637 motion techniques** (awesome-ai-motion, MIT) with a Miran
mapping for every one. Search it before inventing a move: the entry names the
kit call, effect, tool or recipe that makes it, with arguments and a note.

```
motions {query: "mask reveal"}                 // words
motions {query: "attention", family: "type"}    // a use tag, in one family
motions {query: "title", kind: "kit"}           // what maps to kit.title
motions {id: "wipe"}                            // mapping + recipe + params
```

Reading one result:

- `mapping.kind == "kit"` → `mapping.call` is the call; put your own layers in
  for `layer`, `leader` and `textLayer`.
- `"effect"` → `addEffect {layer, effect: mapping.effect, params: mapping.args}`.
- `"tool"` → call `mapping.tool` with `mapping.args`; `mapping.on` says what
  the args expect.
- `"recipe"` → `runScript {source: <recipe>}`. It is a complete script; run it,
  look at the frame, then take the numbers you want.
- `"gap"` → no single call makes it: a "No preset:" note lists the calls that
  build it, any other note names what Miran lacks. Pick another technique or
  build the closest thing and say so.

`mapping.note` matters: a kit call is sometimes the whole move, sometimes a
starting point ("two panels: run the same enter twice"). The `parameters` list
is the source's own (Korean labels, numeric defaults); the recipe names its own
constants.

A reference clip exists for 128 techniques. `motions {id, preview: true,
frames: 6}` fetches it into the cache and returns a contact sheet in the
workspace — judge the timing, then reproduce it with Miran's eases.

`animateProperty` properties: `position.x|y|z`, `rotation` (2D spin) or
`rotation.x|y|z`, `scale` (uniform) or `scale.x|y|z`, `opacity`,
`procedural.<param>`; for the camera `layer:"camera"` with
`dolly.x|y|z`, `crane.pitch|yaw|roll`, `focal_length`; `volume` (dB) on
audio layers. Unknown names are refused. Chain moves on one property by
calling again with a later `start_frame`. To retime, pass `replace: true`
(the overlapping move goes) or `clearAnimation {layer, property}` first.
Before its first move a property holds that move's `from`, so start a
flash or pop with the layer's `in_frame`.

**Moves hand off.** A move that starts while the property is still
travelling leaves at that speed, the way an animator matches the handles,
and back-to-back moves that share an end point (next `from` = last `to`,
next `start_frame` = last end) pass it at one speed instead of stopping at
each (limited like `auto` keys: never more than twice the slower move's
average). `keyframes` chains join their keys the same way. A move that
turns back starts at rest, a spring too; a move to the value it is at
lets the speed carry out and settle back; a speed too big for a short
move brakes hard into a long ease-in, never past the target; a spring
given an opposing `v0` turns through it and lands smoothly; `linear`
stays constant. `handoff: false` starts from rest, `join: false` stops
at each key, `v0` sets the speed (units per frame).

Keyframes (driver): `{"keyframes":{"keys":[{"frame":0,"value":-420,"ease":"smooth"},{"frame":30,"value":0}]}}`.
A key's `ease` shapes the segment that **starts** at that key; left out,
a key is `auto` (below); `"hold"` freezes; `"spring:snappy"` etc. work
too (a curve fitted to that spring, landing exactly on the next key). A
driver on a path overrides `animateProperty` moves on it.

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
  keys (no stop at each key, no overshoot). `kit.frame` goes further: a
  sequence of framings with `hold: 0` is one flowing camera (its pan keeps
  one speed through every stop and turns over the frames after it, so the
  framed point rounds that turn by a few px), with a hold it rests on each
  target, exactly flat.
- **Time.** `setLayerField` `speed` (speed ramp: `[[frame, speed], ...]`),
  `time_remap` (`[[frame, source_frame(, ease|"hold")], ...]`),
  `posterize` (12 = on twos), `echo` ({count, interval, decay}: trails).
- In scripts: `kit.move`, `kit.follow`, `kit.overlap`, `kit.inertia`,
  `kit.squash`, `kit.fan`, `kit.slowmo`, `kit.posterize`, `kit.trail`.

Then run `critiqueMotion {}`: it flags straight diagonals, constant speed,
entrances that accelerate into a dead stop, lockstep timing, one tempo,
nothing following through, jumps, an empty first frame, empty frames
between scenes and words read on the move, and from the frames themselves
a jolt out of rest, a speed jump where one move takes over from another, a
crawling tail, stops on a beat and fast moves with no blur, with a fix call
for each. Fix every finding it names; the score grades only the moves that
exist, so it is no measure of life.

## Three layers of motion, and effects that move

A shot reads alive when three layers move at once, each at its own speed:

1. **The action**: the move that carries the idea, one at a time, shaped,
   on the beat (the card files in, the number lands, the screen swings
   face-on).
2. **The reaction**: what the action disturbs, small and a few frames late
   (`kit.follow`, `kit.inertia`, `kit.overlap`, `kit.punch`): the badge trails
   its card, the rows answer the click one by one, the frame punches on the
   hit.
3. **The world**: slow, far and continuous: dust in the light, a soft blob,
   a light that breathes, a push of a few percent. It never takes the eye; it
   is why the frame never looks printed.

A shot with only the action looks mechanical; with only the world it is a
screensaver. Model-made shots usually have the action and nothing else, and
freeze when it ends. Energetic launch films barely stop (Lovable, Claude
Design: about 5-9% of the frame changing, almost nothing frozen); calm ones
hold only on a full frame of legible product. Words still hold their place while they are read; the shot around them
keeps going.

Effects are moves too, when they change on a beat: a word resolves from a
blur as it lands (`blur` 30 → 0), a glow swells on the hit and settles
(`kit.glow` with `peak` and `rest`), light rays drift through a title
(`light_rays` `center` animated), a few frames of `chromatic_aberration` on an
impact, the field warms to the brand colour at the reveal (`kit.color`). An
effect held at one value is a filter; give it a start and an end.

```js
// A fresh project: the three layers of motion in one beat, and effects that move on the beat.
miran.createComposition({name: "Layers", width: 1920, height: 1080, fps: 30, duration_frames: 90});
kit.theme({font: "SF Pro", display: "SF Pro", bg: "#0A0E1C", surface: "#141A30", text: "#F1F4FF", muted: "#97A0C2", accent: "#4D6BFE", accent2: "#F2B84B"});
kit.backdrop({style: "solid"});
const T = kit.theme();
const {w, h} = kit.size();
kit.scene({start: 0, end: 90, name: "Kickoff"}, ({start, end, group}) => {
  // The world: light from one side, dust in it, slow and far.
  kit.spot({at: [-w * 0.3, h * 0.3], size: w * 1.5, intensity: 0.45});
  kit.particles("dust", {start, colors: [T.text, T.accent], count: 12, size: 3});
  // The action: the card arrives on an arc, and its stop overshoots and settles.
  const card = kit.card({at: [0, 0], w: 760, h: 380, radius: 32, fill: T.surface});
  const title = kit.title("Kickoff, Wed 14:30", {at: [0, h * 0.05], size: 64, weight: 700, color: T.text, style: "none"});
  const g = kit.group([card.id, title.id], {name: "Invite"});
  kit.move(g, {from: [-w * 0.6, -h * 0.35], to: [0, 0], arc: 0.2, start: start + 4, dur: 24, ease: "smooth"});
  kit.inertia(g, {spring: "snappy", amount: 0.6});
  // The reaction: the badge trails the card and settles after it.
  const badge = kit.pill("all 6 free", {at: [w * 0.15, h * 0.17], size: 40, fill: T.accent2, color: T.bg});
  kit.follow(badge.id, g, {delay: 4, spring: "snappy"});
  // Effects as moves: the title resolves from a blur as the card lands; a glow swells on the hit and settles.
  miran.animateProperty({layer: title.id, property: "blur", from: 30, to: 0, start_frame: start + 16, duration_frames: 14, ease: "emphasized-decelerate"});
  kit.glow(badge.id, {start: start + 34, dur: 26, peak: 1, rest: 0});
  kit.punch(group, {start: start + 34, to: 1.03, sound: false});
});
return "three layers";
```

## Easing: start from rest, or already moving

A move in view can begin in only two honest ways: from rest, or already
going. Nothing in the world reaches full speed in one frame, so a layer
that does reads as a glitch. Choose the curve by what the viewer sees at
the start.

**From rest, in view.** `arrive` is the first reach: it leaves rest
with zero velocity, travels, then lands on a long soft cushion — a card
gliding into its place and holding there. `glide` has the same soft
ends with less accent, for a change of place rather than an entrance;
`easy-ease` is After Effects' Easy Ease, blunter and familiar. An exit
that starts from rest is `depart`: `arrive` mirrored, leaning into the
move and gathering speed as it leaves.

**Already moving, or never seen at all.** The strong ease-outs —
`smooth` (expo-out), `emphasized-decelerate`, `snap`, `quint-out` —
spend most of the travel in the first frames. That is honest when the
start is hidden: from opacity 0, from off-frame, from behind a matte,
out of a cut. On a layer the viewer is already watching, the same curve
is a jolt — it is at full speed before the eye has found it, then
crawls into place. Ask whether the first frame can be seen, not how
snappy the move should feel. An entrance that begins hidden may arrive
already moving; an entrance of what is already visible gathers speed
like everything else.

**Constant speed.** `linear` is for what is already in motion and never
stops within the shot: a ticker, a conveyor, a drift, a scroll. It has
no beginning or end of its own, so on a layer that starts or stops in
view it reads mechanical. `kit.drift` and `kit.push` use it by design.
Omit `ease` on a single move and it is `ease-in-out`; omit it on a key
and the key is `auto`.

**A move can take over at speed.** That is the hand-off described
above, and it is on by default: start a move while the property is
still travelling and it leaves at that speed. Two moves that share an
end point pass it at one speed (`join`); `join: false` stops at each,
and `handoff: false` starts the next from rest. `v0` states the speed
yourself, in units per frame.

**Keys are `auto` unless you say otherwise.** A driver's keys are a
monotone cubic by default: at rest only at the first and last key,
carrying speed through every key between them, never overshooting
between two. A key says "the value passes here", not "the value stops
here". Use it for a path through poses — a camera through waypoints, a
card around a corner — and write `ease` on a key only where the
character of the motion should change: `"hold"` to stop, a spring to
settle, a curve to accent.

The rest of the vocabulary, by what it is for: `standard`,
`emphasized-accelerate` and `productive` / `expressive` (the design
systems' utility curves), `expo-in-out` (wipes and big transitions),
`back-out`, `anticipate` and `anticipate-overshoot` (cartoon wind-up and
overshoot; a spring is better for anything physical). Custom: `interp:
{"bezier":[x1,y1,x2,y2]}`. Zooming `scale` between two non-zero values:
add `interp: "log"` so the move spaces by ratio — one steady zoom rate
instead of a rush at the end. A camera is not an object: it eases in and
lands soft (`camera`, `glide`), and springs and overshoot stay off the
lens.

```js
// The card crosses in two moves: the first is still moving at the seam, so the
// second leaves at that speed and lands soft. The y keys are auto, so the card
// passes the middle pose still moving instead of stopping on it.
const card = kit.card({at: [-700, 0], w: 520, h: 300}).id;
miran.animateProperty({layer: card, property: "position.x", from: -700, to: 0, start_frame: 0, duration_frames: 18, ease: "linear"});
miran.animateProperty({layer: card, property: "position.x", to: 620, start_frame: 18, duration_frames: 24, ease: "arrive"});
miran.setPropertyDriver({layer: card, path: "transform.position.y", driver: {keyframes: {keys: [
  {frame: 0, value: -30}, {frame: 18, value: 90}, {frame: 42, value: 0},
]}}});
return miran.motionSummary({layer: card});
```

## Organic float and wiggle

`wiggle(freq, amp, octaves=1, amp_mult=0.5, t=time)` adds seeded
gradient noise to the property's value; on position it moves both axes
as one point (install on `path:"transform.position"`; a per-axis path
changes only that axis). Axes and octave lattices are offset and sampling
sits off the lattice, so they do not pause on a regular beat, the velocity at
a lattice point is never zero, and a frequency that is a multiple of the
frame rate (or a `posterizeTime` step) cannot freeze the channel.
Scalar wiggles still turn around naturally.
Frequency is in lattice cells per second; amplitude sets the motion's
strength and pins its reach — the same amplitude wanders within 10% of the
earlier value-noise wiggle at every octave count and `amp_mult`. The motion
is faster because it never stops: judge a drift by eye, not by the old speed.
Use a `noise` driver for idle life (`amplitude:6, frequency:0.4, seed:7`),
or `kit.float(id, {amp:6, freq:0.35, seed:7})` on the anchor to keep it
independent of position moves. These use the same gradient noise; a driver
gives each axis its own seed and phase, so two axes never move in lockstep
even with the same seed.
A project loaded from format 3 or earlier keeps the old sampler for its
wiggle and noise drivers, so its animation does not move under its owner;
`setCompositionField {field:"noise_sampler", value:"v2"}` moves a composition
to the fixed one. Expression `noise()` and particle turbulence keep their
earlier values.

## Springs: physical settle

Presets (Apple's duration/bounce model): `smooth` (no overshoot),
`snappy` (under 1% on `animateProperty`, about 1.7% in keys; crisp, the
premium default), `bouncy` (~5%, playful only),
`gentle` (slow, soft), `punchy` (fast kick). Use as `ease:"spring:snappy"`;
on `animateProperty` a named spring lands over `duration_frames`, with its
small remaining residual faded smoothly over the final quarter to reach
the exact target with zero velocity (the end's energy bound must be within
0.5% of travel or 0.25 px, whichever is larger). Property-driver springs
use the same landing, timed from their stored spring coefficients.
Pixel tails snap only when the remaining-energy bound is below 0.02 px and
speed is below 0.05 px/frame, so a slow target crossing keeps its bounce. Custom:
`{"spring":{"duration":0.5,"bounce":0.15}}` (seconds; bounce 0 none, 0.15
crisp, 0.3 playful).

In keyframes, each preset uses a single cubic curve, within 2% of the real
spring (bouncy within about 2.5%), and lands exactly on the next key.
Bouncy has one overshoot per segment; a single cubic cannot match its
early peak and return closer than about 2.3%.

A spring that starts with a hand-off speed lands a little after
`duration_frames` when it carries a lot, still without a jump. The landing
fade keeps the named presets within 0.45% of the physical spring and a
custom spring with bounce of 0.7 or more, or -0.5 or less, within 0.57%.

Bounce scales with size: 5% overshoot on a badge is charm, on a full-frame
card it is a malfunction.

## Timing: the eye sets the length

A duration is the time the eye needs to follow the move, not a number
to hit. A state change on a button is a blink; an entrance is a breath;
a hero move gets a held beat. Length follows distance and mass:
something that crosses the frame takes longer than something that
shifts a few pixels, and a big thing moves slower than a small one.
Watch a draft and trust it — when a move feels slow, it is.

**Count in beats when there are beats.** `kit.music` returns
`beat_frames`, `detectBeats` gives them for a track, and `kit.snap` puts
a frame on the grid. Land accents on the beat and let the phrase set
the timing; with no music, read the line aloud and cut on the breath.

**Choose the frame rate as a format decision.** UI and tech films run
at 60 fps: a cursor, a small card and a camera push travel half as far
per frame, so the eye reads them as smooth rather than stepped, and the
shutter's smear is finer. Film-look pieces, dialogue and footage keep
24 or 30. Frame numbers follow the composition: at 60 every explicit
frame count doubles while the seconds stay the same, and beat frames
come from the music at that rate (`createComposition({fps: 60})`, or
`fps_num: 60000, fps_den: 1001`). The kit's own defaults are written
at 30 fps and read at the composition's rate — a card's entrance, a
counter's count, `kit.words`' stagger, `kit.cursor`'s hop all last the
same seconds at 24, 30, 60 or 29.97. Pass `dur`, `stagger` or `start`
only when you want a specific frame length.

**Distance belongs to the style.** Calm pieces travel a little and
scale from near full size; energetic ones arrive from off-frame, slam
from twice, whip out. Scaling up from exactly zero reads as a template
unless the pop is the point. A fade is always shorter than the move it
rides with.

**Words are read at rest.** Give a line the time you take to read it
aloud, plus the breath before it, and keep a push, zoom or swing out
from under words being read. The hold around them stays alive: a
drifting gradient (`procedural.angle`), a `noise` driver on background
art, a slow `kit.drift` push on the scene group (a tilt only when you
ask for one), `kit.punch` on a hit. The words themselves sit still
while read.

## Decouple channels

Things landing with every property arriving on the same frame is the
clearest machine tell. Offset rotation or scale from position by 4-10
frames and give them their own ease:

```json
{"layer":"Card","property":"position.y","from":-80,"to":0,"start_frame":0,"duration_frames":22,"ease":"arrive"}
{"layer":"Card","property":"rotation","from":-4,"to":0,"start_frame":4,"duration_frames":26,"ease":"spring:snappy"}
```

`kit.enter` does this for you: the position leads, the opacity follows a
beat later and the scale settles last, so the three never stop on one
frame (at a very short duration the fade shortens instead of outlasting the
scale). The same ear applies to a hit: `kit.punch` overshoots and rings
down onto its value (it turns round at zero speed, so there is no stop in
the middle), and `kit.shake` is a decaying sine with its own phase on x and
y, not a back-and-forth, so it dies away the way a struck frame does.

## Slow moves are measured per frame

A drift or push that looks lively at 30 fps over two seconds crawls over a
five-second shot, or at 60 fps: the same distance spread over twice the
frames. Below about 0.3 px a frame text edges shimmer instead of moving, so
either let the move be clearly there or leave it out. `kit.drift`,
`kit.push` and `kit.parallax` size their default distance to the shot's
frames for this reason; when you give a distance yourself, think in pixels
per frame, not in totals. `kit.push`'s default is capped at 1.25, so a shot
past about 400 frames gets less than 0.3 px a frame from it: give `to`
yourself on a long one.

## Text animators

```json
{"layer":"Headline","animator":{"from":{"y":-40,"opacity":0},"start_frame":8,"stagger_frames":2,"spring":"snappy"}}
```

`from` is the starting pose relative to rest (px y up, degrees) with
absolute `scale` and `opacity`. The gap between units is what the eye
reads: a frame or two and the word lands as one gesture, a wider gap
names each letter in turn, and past a word's width it becomes a
typewriter. Set the gap from the gesture you want, not from a preset.
`by: "words"` or `"lines"` moves whole words or lines, scaling each
about its own centre, in one layer (`kit.words(text, {one_layer: true})`
builds on it). Recipes: `guide {topic:"text"}`.

Give the type a spring with some bounce in it — `bouncy`, or a
`{duration, bounce}` with a little bounce — and the letters travel
through their resting pose, overshoot a touch and settle back, the
way a hand-set title lands. Translation, scale and rotation all ride
through; opacity alone never passes fully opaque, so a fade still
reads clean at the peak. Keep the bounce small on big type: a few
percent of the travel is charm, more turns a headline into a toy.

The stagger does not have to march evenly. `stagger_shape` spreads
the same total span differently: `ease-in` when the first letters
should arrive in a rush and the tail drift in, `ease-out` for a
gathering start that lands in a rush at the end, `ease-in-out` for a
cascade that breathes at both ends, `stagger_shape: "random"` with a
`seed` to deal the starts out of order for a playful scramble (the
same seed always deals the same order, so the scramble you approved is
the one that renders). `order: "reverse"` brings the last word home
first.

```json
{"layer":"Headline","animator":{"from":{"y":-36,"scale":0.9,"opacity":0},"start_frame":6,"stagger_frames":2,"stagger_shape":"ease-in","spring":{"duration":0.6,"bounce":0.2}}}
```

A project saved before 0.1.6 keeps 0.1.5's clamp: its springs stop at
rest instead of overshooting. `setCompositionField {field:
"text_spring", value: "v2"}` moves one old composition across; new
compositions are on the signed spring already.

## Motion blur: the shutter, not an effect

It is on for new work: `createComposition` and the comps the tools
create start at a 180° shutter, phase centred, adaptive taps, and
`kit.scene` turns it on wherever the blur was never a choice (a
composition whose blur was set explicitly keeps its setting; saved
projects keep theirs). It is the eye's own smear: at 180° each frame
integrates half of that frame's travel, so anything moving faster than
the eye can read arrives as a streak instead of a row of copies. That
is most of what a film moves — a cursor crossing a panel, a card
whipping out, a camera push.

You do not choose it per move, because it decides per frame: adaptive
sampling measures on-screen travel and spends taps where the travel is,
so a slow drift gets a whisper, a whip gets a streak, and a still frame
costs nothing. What you choose is the look — `shutter_angle` in degrees
(180 film, 90 crisp, 360 dreamy), `shutter_phase` for the open window —
and only turn it off on purpose, when the picture must read as graphics
rather than photography: a hard-edged title in a fast push, an overlay
that should stay crisp. `setCompositionField {field:
"motion_blur_enabled", value:false}` records that choice.

## Through a cut

A cut is not a reset for motion. If the outgoing shot is moving, the
incoming one is already moving when it appears: start its first move a
frame or two before its `in_frame`, so it enters mid-flight, or let the
move take the speed the last one had (`handoff`). Two shots that both
arrive at a cut and stop there, then start again from rest on the
other side, read as two clips rather than one film, and the audience
feels the seam even when the cut itself is hidden. The picture carries
too: both sides smeared by the shutter while travelling the same way
read as one movement, and a still frame on either side of a fast move
breaks it. miran-direct turns this into joins ("Joining shots").

## Check the motion

`critiqueMotion {}` (how the moves read, with fixes), `motionSummary
{layer}` (every move, path, follow and timing), `getProperty {layer, path,
frame}` (the evaluated value), and a `renderContactSheet` at the key poses.
`inspectMotionActivity` measures the whole piece's energy over time and
flags dead holds, a weak opening and a frozen ending. Judge feel with a
`quality:"draft"` MP4 of the section.
