---
name: miran-direct
description: Directs any Miran Fann film the way studios do - the production order (brief, concept, treatment, board, design system, music or voice first, styleframes, build, director's review), shot sizes, camera moves with a reason, cutting on the music, light, type, acting and comic timing, and the arc of each kind of film (launch, explainer, dialogue cartoon, 9:16 short, title sequence, data story), with runnable recipes. Use with miran-video for every video, before planning and again for the review.
---

# Directing a film with Miran Fann

> **Lean tool list:** a call written below as `name {params}` is `miran.name({params})` inside `runScript` (or `query` when it only looks) unless `name` is in your tool list; exports, jobs, voiceover, downloads and panels stay direct calls.

A clean render and a film people remember differ in direction, not in
pixels. A directed shot has a job in the story, a size, a camera move
with a reason, a place in the music or the voice, and one thing to look
at. Average AI video skips this: small centred text fading in, one layout
per beat, a crossfade, the next card. Studios work in a fixed order, and
so do you. **miran-video** has the build mechanics (runScript, kit,
scoring, export); this skill is what to build and why.

## 1. The production order

Every film, from a 10 s social cut to a 90 s cartoon, goes through these
stages. Each ends in something written or rendered; do not start one
before the last passed its test. Write stages 1-7 into `plan.md` in the
film's folder before the first build call (briefly: a plan, not an essay),
opening with the one sentence; re-read it after any pause.

| # | stage | you produce | it passes when |
|---|---|---|---|
| 1 | Brief and research | the one sentence the viewer must remember, audience, length, format, brand; the subject's **truth** and the real material (UI, copy, numbers; or characters, setting, the joke) | you can say what the film is for in one sentence, in the subject's own words |
| 2 | Concept | first the concept every other video on this subject would use, ruled out; then three one-line concepts, as different as you can (one in an unusual form: a letter, a countdown, a front page, a map); keep the most specific | another brand or story could not use it unchanged |
| 3 | Treatment | six lines: logline, what we show, signature shot, motif, arc, look (below) | none of the six is vague |
| 4 | Structure | the arc of this kind of film, timed to the length (section 3) | every part has a job |
| 5 | Board | one line per shot: frames, size, picture, camera move and its reason, device, transition, music or voice cue | sizes alternate; no shot holds past its kind's limit |
| 6 | Design system | palette and colour script, type scale, grid, light, motion language (section 5); for cartoons the character and set sheet | written as numbers, saved with `kit.theme` |
| 7 | Timing spine | the music (`kit.music {reveal, ending}`) or the voice (`voiceover`, `kit.dialogue {dry: true}`) made first; the board snapped to it | every cut on a beat, a downbeat or a word |
| 8 | Styleframes | the three key frames built as finished stills and rendered | each works as a poster (section 6) |
| 9 | Build | one `runScript` per scene (miran-video), a contact sheet after each | the sheet matches the board |
| 10 | Finish | the joins (section 2), light, depth, sound on every action, J-cuts | no flat or frozen frame |
| 11 | Review | the director's review (section 8) plus `critiqueLayout`, `critiqueMotion`, `inspectMotionActivity` | the two lowest scores fixed, no `misaligned` finding |

**The treatment**, six lines:

1. **Logline**: the film in one sentence, built on the truth ("four
   hundred photos, finished before the kettle boils"; "a cat who thinks the
   vacuum is a dragon").
2. **What we show**: the subject doing its thing on screen: the product's
   UI, output or number; the character's action. Not words about it.
3. **Signature shot**: the frame that becomes the thumbnail.
4. **Motif**: one element that recurs in three beats or more and pays off
   at the end (a cursor, a dot, a line, one colour, a prop, a catchphrase).
5. **Arc**: the tension the audience feels and the moment it resolves
   (the product's reveal on the music's drop, the punchline, the result).
6. **Look**: palette, type, light direction, texture, one line each.

**The plan**, for one product, to show the shape (yours takes its words,
look and devices from its own subject):

```
Sentence:  for photographers drowning in a shoot: "Lumen finishes every photo in one tap"
Ruled out: before / after sliders over a beach photo (every editing ad)
Treatment: logline "four hundred photos, finished before the kettle boils" | show the grid
           grading itself | signature: the screen swinging face-on as the grid warms on the
           drop | motif: the amber dot (the button, then the proof, then the logo mark) |
           arc: a grey pile -> a finished album | look (from Lumen's own app: a dark editor
           so photos read true, its amber icon #F2A93B), SF Pro, one light from the top left
Spine:     kit.music({mood: "tech", reveal: 270, ending: 840}), 30 fps, 900 frames
Board (frames | part | size | picture | camera and reason | device | join out):
  0-45     open     macro   the amber "Finish all" button, the cursor arriving    push in: anticipation     kit.frame      cut on a bar
  45-105   tension  close   grey thumbnails piling up                            track the pile            importHtml     cut
  105-165  tension  medium  "Four hundred photos later." as one block            none: the count moves     kit.lockup     cut
  165-240  tension  insert  the counter racing to 412, 20-frame inserts          hard cuts on beats        kit.counter    zoom: the reveal
  240-330  reveal   wide    the app tilted, swinging face-on on the drop (270)   glide, swing on the drop  kit.screen3d   (continues)
  330-420  reveal   close   the push into the photo grid as it warms             push: this matters        screen3d push  cut
  420-540  feature  close   the cursor picks "Golden hour"; the card lifts       push into the card        kit.cursor     push
  540-630  claim    -       "One tap. Every photo." a word a half beat            none: the words land      kit.words      cut
  630-840  proof    macro   the amber dot, then "3 seconds a photo" at 420 px    punch on the hit          kit.counter    cut
  840-900  end      -       the dot becomes the mark; wordmark, line, url        a sheen once              kit.endcard    -
```

The recipe in section 7 builds these as seven scenes; split yours into shots.

Research means real material: the product's or story's facts, its real
interface, words and numbers, brand colours, type and logo (`captureSite
{url}` reads a public site's palette, fonts, logo and headline; `brand {action:
"capture", url}` saves them). Write each screen as HTML and CSS (`importHtml`,
one `id` per part that moves): Chrome lays it out with real fonts and shadows,
which positioned SVG never matches. An app everyone knows is judged against the
real one: copy its layout, spacing, icons and type at real size (a phone screen
is 390 x 844 points). The user's screen recordings are the best material of all
(`createLayer {kind: "video"}`, `kit.zoomFollow`). Show the interface itself,
big, and push into it; a laptop or phone drawn from shapes looks cheap next to
real UI (`kit.screen3d {bezel}` gives a clean device edge). AI products show the
task, the steps and the result, never brains, robots, swirls or circuit
boards. Material you could not get is designed in the product's style and
listed as invented in the final report.

## 2. Shot grammar (every kind of film)

**Sizes.** Every board line names one, and two shots in a row never share
a size:

| size | product / UI | character | in Miran |
|---|---|---|---|
| Macro (extreme close-up) | one button, a digit, a cursor tip filling the frame | eyes, a hand, a prop | `kit.frame(part, {fill: 0.35})` or `kit.zoom` 3-6x |
| Close-up | one card or chart at 60-80% of the frame | the face fills ~60% of the height | `kit.frame(part, {fill: 0.7})`; `shot: "close"` |
| Medium | the panel in context | waist up, or two characters (two-shot) | the screen at 80-100% of the frame; `shot: "two"` |
| Wide | the whole product or its world, under 1.5 s (nobody reads a wide UI) | whole figures in the set (establishing) | `kit.screen3d`, then push in; `shot: "wide"` |
| Insert | a cutaway: a notification, a timestamp, a green tick | a clock, a sign, the thing they look at | 10-20 frames, hard cut in and out |

Wide to tight says "this matters"; tight to wide is a reveal (context,
scale). A feature or an action reads wide (where), close (what), macro
(the moment), medium (the result).

**Composition.** One focal point per frame, on a third, not dead centre,
unless the shot is about symmetry (a logo, a keynote title). Leave lead room in
the direction something moves or looks. Build depth in three planes and move
them at different speeds (parallax). Big against small: a 300 px word beside a
40 px label, a macro against a wide.

**Camera: every move has a reason**, starts on the action that motivates
it (a click, a cut, a beat, a line of dialogue) and eases into its landing:

| reason | move | in Miran |
|---|---|---|
| This matters | push in | `kit.frame` to the part, `kit.zoom`; 3D: `cameraDolly` |
| How big, where we are | pull back | `kit.frame(..., {back: true})`, a zoom out from a detail |
| Follow the action | track the cursor, the data, the character | animate the scene group with the subject; `cameraTrackTarget` |
| Premium hero | a tilted screen or object with a slow glide or orbit | `kit.screen3d`; `cameraOrbit` 20-40 degrees |
| Shift attention | rack focus | `cameraRackFocus`; 2D: blur the old subject, sharpen the new |
| Energy change | whip, punch-in on a hit | `kit.transition {style: "whip"}`, `kit.punch` |
| Keep a hold alive | change something in it (a state, a number, the cursor, a light); with nothing to change, a 3-5% drift that moves the picture, not the words being read | a counter, `kit.cursor`, `kit.sheen`; `kit.drift` |

One move at a time, each landing and resting before the next (the
screen3d hero is three in turn: tilt, swing face-on, push). Constant-speed
drifts are for holds only, never the hero move.

**Screen direction.** Keep motion flowing the same way across cuts
(progress moves left to right). In a dialogue, A on the left looks right
and B on the right looks left in every shot (the 180-degree line);
`kit.dialogue` keeps it.

**Editing grammar.** Cut on action: hide the cut inside a movement (a hand
rising, a head turn, a click) and the eye never sees it. Cut where the eye is
going: the next shot's subject sits where the last shot's gaze ended, so the
viewer does not hunt (`critiqueEdit` checks the eye trace). Let sound cross
the cut: a J cut brings the next shot's sound in first, an L cut lets a line
finish over the reaction (`edit jl`, `kit.transition {lead}`). Pace is varied
length: a cut is a beat, a hold a breath; cut faster as energy rises, let the
payoff breathe, and never cut every shot to one length (a metronome).
Footage work: **miran-edit**.

**Joining shots.** An editor's default is the hard cut, on a beat: most
joins in a good film are cuts. A transition is an event, so it goes where
the idea changes, and what the two shots share picks it:

| the two shots | join | in Miran |
|---|---|---|
| the next moment of the same thing | a cut on the beat | no transition: the scenes meet |
| a shape carries over (the dot, a circle, a card) | match cut: it becomes the next shot | `kit.match(from, to)`, or `zoom` into that shape |
| a detail or a world inside this shot | zoom through the part that leads there | `kit.transition {style: "zoom", into}`, `flythrough` |
| the next step, onward | push the way progress goes, the same way every time | `push {dir}` |
| an energy jump (into the drop, a new section) | whip or flash on the downbeat | `whip`, `flash` |
| a new chapter, its colour taking over | the colour wipes or floods in from an element | `wipe {color}`, `iris {from_point}` |
| a calm change, time passing | blur through | `blur` |

Carry motion through the cut: the outgoing move's direction and speed
continue in the next shot, or the cut lands on a stop, on purpose. Give the
film one transition language (one or two of these, travelling one way) and one
special join kept for the reveal. A background colour changes with the
chapter, not every shot, and the join brings it in.

## 3. Structure by kind of film

Time the arc to the length. Shares are of the running time.

**Product launch or teaser.**

| part | 30 s | job and how |
|---|---|---|
| Cold open | 0-3 s | Start inside the product's world, moving on frame 0: a macro detail, a cursor already travelling, a number racing. No logo |
| Tension | 3-8 s | Make the problem felt: tight shots, cuts every 12-20 frames, cooler or muted colour, a riser building |
| Reveal | ~8-10 s | The product arrives on the music's drop after a breath of silence: the biggest change of scale in the film (`kit.screen3d` swinging face-on, a pull-back, the name slamming in) |
| Demonstration | 10-24 s | Two or three features, each a 4-5 s sequence with its own device, sizes and camera; cause and effect on real UI; one huge number as proof |
| Payoff | 24-27 s | The signature shot; the motif pays off; the biggest move, a speed ramp (`kit.slowmo`), a light sweep (`kit.sheen`) |
| End card | last 2-3 s | `kit.endcard`: mark, wordmark, line, url, still alive; the music resolves |

A 15 s cut keeps the cold open, the reveal, one feature and the end card.
A teaser that does not show the product yet builds the motif toward the
name instead of a demonstration.

**Explainer or product demo.** Hook (the problem in one image, 0-3 s); how it
works in three steps, one idea each, 3-5 s per step, cause and effect on
screen; the result (the before and after, or the number); the call to action.
The voice leads: record it first, land every picture change 2-4 frames before
its word (`kit.when("word")`). The cursor is an actor: it travels on an arc,
slows before its target, pauses, clicks (`kit.cursor`). Typing is edited, never
real time.

**Dialogue cartoon or character piece.** Establish (a wide of who and
where, 2-3 s); the want or the problem; escalation in three beats, each
bigger than the last (the rule of three); the turn or punchline, played
close after a beat of silence (`pause: 1`); the button (one small gag
after the punchline, with its sound); the end card. Acting sells it:
- **Poses read in silhouette**; every big action has an anticipation
  (wind up the other way first) and a follow-through.
- **Reactions are half the joke**: cut to the listener (`react: "shake" |
  "nod" | "shrug"`); a sceptic does not nod along.
- **Comic timing**: the beat of silence before the punchline, the hold on
  the face after it (12-20 frames), the gag's sound exactly on its frame
  (`kit.sfx("boing", f)`, 14 cartoon sounds).
- **Gestures land on the stressed word** (`kit.when`), characters fill
  55-75% of the frame height in dialogue shots, `kit.life` under everything
  so nobody freezes.

**Social short (9:16).** The hook in the first second (motion and a claim,
never a logo), a payoff every 3-5 s, captions on every spoken word (three words
a phrase, below the centre), the full height composed (`kit.safe({platform:
"reels"})`), an ending that loops into the start.

**Brand film or title sequence.** One motif transforming through the piece (a
line becoming the logo), the music leading every cut, type used as image (huge,
cropped, masked), the name revealed slowly and last.

**Data story.** One number per shot, huge, landing on a beat; the chart
building is the drama (bars racing, a line drawing on); before then after; the
source in small type on the frame that shows it.

**Pacing.** The one table every other skill points to:

| kind | rhythm | what keeps a hold alive |
|---|---|---|
| Launch, promo, social | something new every 1-2 s; no composition held past 2 s without a cut, a push or a new element | slams, pushes, a colour per chapter |
| Explainer, product demo | one step per 2-4 s; the UI does the work | the cursor, typing, a state changing, a push into the part |
| Keynote reveal | one idea per beat, 2-3 s holds | a slow push, a light sweep, one motif carried through |
| Dialogue cartoon | the joke's timing: silence before the punchline | acting, reactions, cutting between shots |

A drift alone does not keep a hold alive.

## 4. Timing spine: music or voice first

Fix the frames that matter (the reveal, the punchline, the end card),
then make the spine before cutting anything.

**Music.** `const m = kit.music({mood, reveal, ending})` nudges the tempo
so the drop lands exactly on `reveal`, and returns `sections` (intro,
build, stop, drop, ending as `[start, end]` frames), `bar_frames`,
`beat_frames` and an `edit` line. Map the board onto the sections:

| section | picture | shot length |
|---|---|---|
| `intro` | cold open, then the problem | 2-4 beats, then 1-2 |
| `build` | the fastest cuts, tension peaking | 1 beat each, hard cuts |
| `stop` | a breath: the last shot holds, no cut inside it | silence |
| `drop` | the reveal on its first frame, then the demonstration | hold 4-6 beats, then 2-4 |
| `ending` | the end card, the last chord ringing out | 4 beats or more |

Section changes cut on downbeats, cuts inside a section on beats:
`kit.snap(frame, m, {to: "bar"})` moves a frame onto the grid, a transition's
`at` too. Sound leads picture: `kit.transition({at, lead: 6})` starts the next
scene's sounds 6 frames early (a J-cut).

**Voice.** Explainers and cartoons are timed by the words: `voiceover
{script}` or `kit.dialogue(lines, {dry: true})` first, then every beat on
`kit.when("word")`. Size the composition to the dialogue's `end` plus an ending
beat of 2-4 s. The words set the length: a brief's "2 minutes" aims the script,
and a longer voice makes a longer film. Shorten by cutting words and voicing
again, never by speeding the narration up or cutting a line before its last word.

Once the blocks are in, export a `draft` and watch the pacing before polishing.

## 5. Design system: decide in numbers, once

- **Palette and colour script**: background, surface, text, muted, one
  accent, found in the brand (its logo file, site, app icon, CSS) and
  written with its source; with no brand, taken from the subject's real
  world and named as the thing it comes from. A palette you picked because
  it looks tasteful is the house look of whoever picked it, not the
  brand's. The palette moves with the arc: muted or cool in the tension,
  the brand colour arrives with the reveal; a cartoon's set colours sit
  quieter than its characters.
- **Type scale at 1080p** (double for 4K): display 160-400 px tracked
  -0.02 to -0.04 em, line height 0.95-1.05; headlines 96-140; kickers
  0.19x the headline in tracked capitals (28 px at least); subs 0.34x the
  headline; UI, body text, labels and captions 28 px or more on screen. One
  family in two weights, or a contrast pair from `guide {topic:"fonts"}`
  (a serif headline with a sans, a heavy sans with a mono for data); at most
  about seven words on screen outside the UI. These numbers are the ones
  every other skill and guide uses.
- **Grid and alignment**: 8-9% safe margins. One alignment axis per shot: a
  left block's lines share one ink edge, a centred block one centre, a label
  sits on its headline's edge, icons and marks align to the cap height. Build
  every block of type with `kit.lockup` (end cards `kit.endcard`): it measures
  the ink; titles placed one by one miss by 15-200 px (`critiqueLayout` reports
  `misaligned`). Art stays out of the text column.
- **Light**: one direction for the whole film (gradients, glows and
  shadows agree), a soft light behind the hero (`kit.spot`), a sweep across
  it at the payoff (`kit.sheen`), depth behind the subject (a faint grid,
  texture or a blurred plane).
- **Motion language**: two eases (entrances `emphasized-decelerate` or
  `spring:snappy`; exits `emphasized-accelerate`, about 30% shorter), three
  durations (8-12 frames small, 18-24 normal, 30-36 the hero), entrances
  staggered 2-4 frames, moves on arcs (`kit.move {arc}`), overlap and
  follow-through (`kit.follow`, `kit.inertia`).
- **Cartoons**: one character size, one line weight, a set drawn 1.2x the
  frame so close-ups never show an edge.

## 6. Styleframes before animation

Build the opening, the signature shot and the end card as finished stills
before animating anything. Render them (`renderFrame`) and judge each as a poster: one hero, the committed
palette, the real product or character on screen, type sized and aligned
(no `misaligned`), light and depth behind the subject, a frame no other
brand could use. Then say which of your usual defaults showed up (centred
text fading in, one ease, a glow and a gradient, the default sans,
identical cards) and change each one that is not deliberate for this
subject. Fix them until they hold up; they set the bar for every other shot. Then animate.

## 7. Recipes: the shots films are made of

Each runs inside `runScript` with the kit (`miran.help("kit.name")` lists
every option). The first block is a whole fresh project, runnable as
printed; its scenes run long to keep it short, so split yours into shots
(section 3). `screen` is the real product's UI as SVG markup or an .svg
path, one `<g id>` per panel.

```js
// A fresh project: a 30 s film at 30 fps, the reveal at 9 s, the end card at 28 s.
miran.createComposition({name: "Lumen launch", width: 1920, height: 1080, fps: 30, duration_frames: 900});
// The palette is Lumen's own (its dark editor and amber icon), written in plan.md with its source.
kit.theme({font: "SF Pro Display", bg: "#111014", surface: "#1C1B21", text: "#F5F2EA", muted: "#9A968C", accent: "#F2A93B"});
kit.backdrop({style: "solid"}); // the editor's own flat surface, no glow
const screen = `<svg xmlns="http://www.w3.org/2000/svg" width="1440" height="900" viewBox="0 0 1440 900">
  <rect width="1440" height="900" rx="24" fill="#18171C"/>
  <g id="sidebar"><rect width="300" height="900" rx="24" fill="#222127"/></g>
  <g id="photos"><rect x="340" y="60" width="640" height="560" rx="18" fill="#1F1E24" stroke="#2C2B31"/>
    <text x="380" y="124" font-family="SF Pro Display" font-size="36" font-weight="600" fill="#F5F2EA">Sunday, the lake</text></g>
  <g id="look"><rect x="1020" y="60" width="380" height="300" rx="18" fill="#2A2930"/>
    <text x="1050" y="124" font-family="SF Pro Display" font-size="32" font-weight="600" fill="#F2A93B">Golden hour</text></g>
  <g id="go"><rect x="1020" y="760" width="380" height="90" rx="45" fill="#F2A93B"/>
    <text x="1210" y="817" text-anchor="middle" font-family="SF Pro Display" font-size="32" font-weight="600" fill="#111014">Finish all</text></g>
</svg>`;
const logo = `<svg xmlns="http://www.w3.org/2000/svg" width="120" height="120" viewBox="0 0 120 120">
  <circle cx="60" cy="60" r="56" fill="#F2A93B"/><circle cx="60" cy="60" r="22" fill="#111014"/></svg>`;

// The spine first: the drop lands exactly on the reveal.
const m = kit.music({mood: "tech", reveal: 270, ending: 840});
log(m.edit);                        // where the drop and the bars are

// Cold open (0-60): a macro push into the button that starts everything.
kit.scene({start: 0, end: 60}, ({start, end}) => {
  const ui = kit.svg(screen, {width: 1600, style: "none"});
  kit.frame(ui.parts.go, {fill: 0.3, start, dur: end - start, hold: 0});
});

// Tension (60-240): the problem as a lockup, every line on one ink edge; the counter racing
// up is what keeps the hold alive (no drift under words being read).
kit.scene({start: 60, end: 240}, ({start, end, group}) => {
  kit.lockup({align: "left", at: [-760, 0], kicker: "Sunday, 9:12 pm", headline: "Four hundred\nphotos later.",
    sub: "Not one of them edited.", start: start + 8});
  kit.counter({to: 412, size: 300, at: [470, 40], color: "#E5484D", label: "photos to edit", start: start + 6, dur: 140});
});

// The product hero on the drop: tilted in 3D, swinging face-on on the reveal,
// then pushing into the panel that matters; a soft light behind it.
kit.scene({start: 240, end: 420}, ({start, end, group}) => {
  const s = kit.screen3d(screen, {start, face: {at: 270}, push: {to: "photos", at: 330}});
  kit.spot({behind: s.surface});
  kit.drift(group, {start: s.end, end});            // the hold after the push stays alive
});

// A feature: the cursor picks the look, the card lifts off the screen.
kit.scene({start: 420, end: 540}, ({start, end, group}) => {
  const ui = kit.svg(screen, {width: 1700, style: "none"});
  const card = miran.inspectLayer({layer: ui.parts.look, frame: start}).bounds.center;   // aim at it, never guess
  const c = kit.cursor({path: [[-700, -360], card], click: [1], start: start + 4});
  kit.lift(ui.parts.look, {start: c.arrive[1] + 3, until: end - 14});
});

// A statement on the beat: a word every half beat, the key word set apart.
kit.scene({start: 540, end: 630}, ({start, end, group}) => {
  const b = m.beat_frames.filter((f) => f >= start);
  kit.words("One tap. Every photo.", {size: 150, style: "slam", start: b[0], stagger: Math.round((b[1] - b[0]) / 2),
    highlight: {word: "Every"}});
});

// Proof: one number, huge, landing on a downbeat.
kit.scene({start: 630, end: 840}, ({start, end, group}) => {
  const land = kit.snap(start + 50, m, {to: "bar"});
  kit.counter({to: 3, size: 420, start: land - 44, dur: 44, label: "seconds a photo"});
});

// The end card: the motif's amber dot is the mark; wordmark, line and url centred on the
// whole lockup, light sweeping once.
kit.scene({start: 840, end: 900}, ({start, end, group}) => {
  const e = kit.endcard({mark: logo, wordmark: "Lumen", tagline: "Your photos, finished.", url: "lumen.app", size: 150, start});
  kit.sheen(e.wordmark.id, {start: start + 26});
});

// Joins (section 2): most scenes meet on a hard cut, on the beat. One special
// move, saved for the reveal: zoom into the product. One push carries the
// feature onward into the statement. Sound leads picture.
kit.transition({at: 240, style: "zoom", lead: 6});
kit.transition({at: 540, style: "push", dir: "left", lead: 6});
return miran.renderContactSheet({frames: [40, 150, 262, 300, 370, 480, 600, 700, 885], columns: 3, out: "qa/board.png", scale: 0.5});
```

A dialogue beat is staged and timed with `kit.dialogue` (`guide
{topic:"characters"}` has a runnable two-hander).

## 8. The director's review

Before the export, score the film's sheet on each line 1-5, fix the two
lowest and look once more (one pass, not a loop):

1. **Story**: could someone tell the arc from six frames? Is the reveal
   (or the punchline) where the music or the silence says?
2. **Shot variety**: sizes alternate, no layout repeated back to back, no
   shot static past its kind's limit.
3. **Camera**: every move motivated, starting on its action, eased into
   its landing.
4. **Subject truth**: the real UI, number or character readable when it
   matters (28 px text, faces at 55% of the height in dialogue).
5. **Hierarchy and composition**: one focal point per frame, on a third,
   depth behind it.
6. **Light, colour and finish**: the brand's own palette (cover the logo:
   could this frame open any AI startup's film?), one light direction, the
   hero separated from the background, no flat empty frame.
7. **Type**: sized, tracked, few words, every block on one axis (no
   `misaligned`).
8. **Rhythm and sound**: cuts on beats or words, a breath before the hit,
   a sound on every action.
9. **Acting** (characters): anticipation, reactions, holds on faces,
   gestures on stressed words.
10. **Motif, payoff and ending**: the motif pays off in the signature
    shot; the end card is alive and the music resolves.

Then look at the sheet with the notes in miran-video ("Review") and run the
lazy-defaults check ("Craft as tests"). `guide {topic:"directing"}` and
`guide {topic:"characters"}` go deeper.
