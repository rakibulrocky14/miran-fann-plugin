---
name: miran-video
description: Makes a complete motion-graphics video with Miran Fann, end to end - a concept specific to the subject, real product UI (screens written as HTML, live sites captured, screen recordings), a directed board paced for the kind of film, each beat built as one runScript with kit (kinetic type, UI cards and cursors, code, arrows and diagrams, charts, 3D screens, camera moves, transitions), reviewed like a creative director, scored with generated music and sound, voiced and captioned, exported and checked. Use when the user asks for a video, animation, promo, teaser, launch film, explainer, tech or product demo, reel or vertical short, title sequence, kinetic typography, voiceover or captions, a dialogue cartoon, a video like a reference, or "motion graphics" made with Miran.
---

# Making a video with Miran Fann

> **Lean tool list:** a call written below as `name {params}` is `miran.name({params})` inside `runScript` (or `query` when it only looks) unless `name` is in your tool list; exports, jobs, voiceover, downloads and panels stay direct calls.

Miran is a GPU motion-graphics engine driven by tools (MCP server `miran`).
This skill is the workflow and the build. **miran-direct** is the directing
half (production order, shot grammar, the arc of each kind of film, the
director's review): read it for every film. Deeper skills: **miran-create**
(layers, SVG, HTML), **miran-motion**, **miran-camera**, **miran-critique**,
**miran-render**. The server documents itself: `guide {topic}`,
`describeTool {tool}`, and `miran.help("kit.title")` inside a script for one
kit piece's options.

## Codex: how these films go wrong, and the answer to each

Codex films come out clean and tasteful, and too often as slides. Before
planning, open the **miran-direct** skill (its `SKILL.md` is in the folder
next to this one) and read all of it; for a launch film also `guide
{topic:"directing"}` sections 5-7. Write the plan to `plan.md` before any
build call. Then watch for the three failures field tests showed:

- **Slides**: centred cards fading in, one after another, the camera still.
  Every shot gets a size and a camera move with a reason (miran-direct
  section 2): the product hero is `kit.screen3d` (tilted, swinging face-on,
  pushing into a panel), the feature shots push into the part that matters
  (`kit.frame`), and each feature gets its own layout and device.
- **One entrance for everything**: choose the entrance from what the thing
  is and does: a claim slams (`kit.words`), a headline rises from its own
  line (`style: "mask"`), a pill grows, a reveal resolves from blur, a row
  arrives because the cursor clicked. Two beats in a row never share one.
- **Type blocks at guessed coordinates**: lines that read together are one
  `kit.lockup` (end cards `kit.endcard`); never stack `kit.title` calls by
  eye.

Cartoons are staged, not filmed wide: an establishing wide, then two-shots
and close-ups, a reaction to every line (miran-direct section 3). For an
illustration you cannot draw well in SVG, generate it as an image, import it
(`importAsset`, `createLayer {kind: "image"}`) as a plate, and move the
camera and type around it.

Your runs finish fast: spend the saved time on the second pass. Render the
whole film with `renderContactSheet {frames: "auto"}`, score it with the
director's review (miran-direct section 8), rebuild the two weakest beats,
and only then export.

## The workflow

Films come out average when building starts before directing. Copy this
and tick it off:

```
- [ ] 1. Brief: the one sentence to remember, audience, length, format, brand; the subject's truth
- [ ] 2. Real material: the brand's own colours, type and logo (logo SVG, site, repo CSS; captureSite / brand capture); its screens as HTML (importHtml); the user's footage; real copy and numbers ("Real material first")
- [ ] 3. Direct (miran-direct 1-7): three concepts (one rules out the obvious idea, one an unusual format), the treatment, the arc, the board, the design system, saved in plan.md
- [ ] 4. Spine: kit.music {reveal, ending} or voiceover / kit.dialogue {dry: true}; snap the board to it
- [ ] 5. Styleframes: opening, signature shot, end card as finished stills; openViewer link to the user
- [ ] 6. Build: one runScript per kit.scene, a sheet after each, fix in place
- [ ] 7. Review: renderContactSheet {frames: "auto"}; critiqueLayout, critiqueMotion (dead_frame, read_in_motion), inspectMotionActivity; the director's review; fix the two weakest beats
- [ ] 8. Finish: joins, light, sound on every action, captions if it talks, kit.finish, loudness
- [ ] 9. Export: a draft for timing, then the final; read the check that comes back (frames from the file, size, loudness)
- [ ] 10. Report: what was checked, what came from the brief and what you invented
```

**plan.md.** Write the plan as a file next to the project (the film's
folder, `miran/<name>/plan.md`; in a host without file tools, in your
reply). Open it with the one sentence (who it is for, what they must
remember), then the treatment, the board and the design system. Re-read it
after any pause or context compaction, and keep it true as the film
changes: it is how the next session, or the user, knows what the film is.

**The viewer.** Call `openViewer` once and give the user the link: a live
timeline, layers and preview that update as you build. The user can edit
there; your next result carries `viewer_edits`. Build on their edits, never
undo them.

## Real material first

Launch and tech films are judged against the real product. UI redrawn by
hand as positioned SVG reads as almost real, and almost real reads as fake.
`guide {topic:"ui"}` has the details and runnable examples.

- **A product with a website**: `captureSite {url}` photographs it and reads
  its palette, fonts, logo, headline and the boxes of its headings, buttons
  and images; `place: true` puts the screenshot in the composition with the
  boxes in composition px, ready for pushes. `brand {action: "capture",
  url}` saves the palette, fonts and logo as a brand to apply.
- **Art lifted out of a screenshot** (an orb, a blob, a glow cut from a
  captured page) carries the page's dither and noise; scaled, moved or used
  as a fill, it reads as static, the cheap-video look. A screenshot works as
  a screen at its own size. Rebuild its art in the engine: a circle with a
  radial `gradient` and a soft `gaussian_blur`, or `kit.blob` for a living
  shape, stays smooth at any size.
- **Art the engine cannot draw** (a painted plate, an illustration, a
  texture, an object): `generateImage {prompt, aspect}` with the user's
  OpenRouter key from Miran Settings. Write the film's look once in plan.md
  (the brand's palette, the medium, the light) and put it in every prompt,
  so the images belong to one film; look at each before it goes in.
  `svg: true` traces it into vectors to animate: flat shapes and few colours
  trace cleanly, a photographic plate stays an image. Never ask it for
  words, logos, UI or charts. The same prompt returns the saved file; each
  new image costs the user money.
- **A screen to show** (an app view, a dashboard, a chat, a settings page):
  write it as HTML and CSS and `importHtml` it. Chrome lays it out with real
  fonts, flexbox, grid and shadows, and every element with an `id` comes in
  as its own layer, so rows arrive, buttons get clicked and cards lift.
  Size it for the film: body text 26-32 px in a 1200-1440 px page.
- **The user's footage or a screen recording**: `createLayer {kind:
  "video", path, fit: "cover"}` (trim with `src_in` / `src_out`, `speed`),
  `kit.zoomFollow` to push in on each click, `kit.cursor` for a clean
  pointer, `kit.screen3d` to tilt it.
- **Code**: `kit.code(src, {lang, file, reveal: "type", highlight})`.
- **Diagrams**: `kit.arrow(from, to, {label})` between layers, `kit.flow`
  for chips and tokens in wrapped rows, `kit.line {head: "arrow"}`.
- **Marks, icons, illustrations**: SVG (`kit.svg`, `guide {topic:"svg"}`).
- **A finished shot to start from**: `template {action: "list"}` (lower
  thirds, stat callouts, notifications, title and end cards, charts).

- **A video to match** ("make it feel like this"): `reference {video}`
  measures its shot length, transitions, motion, camera, palette and type;
  write your look from those numbers, keep your own concept, and after a
  draft `reference {action: "compare", video}` lists what differs, worst
  first (`guide {topic:"reference"}`).

When there is no real material, say so in the plan and design screens in
the product's style; never present invented screens as the real product.

## Craft as tests

Rules about pixels are what the critics measure. Taste is a set of questions
asked of every move and every frame:

- **Every movement finishes a sentence.** "This pushes in because the
  cursor is about to click it." "This slams because it is the claim." If you
  cannot finish the sentence, cut the move. Motion that means nothing is a
  screensaver.
- **A hold stays alive because something in it changes meaning**: a state
  flips, a number climbs, the cursor travels, a light sweeps, a row arrives.
  A slow `kit.drift` is the fallback for a hold with nothing to change, and
  it moves the picture around the words, never the words being read
  (`critiqueMotion` reports `read_in_motion`).
- **Rule out the obvious.** Name the concept every other video on this
  subject would use, and do not make it. One of your three concepts takes
  an unusual form: a letter, a countdown, a front page, a map, a receipt.
- **Steer between the two failures.** Slideshow: everything arrives early,
  then freezes. Screensaver: ambient motion that says nothing. The film
  moves when the story does.
- **Check your lazy defaults before the first build and again before the
  export.** Each default a model reaches for, with the question that
  replaces it: a centred card fading in (what does this thing do when it
  arrives?), one ease everywhere (is this a snap, a settle, a glide?), the
  same transition at every cut (what do these two shots share?), a glow
  behind every hero (where is the light?), the default sans (what does this
  brand's voice sound like?), lorem ipsum or round numbers in a UI (what
  would this product really say?).
- **Carry motion through the cut.** The outgoing move's direction and speed
  continue into the next shot, or the cut lands on a stop, on purpose.
- **Say what you invented.** UI, numbers, quotes and claims not from the
  brief are listed as invented in the final report.

## Review

Look where films break: at the seams. `renderContactSheet {frames: "auto"}`
samples each scene's middle and both sides of every cut (`kit.scene` makes
the scenes), every cell labelled with its frame and time; a number (`frames:
12`) spreads that many evenly. Then:

- `critiqueLayout` (collisions, safe area, contrast, `misaligned`) and
  `critiqueMotion` (aim for 85+; `dead_frame` is a stretch with nothing but
  the background, `read_in_motion` is text travelling while it is read);
  `inspectMotionActivity` for holds and dead time.
- The director's review (miran-direct section 8): score each line 1-5, fix
  the two lowest, look once more.
- An independent reviewer when you can start one: give it frames (not the
  session), the brief and the bar; ask for defects ranked with timestamps.
  The builder is the worst judge of its own work.

What usually gives a film away on the sheet: type alone on a field held
for seconds; words read on the move; a push that crops half a word at the
frame edge; a tilted screen asked to be read (read it face-on or in the
push); product UI with 14 px text (push in until body text is 28 px); the
same layout, ease or entrance beat after beat; on 9:16, an empty half.
Never claim a beat works without looking at its sheet.

## Conventions that trip everyone

- Time is **frames**; render ranges are end-exclusive.
- Position is **px from the composition centre, +y UP**. Effect offsets and
  `screen` params are top-left, y down.
- Rotation in degrees (positive = counter-clockwise), scale 1 = 100%,
  opacity 0..1, colours `"#RRGGBB"`.
- Imported SVG parts rest at the fit scale (`rest_scale`): animate scale
  relative to it, or use `kit.enter`, which lands on the rest.
- `setProperty` sets a rest value; `animateProperty` moves play over it. A
  layer that appears later needs its entrance to start at that frame.
- Layers are 2D by default; the camera moves only `is_3d` layers. Real 3D:
  `kit.lit`, `kit.text3d`, `kit.logo3d` (**miran-camera**).
- Paint order: index 0 is the bottom. `reorderLayer {layer, to: "top"}`,
  `{above}`, `{below}` or `{index}`. `kit.group` returns a bare id (`g.id`
  is undefined).
- A composition's length changes with `setCompositionField {field:
  "duration", value: seconds}`; layers that ran to the end follow it.
- Files go in the film's folder (`miran/<name>/`): renders, sheets, sounds,
  voices, captures. An export never replaces a file another session made
  (the result says the name it used).
- Read `warnings` and the script `log`: missing glyphs, fallback fonts, a
  title set smaller to fit the frame, captions skipped because type shows
  the words.

## Build one scene per script

`runScript` runs JavaScript that calls every tool as `miran.<tool>(params)`
plus `kit.*`, with loops, math and each call's result. It is atomic (an
error rolls the beat back) and one undo step. `guide {topic:"scripting"}`.

```js
// Beat 2 (45-90): the claim slams in on the beat, one word set apart, then an arrow
// draws to the proof. Nothing drifts under the words while they are read.
// Palette from the brand (plan.md: "from the logo SVG: periwinkle #598EF2, rose #DD6392, on white").
kit.theme({font: "Avenir Next Condensed", bg: "#F5F8FF", surface: "#FFFFFF", text: "#13204A",
  muted: "#6B7699", accent: "#3D6FF0", accent2: "#E0608F"});
kit.scene({start: 45, end: 90}, ({start, end, group}) => {
  kit.words("Render at the speed of thought", {size: 150, weight: 800, style: "slam",
    start: start + 2, stagger: 4, highlight: {word: "speed", pill: true}});
  kit.line({points: [[-620, -200], [560, -200]], width: 4, head: "arrow", start: start + 24, dur: 16});
  kit.punch(group, {start: start + 14, to: 1.05});   // the hit lands on the highlighted word
});
return miran.renderContactSheet({frames: [47, 52, 60, 70, 80, 89], columns: 3, out: "qa/beat2.png"});
```

Move like an animator: arcs (`kit.move {arc}`), overlap (`kit.follow`),
follow-through (`kit.inertia`), squash, speed ramps (`kit.slowmo`), two
eases and three durations (miran-direct section 5; `guide
{topic:"motion"}`). Almost everything animates like CSS: colours, sizes,
corner radius, stroke, type size and tracking, blur and every effect
parameter. Shapes carry many looks with a few operators (`setShapes {ops}`,
`kit.burst`, `kit.blob`; `guide {topic:"shapes"}`).

## Revising a beat

Fix a beat where it stands. A note on a detail is a short script on the
layers already there, by the ids the kit returned or `listLayers`:
`setProperty` or `setLayerField` for a place, size, colour or text,
`editKeyframes` to move or change one key, `shiftLayerTime {frames}` to
start a piece earlier or later (its own sounds move with it),
`deleteLayer`, `kit.addTo(scene, ids)` for a new layer in the shot. A note
on the shot itself is its script edited and run again: a `kit.scene` with
that scene's name or frames rebuilds it in place, keeping its transitions,
its place in the stack and every other shot; a `kit.transition` run again at
a cut replaces the one there. `undo` takes back only the last script: keep
it for a script that did something you did not mean.

## Score it

A silent video is unfinished. Kit pieces already play their sounds
(whooshes on transitions, keys under typing, ticks under counters, clicks
under the cursor); add the bed and the big moments (`guide {topic:"sound"}`):

```js
// The spine, made before the cuts: the reveal exactly on the drop, the logo on the ending
const m = kit.music({mood: "tech", reveal: 60, ending: 120});
log(m.edit, JSON.stringify(m.sections));   // cut on m.bar_frames; kit.snap(f, m, {to: "bar"})
kit.sfx("impact", 60);
kit.sfx("logo", 120);
const mix = miran.mixAudio({out: "qa/mix.wav"});
return {bpm: m.bpm, peak: mix.peak, errors: mix.errors};
```

Pick the mood with the style (tech, luxury, minimal for product; hype and
playful for energy; epic and dark for cinematic). On calm music (chill,
ambient, minimal, luxury) cut on bars and section changes, not on every
beat. One hero sound per moment.

## Voice it, caption it

Explainers, promos and social cuts usually speak. Write the script with the
board, voice it before building, and land each beat 2-4 frames before its
word (`guide {topic:"voices"}`):

```text
voiceover {action:"pronounce", text:"Ledger reads every invoice"}   // fix guessed names first
voiceover {script:"NARRATOR: Every week, your team loses hours...\n\nNARRATOR: Ledger changes that."}
runScript: const inv = kit.when("invoice");     // inside a kit.scene: the time it is said in this scene
           kit.enter(card.id, {style: "pop", start: inv.start - 3});
           kit.captions({style: "highlight"});   // skips phrases kinetic type already shows
```

The built-in voice is offline and free (`af_heart`, `am_michael`, `bf_emma`,
...). Gemini or ElevenLabs only when the person asks; a missing key fails
with `no-key`: tell them to add it in Miran Settings, never ask for a key in
chat. Calling `voiceover` again with the same `name` re-voices the lines in
place.

The voice is the film's clock. A length in the brief ("a 2 minute video") is
where the script aims, not a wall: when the voice comes back longer, the
composition grows to fit (the result's `extended` says so) and that is the
film's length, unless the user called theirs a hard limit. To come in
shorter, cut sentences and voice again; unchanged lines come back from the
cache. Never squeeze narration to fit: `time_stretch` resamples it (faster
and higher, like a tape), and an out point before the last word chops the
sentence off. To change what a line says, rewrite it, voice it again and
rebuild the scenes cued to its words. `voice_cut` in mixAudio and
renderVideo names any line that is not heard to its end.

## Dialogue cartoons

Read `guide {topic:"characters"}` (it ends in a runnable two-hander). Write
the lines with the joke's timing, dry-run them (`kit.dialogue(lines, {dry:
true})` returns every line's frames and `end` with nothing built), size the
composition to `end` plus an ending beat of 2-4 s (`setCompositionField
{field: "duration"}`), then stage it: cast large (55-75% of the frame
height), open wide, then two-shots and close-ups, the punchline close after
`pause: 1`, the listener reacting the way the line deserves, gestures on the
stressed word, `kit.life` under everything, a gag sound on its frame
(`kit.sfx("boing", f)`). Draw sets 1.2x the frame so close-ups never show an
edge.

## Vertical (9:16)

Compose for the full height: type and art in tiers, art in the lower third,
scale contrast vertically, backgrounds bleeding off the edges. Keep text in
the app's clear box: `kit.safe({platform: "reels"})` (or `"tiktok"`,
`"shorts"`); `critiqueLayout {platform}` checks the same zones. Captions sit
below the centre, three words a phrase.

## Export

```json
{"out": "renders/draft.mp4", "quality": "draft"}
{"out": "renders/final.mp4"}
```

`draft` is half size and several times faster: watch it for timing.
`renderVideo` defaults to the whole composition at `high` (H.264) with the
sound mixed in; `max` is 10-bit HEVC for dark gradients and glow, `master`
is ProRes (`.mov`), `max_mb: 25` fits an upload. Turn motion blur on for
anything fast (`setCompositionField {field: "motion_blur_enabled", value:
true}`; adaptive, free on still frames). Over MCP a long render returns
`{job}`: poll `jobStatus {job}`.

Every export opens the file it wrote and reports `check`: frames decoded
from the MP4 (a labelled sheet, returned as an image), duration, size,
bitrate, audio and loudness, with problems named in `verdict`. Look at it:
a defect that only the encoded file shows (blocky grain, a silent mix, a
file too big to share) is caught there. `checkExport {path}` checks any
video in the workspace.

## The final report

Close with what the user needs to trust the film: the file, its length and
size; what was checked (the sheets, the critics and their scores, the
export check) and what was not; where the UI and copy came from (captured,
written in the brand's style, or invented) and every invented number or
claim; anything you would still change.

Then tell the Miran team, in one short paragraph, the issues you ran into in Miran
itself: what fought you and how you got around it (a tool that misbehaved, a doc that
misled you, something you needed and could not do). `feedback {issues, film}`. Plain
words, the main issues only; "no issues" when there were none.

## Guardrails

- Preserve work you did not make: `listLayers` / `inspectProject` before
  restructuring an existing project; never delete to "clean up".
- `createCheckpoint` before risky sweeps.
- Keep outputs inside the workspace (relative paths).
