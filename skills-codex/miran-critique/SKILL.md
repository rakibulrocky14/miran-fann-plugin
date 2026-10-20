---
name: miran-critique
description: Measures and reviews Miran Fann work instead of guessing - contact sheets returned as images, critiqueLayout collision, safe-area and frozen-hold checks, critiqueMotion (arcs, eases, lockstep, follow-through), a creative-director review checklist, an independent-reviewer loop, critiqueEdit and footage watch for cuts made from footage, critiqueDesign repairs, validateFinalDesign pass/fail, pixel diffs, text and camera coverage, and SVG fidelity. Use before claiming anything in Miran looks right, and after every change.
---

# Measure and look

For a video/animation task without an existing reference study, search for
suitable reference videos unless the user opts out, requests offline work, or
limits reference research to supplied videos. Load the sibling **miran-reference** skill;
judge the task-specific choices against the inspected references and the brief.

> **Lean tool list:** a call written below as `name {params}` is `miran.name({params})` inside `runScript` (or `query` when it only looks) unless `name` is in your tool list; exports, jobs, voiceover, downloads and panels stay direct calls.

Miran's advantage is not that it renders — it is that it can **tell you what
is wrong** with numbers and evidence, and show you the frame. Guessing is the
alternative, and it loses.

Build with **miran-create**, animate with **miran-motion**, move the camera
with **miran-camera**, export with **miran-render**. When the film is built,
**miran-polish** runs the last pass in order (content, motion, type, colour,
sound, export) with these tools.

## Guardrails

- **Never claim it looks right without looking.** `renderContactSheet` returns
  the grid as an image part: you have eyes on the output. Use them.
- **Never claim it measures right without measuring.** "Probably fine" is not
  a state this engine produces; the critics below are.
- Critique tools are read-only, so run them as often as they are useful.

## The loop

```
renderContactSheet → LOOK → critiqueLayout + critiqueMotion → read findings
                   → fix one thing → re-render → re-critique → …
                   → inspectMotionActivity → export
```

(A design spec from **miran-design** takes `critiqueDesign` and ends in
`validateFinalDesign`.) One fix per iteration. The findings name what they
found; changing three things at once makes the next critique unreadable.

## What to look for on every sheet

```
- [ ] One obvious hero per frame; everything else clearly secondary
- [ ] Text readable at a glance (>= 28 px body at 1080p), held long enough and still while it is read (`read_in_motion` names words that travel during their read)
- [ ] No empty frames between scenes (`dead_frame`): the next scene overlaps the last one's exit
- [ ] Margins consistent (5-8% of the frame); every block of type on one ink edge or centre, `misaligned` at 0; on 9:16 no empty half and type inside `kit.safe({platform})`
- [ ] Specific to this product (another brand could not reuse the frame) and bold enough to screenshot
- [ ] The pace the style asked for (the pacing table in **miran-direct**: a launch film changes something every 1-2 s, a calm explainer holds a read longer); a drift is not a change; type alone on a field is a breath, not most of the film; several different devices, not one fade-and-rise
- [ ] The brand's palette, written with its source in the plan (one accent for calm styles; colour fields for bold ones); not a house look (navy glow, cream-ink-orange, black-and-orange) unless that is the brand
- [ ] The subject fills its shot: no small window or lone line in a big empty field, no frame of flat colour with nothing on it
- [ ] Mid-motion frames intentional: no collisions, nothing half-offscreen by accident
- [ ] Pushes and tilts land clean: no half-word or half-card at the edges (unless the crop is the design); the UI is read face-on
- [ ] Motion language consistent: same eases and stagger, exits faster than entrances; arcs and follow-through (`critiqueMotion`: fix every finding it names; the score grades only the moves that exist)
- [ ] No clip-art primitives (giant circles, default blobs: `critiquePrimitives`), no effect soup
- [ ] Launch films: the arc readable from six frames, the reveal on the music drop, shot sizes alternating, every camera move motivated, the director's review scored
```

Name what is wrong in words before fixing it; **miran-direct** section 8 is
the director's review (ten scores; fix the two lowest before exporting).
`frames: "auto"` picks the key poses: the middle of every scene, a few frames
either side of each cut and the last frame (scenes come from the `Scene N`
nulls `kit.scene` makes; without them, 12 evenly spaced frames). Every cell is
labelled with its frame and time. Add frames by hand for each push and tilt
landing (full size with `renderFrame`: a thumbnail hides slivers).

## Contact sheets: the eyes

```json
{"tool":"renderContactSheet","arguments":{"out":"qa/draft.png","frames":"auto"}}
```

Returns `{"out":"qa.png","width":…,"frames":3,…}` **plus an image part** — the
PNG itself, base64, `mimeType image/png`. You are looking at the actual
rendered frames, including everything that only shows up in pixels: text
overflow, the wrong font fallback, a matte eating the wrong layer, a chamfer
that is too hot.

`renderFrame` also returns an image part for single frames. Prefer the sheet:
four to six beats of the film in one look.

The sheet result also carries `layout`: landed collisions and text past
the safe area in those frames, measured from geometry (thumbnails hide
them; `platform` checks it against an app's zones). Fix each one before
moving on.

## critiqueLayout: the whole film

`critiqueLayout {}` samples every few frames and reports, with frame ranges
and layer names: text colliding with art or other text (`text-collision`,
`text-overlap`; a plain shape under the text, like a highlight bar or a panel,
is design), lines through letters (`stroke-through-text`: rays, rings or
connectors crossing a word, even while they play; shorten them or tuck them
under the card), art painted over text (`text-hidden`), text on a surface of
nearly its own colour, with the contrast ratio (`text-low-contrast`), text past
the safe area (`outside-safe`; on a 9:16 piece pass `platform: "reels"` /
`"tiktok"` / `"shorts"` and text under the app's top bar, caption block or
button column is reported too; `safe` takes a margin fraction or `{left, top,
right, bottom}`), text under ~2% of the frame height (`tiny-text`, about 22 px
at 1080p, so 28 px body text is still yours to check), stacked lines of one
block that share no left edge, right edge or centre (`misaligned`, with the
offset in px: a must-fix; rebuild the block with `kit.lockup`, or give the
lines one `at.x`), nearly empty frames or one big empty block (`sparse`,
`empty-area`, with the block's position) and nothing moving for 1.5 s or more
(`frozen`). Only layers that have landed count, so overlaps while things fly in
are fine. Words inside imported SVG artwork (a UI mockup) are art to it: check
those by eye. Run it after the last beat and before every export; `sparse` and
`empty-area` are notes when calm space is the idea.

## Motion activity: the timing

A sheet cannot show time. `inspectMotionActivity {}` renders the range and
reports how much of the frame visibly changes each second, the still holds,
and findings with frame ranges:

- a still hold over ~1 s (add secondary motion or shorten it),
- a weak opening (the first 2 s calmer than the rest: open on your strongest
  motion),
- a frozen ending (keep a slow push or drift on the end card).

On real launch films (0.1.6 study) the energetic ones, Lovable and Claude
Design, sit at about 5-9% median with almost nothing frozen; calm UI films,
Cowork and Cursor 2.0, sit under 2% and hold for 2-3 s, which works only on a
full frame of legible product with a cursor or a voice about to act. Above
~35% sustained is frantic. For a film carried by music, aim for about 5% or
more, and treat a still hold over about a second as a finding (the breath
before the climax excepted). Run it after
every beat and on every draft before the final export. It is the check a
contact sheet cannot do: stills of a frozen film and of a living one look the
same. Fix a hold with a performance (the camera travelling to the part that
matters, a staged reveal, the product changing state), not a fade, and never
by clearing motion that a critic complained about.

## critiqueMotion: how things move

`critiqueMotion` grades the moves that exist; it does not count the seconds
where nothing moves. A launch film measured in the 0.1.6 study scored 100
with half a percent of its frame changing on a typical frame and ten frozen
seconds in twenty-four; and its score drops to 0 while any warning stands, so
a living film can score below a frozen one. Read it after the activity check,
never instead of it, and read the findings rather than the number: mark
atmosphere (dust, sparkles, a breathing spot) `intended`, fix your own moves,
and never remove motion to raise the score. Only atmosphere: a scene group, a
camera move or your own moves marked `intended` hide real faults, and the score
that buys means nothing. A warning on a kit piece's own move goes into your
`feedback` report instead.

`critiqueMotion {}` reads the moves themselves (channels, curved paths,
follows) and, for smoothness, where every layer sits on screen at each frame,
like an animation director, and returns a score, findings with layer and
frames, and a fix that names a tool call for each (apply it, run the check
again):

- `straight_diagonal`: x and y moved as a straight line (move on an arc:
  `animateProperty {property:"position", to, arc:0.2}`; a path starting on
  the same frame takes over both axes),
- `linear_ease`: constant speed on a move that starts or stops on screen,
- `entrance_accelerates` / `exit_decelerates`: the ease faces the wrong way,
- `lockstep`: three or more layers starting together with identical timing
  (a radial burst, ring of dots or petals is one gesture and passes),
- `uniform_timing`: one duration for almost everything,
- `no_follow_through`: nothing overshoots, settles or drags behind,
- `teleport`: a jump too fast to read without motion blur,
- `empty_opening`: frame 0 shows only the background,
- `dead_frame`: a stretch mid-film where nothing but the background shows
  (the gap between one scene's exit and the next entrance; overlap them),
- `read_in_motion`: words still sliding, swinging or zooming while they are
  read (land them and hold still for the read; move the camera or the art
  around them instead; a slow push is fine),
- `held_before_move`: a layer shows its first move's start value (a flash
  at 85%, a spark at 2x, full-strength aberration) from its in point until
  the move begins; set its `in_frame` to the move's start,
- `first_frame_jump`: a visible layer leaves rest with a big first frame, or is
  at full speed on its first frame (linear keys). Strong ease-outs (`smooth`,
  `emphasized-decelerate`) are for a layer that starts hidden; on screen,
  leave rest on `arrive`, `glide` or `easy-ease`. Keys are fixed on their
  keys (`setPropertyDriver` with keys), moves with `animateProperty`,
- `dead_stop`: it arrives at speed and stops in one frame; `glide`,
- `velocity_jump`: the speed steps in one frame while the layer moves, as
  when a second move takes over mid-flight and restarts at its own speed;
  the fix puts auto keys on every axis that moves, keeping the endpoints. A
  step that turns the layer round (a bounce, an impact) is meant and is not
  judged,
- `tail_crawl`: a creep of 0.6-9 px a second for over 0.2 s at the end of a
  move, which shimmers on edges and text (a spring's settle is physics and is
  not judged); another ease, a shorter move, or hold the layer still,
- `periodic_stop`: it comes almost to a stop again and again on an even beat
  without turning round (lattice noise, wiggle, five eased moves back to back);
  auto keys carry the speed. A bob, a pendulum or a ping-pong turns round at
  every stop and is meant,
- `stop_between_moves` (a note): one stop between two moves the same way, so
  they read as two moves; auto keys through the join,
- `fast_no_blur`: it moves faster than the eye can follow (2.5% of the width a
  frame) with no shutter or blur effect:
  `setCompositionField {field:"motion_blur_enabled", value:true}`,
- `transition_peak`: a push, slide or whip whose fastest frame crosses a
  sixth of the width while the blur (shutter or a sized blur effect) covers
  under 45% of it; blur on, shutter 180.

Speeds and lengths are judged in seconds, so 24, 30 and 60 fps get the same
verdict. A 3D camera is judged through its own motion (jolts, dead stops and
blur); `ignore: ["camera"]` leaves it out. A long film is sampled across its
whole length; a `sample_cap` note names what the cap left out, and
`critiqueMotion {frames:[from, to]}` checks a range on its own.

Snaps on hold keys, stepped motion (posterize, hold keys, `posterizeTime`) and
layers marked `intended` are meant and are not judged; a stage that is marked
carries its children, and a stage's children that ride one move are one
finding.

It does not judge reveals (a track-matte source, a layer that never shows, a
cover plate such as a typing wipe, mask or trim animation). A move you chose on
purpose, like a constant-speed scroll, goes in `ignore: [layer names or ids]`
(`renderContactSheet` takes it too); the result's `not_judged` lists what was
left out. Moves overridden by a later move are skipped: the critic judges what
plays. Retime with `replace: true` or `clearAnimation`. Fix every finding it
names: the score grades only the moves that exist, so it cannot say the film is
alive. Contact sheet results also carry the findings near their frames
(`motion`).

## critiqueEdit and watch: footage and the cut

For a cut made from footage (**miran-edit**), `critiqueEdit` is the last look
before export. It is a direct call that renders one small draft of the whole
composition (a long film returns `{job}`: poll `jobStatus`) and checks `frames`
(black, frozen), `flashes`, `jump` cuts, `eye` jumping across a cut, `beat`
(montage cuts off the music), `mixed` (generated shots that do not match the
footage), `audio` (clicks at cuts, clipping, loudness, music over speech, dead
air), `captions` (speed, length, over a face or the app's buttons: pass
`platform`) and `retention` for shorts. Each failed check gives its three worst
moments with a small image and the fix: look at the images, fix the worst, run
it again (`checks` reruns one). A flag can be the style (jump cuts in a vlog):
say so. `flashes` is an approximate photosensitivity check after the WCAG
rules, not a certified test; never call a film certified safe.

To look at footage itself, `footage {action: "watch", asset, start, end}`
returns one picture: the frames of a stretch over its waveform, silences,
motion, words and cuts. Use it before cutting a moment and after changing one.

## Independent review: builder is not judge

The builder is the worst judge of its own work. When you can start a fresh agent
(a subagent), give it only the rendered film or frames, the brief (and the
user's own words) and the bar, never your reasoning or what you fixed. Ask for a
verdict (ship / one more pass), defects ranked with timestamps and screen
regions, and the three highest-value fixes. Fix the biggest gap first,
re-render, and give the next round to a new reviewer that checks each prior item
and looks for regressions. Stop when what is left is cosmetic.

## critiqueDesign: the numbers

```json
{"tool":"critiqueDesign","arguments":{"spec":{…},"frame":30}}
```

Returns:

```json
{"critique":{"findings":[
  {"code":"fullscreen-circle","severity":"warning","confidence":0.7,
   "elements":["frame"],
   "evidence":"content spans 0,0–1919,1079 (100% of frame) at 0.69 fill: reads as one giant disc",
   "metrics":{"bbox_fraction":1.0,"fill":0.69},
   "suggested_repair":"break the disc: crop, mask, or scale below half-frame"}],
 "inspected_layers":4,"inspected_regions":2},
 "errors":0,"warnings":1,"score":1}
```

How to read it:

- `findings[].code` is the machine name; `evidence` is the human reason with
  the actual measurements; `suggested_repair` is the fix to apply.
- `errors` must reach **zero**; `warnings` must be *understood* — a warning you
  can explain is a decision, one you cannot is a defect.
- Pass `"frame"` to include the pixel lens at that frame — static structure
  plus what the pixels actually do.
- Related: `critiquePrimitives` flags full-frame circles (the "one giant blob"
  check); `compareDesigns` diffs two specs at a frame.

## validateFinalDesign: the gate

`validateFinalDesign {spec, frame}` returns `{pass, errors, warnings,
findings}` — pass requires zero errors. Run it once at the end, after the
loop converges. Passing does not mean good; it means nothing measured is
broken. Your judgement on top of a passing gate is the deliverable.

## Pixel diffs: did my change do anything?

```json
{"tool":"compareImages","arguments":{"a":"before.png","b":"after.png"}}
```

Returns `fraction` (pixels differing), `edge_mean_norm`, `frac_over_8`,
`frac_over_32`, and `geometry_edge_confined`. The last one matters: a chamfer
tweak should be **edge-confined**; if changing the stroke recoloured the
interior, something else moved. `inspectImage` gives dims, mean colour and
alpha coverage for one PNG.

After a "small change", diff before/after. A `fraction` of 0 means your change
did nothing: find out why before moving on (this is how silent no-ops get
caught).

## The exported file

`renderVideo` checks what it wrote (`check: true` by default): it decodes the
file, measures loudness, true peak, size and bitrate, and returns a strip of
eight frames from the encoded video with its verdict. Look at that strip: it is
the film as the viewer gets it (a black frame, a frozen tail, banding). Fix
every `problem` it names and export again. `checkExport {path}` checks any file.

## Camera QA

`inspectCameraTrajectory` (the exact renderer pose per frame), `inspectCameraCoverage`
(focal layers off-frame) and `validateCameraMove` (flips, spikes, margins,
focal jumps, conflicts). Push rules and tilt limits: **miran-camera**.

## Other gates

- `compareSvgFidelity` — renders the resvg reference against the native
  vector layer/block and reports PASS/FAIL with metrics. Run it on imported
  artwork before building on top of it.
- `validateProtectedSettle` — pixel-verifies protected brand artwork at its
  settle frame.
- `validateProject` — validation profiles over the whole project.
- `inspectTextCoverage` — which fonts were actually used and which glyphs are
  missing (a fallback changes the look silently).
- `motionSummary` — channels and drivers per layer; confirms the animation you
  think exists does.

## Failures you will hit

- **Everything passes and it still looks wrong** — the gates measure structure
  and pixels, not taste. Name what is wrong in words, turn it into a constraint
  (`required_elements`, `prohibited_elements`, brand rules) and re-run.
- **Score got worse after a repair** — repairs are proposals, not verdicts;
  `applyRepair` is checkpointed, so restore and take the other finding first.

## Tool reference (generated)

`TOOLS.md` in this folder lists every QA tool with its exact parameters,
generated from the runtime registry.
