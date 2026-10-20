---
name: miran-polish
description: The last pass on a Miran Fann film, in the order studios finish it - content, motion, type, colour, sound, export - each judged with the measuring tools (inspectMotionActivity, critiqueMotion read by its findings, critiqueLayout, grade scopes, critiqueEdit, checkExport), one fix at a time, the finishing pass (vignette, bloom, grain), loudness, captions, export and deliver per platform, and the last-mile failures that ship broken films. Use before calling any Miran film finished, and whenever an export differs from the preview.
---

# Finish the film

> **Lean tool list:** a call written below as `name {params}` is `miran.name({params})` inside `runScript` (or `query` when it only looks) unless `name` is in your tool list; exports, deliveries, `critiqueEdit` and `inspectMotionActivity` stay direct calls.

A film is not finished when the last beat is built. Studios give it a last
pass in a fixed order, because each decision rests on the one before. A grade
chosen before the cut is locked gets chosen twice, and a mix cannot rescue a
shot the film did not need. **miran-video** builds the film, and
**miran-critique** has every measuring tool in depth. This skill is the order
of the last pass and what to look for at each step.

## The order

1. **Content.** Watch the draft once, end to end, as a stranger would:
   `renderVideo {quality: "draft"}`.
   - Does every shot say something the film needs? A beat you cannot name
     in a few words is a beat to cut. A cut that tightens the film beats
     any amount of polish on a shot that should not be there.
   - Check the film against the research sheet in `plan.md`. Every product
     screen should be the real one rebuilt, with real words. Every number
     should be the subject's own. Anything invented must say so in your
     report.
2. **Motion.** Is it alive, and are the moves well made? (below)
3. **Type.** Can every word be read, for long enough, inside the safe area?
4. **Colour.** One look, measured, consistent from the first shot to the
   last.
5. **Sound.** Every action that should sound does. The music lands its hits
   and resolves on the end card, at the platform's loudness.
6. **Export.** The right file for where it goes, checked from the file
   itself.

Go back up the list only on purpose: a content cut after the grade means
grading that shot again.

## Motion: alive first, then well made

Run `inspectMotionActivity` on the whole film first. On a film longer than a
few seconds it returns a `{job}`: poll `jobStatus`, and allow a minute or two
for a 20 s film. It reports the share of the frame that changes each second,
and every still hold.

- Real launch films come in two kinds:
  - the energetic ones (Lovable, Claude Design) sit at about 5-9% median with
    almost nothing frozen;
  - the calm UI films (Cowork, Cursor 2.0) sit under 2% and hold for two or
    three seconds, which works only on a full frame of legible product with
    a cursor or a voice about to act.
- A film carried by music and no voice belongs at the energetic end. In such
  a film, a still hold over about a second is a finding, except the breath
  before the climax.
- The end card's words hold to be read, but its frame keeps a slow push or a
  drift. A frozen ending is a finding too.
- Fix a still stretch with a performance: the camera travelling to the part
  that matters, a staged reveal, the product changing state. Never fix it
  with a fade.

Then read `critiqueMotion` by its findings, not its score:
- The score grades only the moves that exist, so a frozen film can score
  100.
- It drops to 0 while any warning stands, so a living film can score below
  a frozen one.
- Fix the findings on your own moves: words that travel while they are
  read, jolts out of rest, lockstep starts.

Mark only atmosphere as `intended` (dust, sparkles, a light that breathes)
with `setLayerField {layer, field: "intended", value: true}`. Never mark a
scene group, a camera move or your own moves to clear the critic: that hides
real faults, and the 100 it buys means nothing. A warning on a kit piece's own
internal move is a line for your `feedback` report, not a reason to remove
the piece. Never delete motion to raise the score.

## Type

`critiqueLayout` on the whole film. Collisions, text too small, and text
past the safe area are errors; fix every one.
- Its `sparse` and `empty-area` notes are judgements: a calm end card leaves
  space on purpose, but an accidental empty frame is a bug.
- `misaligned` on things that tumble on purpose can be left, with a reason
  in your report.
- Read time: a line needs about its word count divided by 3.5 seconds once
  it has landed, plus a beat to land.
- `read_in_motion` from `critiqueMotion` means words moved while being
  read: hold the words, keep the shot around them going.
- On a vertical cut, `kit.safe({platform})` keeps captions and type clear of
  the app's own interface.

## Colour

Measure before you grade. `grade {action: "scopes", frame}` gives the luma
spread (`p1`, `p50`, `p99`), the clipped share, saturation and any colour
cast, with a waveform, vectorscope and histogram image.

- **Consistency matters more than the number.** A dark style (neon, night
  cinema) sits low, a bright one (editorial, candy) high. A film that swings
  between the two between shots reads as two films. Read the scopes on two or
  three frames from different beats, not one.
- **Clipped highlights on designed frames** are a glow or a white pushed too
  hot. Pull that layer back; a grade on top only greys it.
- **One look, applied once**, chosen from the film's style:
  `grade {action: "look", name, strength}` with `film`, `warm`, `cool`,
  `teal-orange`, `bleach`, `mono` or `fade`. Designed brand colour often
  needs none. A second look is a mistake, not a refinement.
- **Footage and generated plates** get `grade {action: "match", layer, to}`
  toward a designed reference, or `grade {action: "auto", layers}` to bring
  them to one exposure. Designed graphics are already exact; leave them be.
- **Contrast you can read:** display type at 3:1 against its ground or
  better, small text at 4.5:1.

## The finishing pass

`kit.finish({vignette, bloom, grain, grainHold})` goes last, and once.
- A vignette of 0.15-0.3 focuses the frame without showing a ring.
- Bloom only when there are highlights worth blooming: a screen, a light, a
  glowing mark.
- Grain only when the style asks for texture (print, film), never as a
  default. `grainHold: 0` is a static paper texture and costs little. Grain
  new on every frame is the heaviest thing an MP4 can hold: a 4-minute film
  with it came out at 531 MB.
- One sweep of light (`kit.sheen`) across the hero at the payoff does more
  than a glow on everything.

## Sound

- **Every action that should sound, sounds.** Kit pieces score themselves.
  For motion built without the kit, `kit.sfx.auto()` in a script places a
  whoosh at a fast move's fastest frame, pops, slams and ticks under
  counters, and skips anything already scored. Then delete what crowds: the
  hero hit on the lockup is the loudest moment.
- **The music lands and resolves.** The reveal sits on the drop, and the end
  card sits on the ending, not on a cut-off loop (`kit.music {reveal,
  ending}`).
- **Measure it.** `critiqueEdit {checks: ["audio", "flashes"]}` reports
  loudness, true peak and flash frames. `inspectAudio {asset}` probes one
  file. Web and social sit near -14 LUFS, a voice-led film or podcast near
  -16, and `deliver` normalises per platform.

### Recordings when synthesis is not enough

`addSound` and `kit.sfx` are generated in the engine and land on the frame.
When the edit wants a real recording — a room, a cloth, a click with weight —
search the shipped CC0 sound directory before reaching for a synthesised
preset:

```
sounds {query: "whoosh", max_seconds: 1}
sounds {category: "riser", min_seconds: 2}
sounds {id: "anve/whoosh/snd-whoosh-swish-s", place: true}   // a direct call
```

`place: true` caches the file, copies it to `sounds/` and imports it as an
audio asset (call it directly: a script reads the cache but does not
download); place it like any audio layer (`createLayer {kind:"audio"}`) and
time it with `setAudioTiming`. 352 files, CC0-1.0 (ANVE motion-pack and
Kenney UI SFX), never shipped with the app. Use a recording for texture and a
generated sound for the exact hit; do not stack two sounds that say the same
thing.

## Export

| where it goes | call |
|---|---|
| a draft to judge timing | `renderVideo {out, quality: "draft"}` (half size, fast) |
| web and social | `renderVideo {out, quality: "web"}` (small, tuned for flat animation) |
| YouTube, a master that plays everywhere | `renderVideo {out, quality: "high"}` (H.264) |
| dark gradients, glows, smooth light | `renderVideo {out, quality: "max"}` (HEVC 10-bit, no banding) |
| finishing, archive, alpha | `renderVideo {out: "....mov", quality: "master"}` (ProRes; 4444 on a transparent composition) |
| several platforms at once | `deliver {to: [...], captions, thumbnail}` |

`deliver` renders each platform's shape, normalises loudness, and writes
captions (`burn`, `srt`, `vtt`, `both` or `none`), chapters and a thumbnail
in one job. Use it for anything that ships to more than one place. Every
export ends with `checkExport {path}`: frames decoded from the file itself,
duration, size, bitrate, fps, audio and loudness. Read every `problem` it
names before you call the film done.

## Recipes

These run on an open project whose composition is `Main`. Exports and
deliveries are direct calls, so they are shown as calls, not scripts.

### The colour pass: measure, then one decision

```js
// The colour pass: read the scopes on frames from different beats, then make one decision from the numbers.
const frames = [20, 75, 130];
const reads = frames.map((f) => miran.grade({action: "scopes", composition: "Main", frame: f}));
const mids = reads.map((r) => r.luma.p50);
const notes = [`median luma by beat: ${mids.join(", ")}`];
// A film whose beats swing far apart in brightness reads as two films: bring the odd beat back first.
if (Math.max(...mids) - Math.min(...mids) > 0.3) notes.push("brightness swings between beats: fix the odd beat before any look");
// Clipped highlights on a designed frame are a glow or a white too hot: lower that layer, do not grade over it.
reads.forEach((r, i) => { if (r.clipped_pct.high > 1) notes.push(`frame ${frames[i]}: highlights clip`); });
// One look for the whole film, chosen from its style and kept light. Brand colour often needs none at all.
miran.grade({action: "look", composition: "Main", name: "warm", strength: 0.4});
return notes.join("; ");
```

### The last hold stays alive, then the finishing pass

```js
// The end card's words hold to be read; a slow push keeps the frame alive; the finishing pass goes on last, once.
kit.push("Card", {from: 1, to: 1.03, start: 90, end: 150});
// A soft vignette and a static paper grain: texture the style asks for, cheap in the file.
kit.finish({vignette: 0.22, grain: 0.02, grainHold: 0});
return "finished";
```

### The sound pass: score what moves, then measure

```js
// Score the motion that has no sound yet; kit pieces already score themselves, so this adds only what is missing.
const r = kit.sfx.auto();
return `placed ${r.placed.length}, skipped ${r.skipped.length}`;
```

Then, as direct calls:

```text
critiqueEdit {checks: ["audio", "flashes"]}
renderVideo {out: "renders/final.mp4", quality: "high"}
checkExport {path: "renders/final.mp4"}
deliver {to: ["youtube", "shorts", "x"], captions: "srt", thumbnail: true}
```

A render or delivery that runs past about 40 s returns a `{job}`: poll
`jobStatus` until it is done.

## Failures you will hit

- **`renderVideo` never overwrites.** A second render to the same `out` lands
  beside it as `final-2.mp4`. Read the path from the result and check that
  file, or you will be checking the old one.
- **`quality: "master"` needs a `.mov` path.** `.mp4` is refused.
- **A transparent composition exported to H.264 loses its alpha.** Use
  `quality: "master"` (ProRes 4444).
- **`checkExport` says `audio: false` on a film with sound layers.** The mux
  failed: render again and read the result's warnings.
- **A draft is half size.** It never matches a full-size export pixel for
  pixel; compare frames with `compareImages` at the same size.
- **The export differs from the preview.** Render the frame both ways and
  diff them with `compareImages`. `inspectRenderGraph` shows a layer that fell
  back to the CPU, and `listRenderCapabilities` shows what the backend
  supports.
- **`deliver` renders files and does not change the project.** Running it
  again overwrites the same outputs.

## Tool reference (generated)

`TOOLS.md` in this folder lists the tools of the last pass with their exact
parameters, generated from the runtime registry.
