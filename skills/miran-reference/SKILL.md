---
name: miran-reference
description: Find, acquire and study reference videos for Miran films and edits. Use for internet video links (YouTube, X, Vimeo, Envato/VideoHive previews and other video pages), local reference clips, contact sheets, motion breakdowns and applying observed craft to the current task. Video creation and editing search for references by default unless the user opts out.
---

# Study references, then direct this film

For **every video or animation task**, search for the best relevant reference
videos before planning or changing the film, unless the user explicitly says to
skip references/search, work offline, or use only supplied references. This includes
footage edits, cartoons, explainers, product films, shorts and revisions. Search
once per task or materially new direction; reuse the studies while iterating.
An explicit opt-out persists for that task. A supplied reference is the primary
direction; search for complementary references unless the user opts out.

The requested subject, claims, audience, brand, length, format and scope remain
authoritative. Learn composition, timing, entrances, camera, transitions, acting
and sound; build an original film with this task's own content and materials —
unless the user asks to recreate a reference exactly, which is its own craft
below. Either way, the reference's own media files never enter a delivered film.

## Find suitable videos

Use the host's web/video search or browser. Miran acquires links; it is not a
web-search provider. Search with this task's genre, audience, format and craft:
“editorial kinetic typography documentary opening”, “playful character reaction
short”, or “premium skincare macro product film”. Include Envato Elements and
VideoHive rendered previews when useful, and other sources that fit the task.

Shortlist a few strong candidates. Prioritize relevance, readable framing,
intentional motion, joins and pacing over popularity. Inspect the actual videos
before calling one the best fit. Record each source, why it was chosen and what
it contributes. A search thumbnail or description is not a video study.

If search or acquisition is unavailable, state the gap, try another suitable
source and continue from accessible material. Never invent an inspection or
silently replace the reference requirement with an unrelated style preset.

## Acquire the playable media

`reference` is a **direct tool call**, outside `runScript` and `query`. For a
`job` / `status: running` reply, wait for `jobStatus {job, wait_seconds: 30}` and
read its final `result`. Do not start the same download again while it runs.

```json
{"action":"add","source":"https://www.youtube.com/watch?v=VIDEO_ID","title":"Restrained type and deliberate pacing"}
{"action":"add","source":"https://x.com/creator/status/POST_ID"}
{"action":"add","source":"https://elements.envato.com/ITEM-SLUG"}
{"action":"add","source":"assets/reference.mp4"}
{"action":"list"}
```

The result contains `reference.id`, `reference.media`, provenance, dimensions,
duration and an overview sheet. Save the id in the film's `plan.md`; use it in
later calls. Studies live in the film's `references/` folder in the workspace
named by `guide {topic:"start"}`. This is normally your working folder; a host
without project roots may use the fallback reported in `workspace_note`.
Studies persist across sessions and add no footage to the rendered timeline.
Every path in the result also comes in an absolute
form (`media_abs`, `folder_abs`, `images_abs`, `out_abs`, `abs`): open those
directly with your file tools. Never search the disk for the study folder —
the result already names exactly where it is.

YouTube/X/Vimeo and supported social links use a separate `yt-dlp` executable.
Use a current yt-dlp with its EJS component and Deno 2.3+ or Node 22+ for YouTube;
the importer detects Deno 2.3+ first, then Node 22+, otherwise runs without a JS
runtime and reports it. Automatic remote code fetches stay off.
See the [yt-dlp runtime setup](https://github.com/yt-dlp/yt-dlp/wiki/EJS) when diagnosing missing-runtime or EJS errors.
Typical setup is `brew install yt-dlp deno` on macOS, or
`python -m pip install -U "yt-dlp[default]"` with Node 22+ on other systems;
follow the host's dependency-install permissions and existing package manager.
`miran doctor` reports the selected runtime and checks curl for direct media and
embedded previews. Official yt-dlp executables bundle EJS; pip's `default` group
installs it. With Homebrew or other package managers, verify their yt-dlp package
includes a matching `yt-dlp-ejs` component using the setup guide above. If missing, use the host's normal dependency-install
workflow or obtain an accessible media URL/local clip. Direct media and ordinary
embedded previews do not need it. No browser cookies are imported automatically.
With ffmpeg installed, platform studies merge separate sound/video streams and
repair inconsistent MP4 container timing without re-encoding. Check the returned
`has_audio` flag; a silent study cannot establish the source's sound design.
Envato/VideoHive pages are studied through their public rendered preview; keep
any watermark. A template project archive is not the rendered reference video.

For a JavaScript-only player the importer cannot resolve, inspect its page in the
host's browser, obtain a public playable media URL and pass that to `add`. Use an
accessible local clip for restricted sources. Generic HLS-only players currently
need an accessible MP4 or a local clip. Respect Local only mode. Downloads are
bounded to 256 MB of saved video and one five-minute acquisition deadline across
DNS, redirects, all candidates, probing and timing repair. Overview-sheet rendering
is a subsequent stage, outside that deadline. Prefer study-quality media to a large
master. Never bypass paywalls or DRM, strip watermarks, or put cookies/tokens in
notes. Report a failed acquisition accurately.

## Inspect the whole selected range, then the important motion

Start with the overview. Each sheet contains up to twelve labelled frames.
Request up to four pages at once and follow `next_page` until the selected range
is covered. Narrow the range and increase sampling around important motion.

```json
{"action":"sheets","video":"REFERENCE_ID","mode":"overview"}
{"action":"sheets","video":"REFERENCE_ID","mode":"interval","interval":1,"pages":4}
{"action":"sheets","video":"REFERENCE_ID","mode":"interval","start":3,"end":5,"interval":0.1}
{"action":"frame","video":"REFERENCE_ID","start":3.4}
```

For shots, text, words, sounds and beats, run `footage {action: "index", asset:
MEDIA_PATH}` using `reference.media` (its `media_abs` form opens the same file
directly). Then use `footage map`, `transcript`,
`search`, `best` and `watch` as needed. `detail: "full"` adds the available visual
labels and similarity analysis. With an index, `mode: "shots"` shows early,
middle and late frames of each shot:

```json
{"action":"sheets","video":"REFERENCE_ID","mode":"shots","pages":4}
{"action":"analyze","video":"REFERENCE_ID"}
{"action":"recipe","video":"REFERENCE_ID"}
```

Sheets/frames can cover the full video; the existing style analyzer measures its
first ten minutes. State the analyzed range and read capability/skip notes when
OCR, speech or description is unavailable. Dense sheets reveal entrances,
stagger, overshoot and joins that an overview misses. Listen to audio when judging
sound design; a waveform alone is not listening. Label inferred font identities
and eases as estimates. Pixels cannot recover the source editable project.

## Apply the study to this particular task

Write a compact breakdown in the film's `plan.md`: sources/ids and inspected
ranges; shot timing; hierarchy and type; how moves enter, travel, land and hold;
camera/framing; joins; sound accents; and what this task adopts or adapts. Give
every adopted choice a purpose in this film. Several references can contribute
different elements without importing their conflicting brands.

Keep timestamped observations with the reference:

```json
{"action":"note","video":"REFERENCE_ID","start":3,"end":5,"text":"Title masks up, then holds while the product pushes closer. Adapt the hand-off to our claim and real product screen; keep our brand colours."}
```

Continue through **miran-direct**, **miran-video** or **miran-edit**. Use the
recipe to guide an edit (`edit recreate` with the user's own clips), or author
the observed motion as editable layers/keys. Preserve the requested edit scope.

After a draft, `reference {action: "compare", video: REFERENCE_ID}` surfaces
measured pacing, contrast, palette and type differences. Judge the traits you
intended to adopt: a deliberate brand colour difference is not a defect. Review
the actual draft and moving transitions, fix the weakest adopted choices, and
report the reference sources and any extraction/analysis gaps.

## Recreate a reference exactly

Sometimes the ask is the recreation itself: rebuild this video, frame by frame,
same brand name, same words, same timing and look — often to prove how
faithfully Miran can do it. That is a proper Miran job, and exactness is the
point. Content is never the obstacle: matching the text, the brand and the
design is the work. Only the reference's own media files stay out of the
delivered film, and Miran refuses them as production assets in code, so build
everything natively and the boundary takes care of itself.

Work it like a tracing animator with a lightbox. Study the reference's sheets
until you can say what every element is, where it sits, and when it moves; dense
interval sheets around each entrance and join, single frames for held poses.
Then rebuild every element in Miran — shapes and type as native layers in the
measured sizes and colours, logos redrawn as vector artwork or imported from
the user's own files, screens written as HTML in the product's real style —
with motion matched to the observed timing: entrances on the same frames,
holds the same length, eases judged from the spacing between sampled frames.

Lay the reference over the work while you build:

```json
{"action":"guide","video":"REFERENCE_ID","composition":"main","opacity":0.5}
```

The guide is onion skin: it sits over the composition in the viewer,
`renderFrame` and contact sheets, so misalignments show as ghosts you can chase
down element by element. Dial its opacity to taste (calling `guide` again
adjusts it), and check timing by rendering your frames at the reference's sheet
timestamps and comparing positions. The guide never needs removing before an
export — every export leaves it out automatically and says so — but
`{"action":"guide","video":"REFERENCE_ID","remove":true}` or `deleteLayer`
takes it away, and releasing the reference removes it with the video. Never
duplicate a guide to keep its picture: a copy is an ordinary layer and the
study-only refusal returns, by design. A preview or contact sheet rendered while
the guide of a downloaded reference shows holds someone else's pixels, so the
file is marked reference media: it cannot be imported, even copied or renamed.
To keep a still for the film, render it with the guide removed. (A reference
that is the user's own clip is not marked: stills and renders of their own
footage stay ordinary.)

Deliver the recreation as a normal export. When the user wants proof of the
match — and only when they ask — offer the labelled comparison:

```json
{"action":"compare","video":"REFERENCE_ID","composition":"main","out":"renders/compare.mp4"}
```

That renders the original and your recreation frame-aligned and labelled
"Reference" and "Miran" (`"layout":"stack"` puts one over the other), with a
similarity score in the result. Present it as proof, never as the film: it is
the one file allowed to contain the reference's frames. It is the user's own
deliverable, so it can be imported back into a film when they want a
before-and-after showreel. If the video was
already released, the comparison cannot render and the result points at the
saved sheets instead — say so rather than reacquiring silently. Finish by
releasing the reference like any other study.

## Finish the film or study: release the video

When the film is finished, or this reference study is complete, call this once
for every saved reference used (repeating it is safe):

```json
{"action":"release","video":"REFERENCE_ID"}
```

Release deletes the copied/downloaded video (and any guide layers showing it) and
keeps source links, contact sheets, extracted frames and notes. The user's original file is untouched. The viewer can
still browse the saved study; further sheets/frames/analyze/recipe/compare need
video and report `reference-released`. To reacquire it, remove the entry and add
its source again. `remove` deletes the entire study plus analysis/recipe caches.
These file changes are outside scene undo history. Use one engine process per
workspace when writing studies. Library media, including public Envato/VideoHive
previews, is refused by production asset imports: learn the craft, use this
film's own materials. Platform terms and creator rights still apply (guide reference).
