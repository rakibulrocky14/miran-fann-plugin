---
name: miran-edit
description: Edits real footage with Miran Fann the way a post house does - logging and searching clips, selects and a paper edit, a rough cut by words and anchors, B-roll cheapest first, cutouts, tracking and screen replacement, speed ramps, clean voice and music fit, colour match across footage and generated shots, captions, the critiqueEdit review loop and deliver for every platform. Use when the user brings video (phone clips, talking heads, podcasts, a screen recording, dance clips, photos) or a song to cut to, or wants a vertical short, a montage, VFX or colour on footage, or files ready to publish.
---

# Editing footage with Miran Fann

> **Lean tool list:** a call written below as `name {params}` is `miran.name({params})` inside `runScript` (or `query` when it only looks) unless `name` is in your tool list; exports, jobs, voiceover, downloads and panels stay direct calls.

A post house never starts at the timeline. It logs the footage, picks the
selects, writes the cut on paper, and only then touches pictures, sound and
colour, each in its turn. Miran has the same departments in one box, and every
read of the footage runs on this Mac, and footage leaves it only inside a
`generate`, `generateVideo` or `generateImage` call it is passed to (it goes to
that one provider): before such a call, tell the user which files go to which
provider. **miran-video** is the film process and
**miran-direct** the grammar; this skill is the footage work inside them.
Footage, motion graphics and generated media are all just material: any of
them can go in any role (`place`), so combine freely.

Some calls decode whole clips and run as jobs, so they are **direct calls,
refused inside `runScript`**: `footage` index / watch / describe, `cutout`,
`track`, `enhance`, `critiqueEdit`, `deliver`, `reference`, `generateVideo`,
`generate`, `generateImage`, `captureSite`, `record`. A reply of `running` means: wait
for `jobStatus`, then call again with the same params (the result is cached and
lands at once). `edit`, `grade`, `place` and the kit (`kit.textBehind`,
`kit.footageCaptions`, ...) run in scripts.

Load the tools in one ToolSearch (`mcp__plugin_miran_miran__footage`, `edit`,
`cutout`, `track`, `enhance`, `grade`, `place`, `critiqueEdit`, `deliver`,
`variants`, `generate`, `generateVideo`, `generateImage`, `captureSite`, `record`,
`openSettings`, `runScript`, `jobStatus`, `renderContactSheet`). Keep a `plan.md` next to the
project: the paper edit, the selects and what each call returned.

## The process, in a post house's order

1. **Log.** `footage index` once per clip or folder (cached until the file
   changes). Then read, never guess from file names: `map` level 0 for the
   shoot and level 1 for its shots, `search` for moments (`query: "pricing"`,
   `sound: "laughter"`, `words: "we shipped it"`), `transcript`, `best
   {what: "takes"}` for the cleanest take of a repeated line, and `describe`
   to label a big library cheaply. `watch {asset, start, end}` is your monitor:
   frames over the waveform, silences and words on one time axis. Look at a
   stretch before you cut it.
2. **Selects and the paper edit.** Choose by story, not by order of shooting:
   the sentences that carry it, the best take of each, the reaction shot. Write
   the order as text (`edit {action: "paper", mode: "export"}`, change it,
   `mode: "apply"`), one line per clip with its words beside it. Show the user
   the paper edit of a long piece first: changing text is cheaper than changing
   picture.
3. **Rough cut: radio first.** `assemble` the sound bites in story order (or
   `cutWords` with the transcript as it should read), then `removeSilence`,
   `removeFillers` and `tighten {seconds}`. Judge it by ear as a story before
   anyone sees a picture. Open on the strongest line, not the greeting, and end
   on the line that lands. Cuts hide in pauses and land between words.
4. **Cover and shape.** Cover jump cuts with B-roll (`broll`) where the talk
   turns abstract, never over the face on a punchline, or a punch-in
   (`kit.punchIn`, or `removeSilence {punch: true}`). The hard cut is the
   default and mostly right; a dissolve says time passed, a dip says a chapter
   ended, a whip carries energy. `jl` makes J and L cuts so sound and picture
   do not change at the same instant.
5. **Picture lock.** Say it to yourself. After this you touch sound, colour
   and graphics only, because a changed cut means fitting the music and
   captions again.
6. **Sound.** Clean the voice first (`enhance {action: "voice", strength:
   0.75}` keeps a little room; words keep their timing; the first use downloads
   a small pinned model, about 8 MB, so say so). Fit the music to the
   locked picture (`enhance {action: "music", seconds}` jumps between two alike
   bars so the song keeps its ending, or `edit beatCut` for a montage). Duck
   the music under speech (`duck_db`), never the speech. Spot the cut
   (`enhance {action: "foley", density: 0.4}`), listen, delete what does not
   earn its place. J and L cuts, a room-tone bed under every join and one hero
   sound per moment are what make a cut sound edited rather than assembled.
7. **Colour, in a colourist's order.** `grade scopes` to look first, `auto` to
   bring the shots to one exposure and white balance, `match` a shot to a hero
   frame, one gentle `look` on the composition, `grain` last, copied from the
   footage onto everything that is not footage. Generated shots and graphics get
   the same treatment: one look over everything is what makes a film instead of
   a collage.
8. **Titles and captions.** `kit.footageCaptions(clip, {style})` takes the
   words from the clip's index and keeps them off faces and the app's buttons;
   motion graphics from the kit go on top of the lock, not under it. Caption
   the whole edit in one call, `kit.footageCaptions("all", {style})` (or a
   list of clips): one style for the film, no overlap at the cuts. It measures
   what is behind each phrase for every colour the words show and says what it
   gave them (`treatment`, `highlight.fix`, `reads`); believe `reads: false`.
9. **QC.** `critiqueEdit`, then the review loop below.
10. **Deliver.** `deliver {to: [...]}` once per release (see **miran-render**).
    One cut for several audiences? `variants {hooks, aspects, languages}` makes
    a composition per combination (an opening title, a reframed shape, captions
    translated on the device); look at each, then deliver them.

## Anchors, not seconds

Name moments the way an editor does: `{sentence: 3}`, `{word: 40, edge:
"end"}`, `{shot: 2}`, `{event: "laughter"}`, `{beat: 8}`, `{downbeat: 2}`,
`{marker: "drop"}`. They survive a re-index and read like notes. `edit` takes
them for any source time, kit options take them for any start
(`kit.textBehind("Clip", "LAUNCH", {start: {sentence: 1}})`), and `kit.at(anchor,
layer)` or `footage anchor` turns one into a frame. Reach for seconds only for
a time that has no anchor.

## Cheapest first, and talk money before you spend it

B-roll for a line, in order: the user's own footage (`broll {source:
"footage"}` matches it by words and labels), motion graphics built in Miran
(free), a still with a slow push or parallax (`broll {source: "stills"}`, or
`generateImage` for about $0.04), a draft video (`generateVideo {draft:
true}`), and a premium model only for the one hero shot. `edit broll` with
`source: "auto"` and no `max_cost` never spends anything.

Before **any** paid call (`generateImage`, `generateVideo`, `generate`, `broll
{source: "generate"}`): price it (`generateVideo {action: "estimate"}`, or
`plan {shots, budget_usd}` for a whole film), tell the user the figure and the
cheaper path, and wait for a yes. A budget named in the brief ("under $2")
is the yes up to that figure. Fal and Replicate show no price first: pass
`max_cost` and say it. Report what was spent at the end.

When a generate call fails, say what happened and stop there. `no-key`: the user
adds the key (OpenRouter, fal or Replicate) in Miran Settings, so call
`openSettings` and give them the link; never ask for a key in chat.
`key-refused` or `no-credits`: say so, and do not retry. `local-only`: the user
turned Local only on in Settings, so say so and do not work around it.
`limit-exceeded`: the user's own cap; offer a cheaper route, never retry around
it. Build the shot from material you already have meanwhile.

## Older macOS

Some reads need a newer macOS (words and transcripts, describe, translation,
learned upscale and denoise, `record`). When a call answers `unsupported` or a
note says so, carry on with what works, tell the user what was skipped, and do
not retry it.

## The review loop

An editor watches the cut as the viewer will, then fixes, then watches again.

1. After each stage, `renderContactSheet` the cut, and `footage watch` the
   stretches you changed: does the line land on the right face, does a pause
   give the cut room, is the music under the voice?
2. At lock and before delivery, `critiqueEdit` (a direct call; `platform:
   "tiktok"` for shorts). It checks flashes, black and frozen frames, jump
   cuts, the eye jumping across a cut, montage cuts off the beat, generated
   shots that do not match the footage, sound (clicks at cuts, clipping,
   loudness, music over the voice, dead air), captions and retention. Each
   check gives its three worst moments with a small image: look at the images,
   fix the worst, run it again. A flagged jump cut in a vlog can stay if it is
   the style; say so. The flash check is approximate, not a certified test.
3. Say what you looked at and what you did not.

## The recipes

Calls marked `[direct]` are never inside `runScript`. Each recipe is a pattern:
swap in the user's clips, watch the result between steps, and take layer names
from `edit {action: "list"}` or `listLayers` (a clip layer is named after its
file).

**Phone clip to vertical short** ("45 seconds for TikTok"). A phone clip is
already vertical; a landscape source goes through `edit reframe {aspect:
"9:16"}` first (it follows the speaker, resets at every cut and sets burned
captions again at the vertical shape), and you `critiqueLayout` the result:
`text-cut` names any title the crop cuts.

```text
footage {action: "index", assets: ["talk.mov"]}                    [direct]
edit {action: "assemble", name: "Short", asset: "talk.mov"}
edit {action: "removeSilence", composition: "Short", punch: true}   // cuts sit in the quietest 10 ms between words
edit {action: "removeFillers", composition: "Short"}
edit {action: "tighten", composition: "Short", seconds: 45, end_on: "sentence"}
edit {action: "broll", composition: "Short", over: [{sentence: 5}], source: "auto"}   // own footage, then stills, $0
enhance {action: "voice", layer: "talk", strength: 0.75}            [direct]
addMusic {mood: "chill", duck_db: -10}      // the bed dips under the voice
grade {action: "auto", track: "V1"}   then   grade {action: "look", name: "film", composition: "Short", strength: 0.6}
kit.use("Short"); kit.footageCaptions("talk", {style: "pop", platform: "tiktok"})
critiqueEdit {composition: "Short", platform: "tiktok"}             [direct]   // then fix the worst moments
deliver {to: ["tiktok"]}                                            [direct]   // -14 LUFS, captions burned
```

**Text behind you, and an aura edit to a song.** Retime first, because a
cutout is made for the clip as it plays; then cut the person out; then dress it.

```text
importAsset {path: "song.mp3", kind: "audio"}; createLayer {kind: "audio", asset: "song.mp3"}
enhance {action: "ramp", layer: "Clip", style: "velocity", music: "song", place: true}   [direct]   // peaks land on the beats
cutout {action: "person", layer: "Clip"}                            [direct]   // look at three frames: start, middle, end
kit.textBehind("Clip", "SHIP IT", {start: {shot: 0}})              // the words sit between the clip and the person
kit.aura("Clip", {beats: "music", every: 2, color: "#FF3D8B"})     // outline and glow flash on the beats
```

Check that the person hides the word on every frame and the outline does not
flicker. A word nearly covered by the person reads as part of the place; pick a
big heavy word and let the person cross a corner of it.

**Recreate this edit** ("cut my clips like reference.mp4"). Learn the rhythm,
never copy the content.

```text
reference {action: "recipe", video: "reference.mp4"}               [direct]   // shot lengths, transitions, cuts on the beat
footage {action: "index", folder: "clips"}                          [direct]
edit {action: "recreate", recipe: <the recipe>, assets: ["clips/..."], music: "song.mp3", seconds: 30}
reference {action: "compare", video: "reference.mp4"}              [direct]   // fix the worst difference first
```

The result is an ordinary sequence: `edit paper export` shows it as text for the
user to change.

**App screen recording to launch film.** Redact before anything else touches it.

```text
footage {action: "index", assets: ["screen.mov"]}; footage {action: "watch", asset: "screen.mov", start: 0, end: 40}   [direct]   // find the clicks
track {action: "redact", layer: "Screen", find: ["secrets", "emails", "faces"], style: "blur"}   [direct]   // check each item it lists on a frame
kit.zoomFollow("Screen", {clicks: [{frame: 60, at: [-420, 210], zoom: 2}, ...]}); kit.cursor({path: [...], click: [1, 2]})
generateVideo {action: "estimate", prompt: "...", draft: true}     [direct]   // tell the user the price, then a draft, then the hero shot
place {media: "hero.mp4", as: "layer", at: 840}
kit.music(...); grade {action: "look", name: "film", composition: "Launch", strength: 0.5}; kit.endcard(...)
```

Points for `kit.zoomFollow` are px from the recording's centre, y up. The hero
generated shot is the one paid piece; the rest is free.

No recording yet? `record {seconds: 30}` films the screen (a `window` by title,
`audio: true` for the Mac's sound) and logs every click beside the file: then
`kit.zoomFollow("Screen")` with no `clicks` zooms on them. It needs the user's
Screen Recording permission for the app running Miran; on `permission-needed`
tell them where to switch it on (System Settings > Privacy & Security) and call
again. Have them ready before you start: it records at once.

**Podcast to highlights** ("three 30-second clips from a two-camera podcast").
Find the moments by laughter and energy, not by order.

```text
footage {action: "index", folder: "cams"}                           [direct]
footage {action: "search", sound: "laughter"}   and   {query: "the mistake"}   // each hit has an anchor; watch the best three
edit {action: "multicam", angles: ["camA.mp4", "camB.mp4"], wide: "wide.mp4", master: "camA.mp4"}   [direct]   // synced by sound, cuts follow the speaker
edit {action: "paper", mode: "export"}      // keep the lines inside one moment, rename the comp, mode "apply" (one composition per highlight)
edit {action: "reframe", aspect: "9:16"}; kit.footageCaptions(...)
```

Check the angles stay in sync within a frame on the cuts (`footage watch`).

**Velocity edit** ("dance.mov to this song, slow motion on the hits").
Slow motion is for the moment, not the shot; slow first, ramp second.

```text
enhance {action: "slowmo", layer: "Dance", factor: 4, range: [90, 120], place: true}          [direct]   // 2 graceful, 4 hero, 8 freeze
enhance {action: "ramp", layer: "Dance", style: "velocity", music: "song", place: true}       [direct]   // the clip's own sound goes quiet: score it
track {action: "body", layer: "Dance", joints: ["right_wrist", "left_wrist"]}                 [direct]
kit.poseFx("Dance", {joint: "right_wrist", fx: "trail"})
critiqueEdit {checks: ["beat", "flashes"]}                          [direct]
```

**Photo story** ("20 seconds from five photos, 3D camera moves"). It costs $0.

```text
cutout {action: "depth", layer: "Photo 1"}   // once per photo, a direct call; a 50 MB model downloads the first time (subject cutouts download one too, about 94 MB)
kit.scene({start: 0, end: 120}, () => kit.parallax3d("Photo 1", {move: "push", amount: 0.06}))   // per photo: push, pull, orbit, rise
kit.transition(...); kit.music(...)
```

Keep every move small (a push of 5 to 10 percent, an orbit of a few degrees):
edges stretch where near meets far, and `critiqueEdit` shows where.

**The impossible shot** ("I step out of my phone into a city you generate, my
title written in the sky"). Every department at once, one grade at the end.

```text
track {action: "plane", layer: "Walk", detect: "screen"}            [direct]   // the phone; kit.screenReplace("Walk", "app.png")
generateImage {prompt: "<the city, the light>", aspect: "16:9"}     [direct]   // tell the user the price first; a still plus parallax is the cheap world
cutout {action: "person", layer: "Walk"}                            [direct]
place {media: "city.png", as: "background", layer: "Walk cutout"}   // under the cutout, original turned off
kit.lightWrap("Walk cutout", "city", {width: 24, strength: 0.6})    // the city's light on the person's edge
a 3D title (createLayer text, setLayerField is_3d) high in the city's sky, between the city and the person: the camera move that gives the city its parallax carries it too
grade {action: "auto", layers: [...]} ; grade {action: "match", layer: "city", to: "Walk"}; grade {action: "look", ...}; grade {action: "grain", layer: "city", match: "Walk"}
critiqueEdit {checks: ["mixed", "eye", "frames"]}                   [direct]   // generated shots must read as the footage's world
```

## Report

Close with what was checked and what not, every paid call and its cost, what
the footage allowed and what was invented, and anything you would change.
