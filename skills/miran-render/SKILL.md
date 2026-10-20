---
name: miran-render
description: Exports from Miran Fann - PNG frames and contact sheets, PNG sequences, MP4/HEVC/ProRes video on the native macOS encoder (no ffmpeg), deliver for every platform (shapes, loudness, captions, chapters, thumbnail), background render jobs, motion blur, audio mixing and render diagnostics. Use when producing any output file, choosing export quality, or when a render returns a job id.
---

# Exporting

Video tasks search for suitable references by default unless the user opts out,
requests offline work, or limits reference research to supplied videos. Load the sibling
**miran-reference** skill when no study exists; reuse the task's inspected
references during export checks and preserve the requested export scope.
When the film or study is done, call `reference {action: "release", video: ID}`
for each reference to delete its copied video and keep sheets, frames and notes.

> **Lean tool list:** a call written below as `name {params}` is `miran.name({params})` inside `runScript` (or `query` when it only looks) unless `name` is in your tool list; exports, jobs, voiceover, downloads and panels stay direct calls.

During work the contact sheet is the tool (**miran-critique**); this skill
is about deliverables. Background: `guide {topic:"render"}`; parameters:
`TOOLS.md` in this folder.

## Stills

- `renderFrame {frame, out}`: one PNG, returned to you as an image.
- `renderContactSheet {frames, columns, out}`: several labelled frames in one
  image; `frames: "auto"` picks the key poses of every scene. At most 2400 px
  wide (`max_width`, 0 = full size).
- `scale: 0.5` renders frames, sheets and video at half size: iterate at half,
  deliver at full.
- `renderRange {start, end, dir}`: PNG sequence, `end` exclusive, max 3600
  frames per call.

## Where files go

Each film keeps its own folder: renders, sheets, sounds, voice and captures
land in `miran/<film>/` (the result says where). Write `out` as
`renders/final.mp4` and it resolves there. An export never overwrites a file
another session made: the new one gets `-2`, `-3` and `note_kept` says so.

## Video

```json
{"tool":"renderVideo","arguments":{"out":"renders/final.mp4"}}
```

`end` defaults to the composition length (exclusive; up to 3 hours of film per
call). Width and height must be even. The result then checks the file it wrote
(`check`, on by default): duration, size, bitrate, loudness and true peak, plus
a strip of eight frames decoded from the file, returned as an image. Look at it
and fix every `problem` before you call the export done. `checkExport {path}`
checks any file.

| quality | output | when |
|---|---|---|
| `draft` | H.264 at half size, motion blur capped at 4 taps (measured frames too): the fastest export | Timing checks while iterating |
| `standard` | H.264 High | Review copies |
| `web` | x264 tuned for flat animation (software) | Uploads: about a third of `high`'s size at 99.6% of the picture |
| `high` (default) | H.264 High, high constant quality, BT.709 | Delivery that plays everywhere |
| `max` | HEVC Main10 (10-bit) from full RGB | Dark scenes, gradients, glows, anything that bands |
| `master` | ProRes 422 HQ; ProRes 4444 with alpha for transparent comps; `chroma: "444"` for ProRes 4444 without alpha; `.mov` path | Hand-off to an editor or colorist |

Colour is stored at half resolution in every delivery preset (H.264 and HEVC are
4:2:0, `master` is 4:2:2), so a saturated edge, such as a red button on black or
white type on red, softens by about one chroma pixel. The chroma is sited on the
left luma column and the stream is tagged so, which is what players expect.
When the file will be keyed, graded or composited, ask for full-resolution
colour: `quality: "master", chroma: "444"` writes ProRes 4444 without alpha
(`encoding.chroma` says `4:4:4`; a transparent comp already keeps 4444 with its
alpha).

On macOS exports encode through VideoToolbox either way — an ffmpeg child with
`h264_videotoolbox`/`hevc_videotoolbox` by default, or AVAssetWriter in process
when ffmpeg is absent or `MIRAN_ENCODER=apple` — so ffmpeg is not required, and
a software fallback covers a missing GPU. Color is tagged BT.709, so
the PNGs show what the video shows. Repeat exports reuse unchanged 2 s pieces
(`cache`; grain exports render whole), so a second pass is cheap.

On a CPU-only machine, let the export advance in short stretches of film.
Start with `renderVideo {out:"renders/final.mp4", preflight:true}` at the
intended quality and size. It samples up to nine frames in costly, median
and cheap windows, weighted by frame cost, including pixel conversion and
a short encode at these settings (hardware or software). `estimate` reports the backend, render and encode
times, their sum `estimated_seconds`, whether it is `uncertain` (a cut
window, an unsampled encode, or cost bands disagreeing by over
2x), and `ranges` on the 2 s cache grid. Sound, joining and cold setup are extra. A requested export
always renders, even when the advisory estimate exceeds eight minutes.
Only explicit `preflight:true` stops after sampling. `start:0` renders
without automatic whole-film sampling; `deliver` and template batches render.
One cold exception: a first whole CPU export over 20 s whose settings have
no measured rate yet samples its render and conversion windows (never the
encoder), so its result still carries the estimate and ranges with the
encode unmeasured and `uncertain: true`. An export with a measured rate is
a pure lookup and is never sampled.

Work through those ranges with `renderVideo {start, end, cache:true,
audio:false, check:false, out:"renders/chunk-01.mp4"}`, using the same
composition, quality, scale and backend each time. About 10–20 s of film is
a useful stride; the estimate shortens it for heavier frames. If the host
has a tighter command limit, shorten the stride on the same 2 s grid. A
failed or timed-out range is simply run again: its finished pieces are
already safe in the cache after their encoder run finishes (per piece on
x264, per continuous missing run on native codecs). Once every range is done, call one full
`renderVideo {out:"renders/final.mp4", cache:true}` at those same export
settings, without start/end or `audio:false`. It joins the saved pictures
and mixes the whole soundtrack;
check that `segments.encoded` is zero, then look at the final export check.
Keep the scene unchanged through the loop. If `cache_available` is false
(grain, a size budget, an animated image or a disabled cache), this join
workflow is unavailable; `guide {topic:"render"}` explains the options and
the cloud dependency setup: `npx --yes miran-fann cloud-setup` in the
release that ships it, or `bash scripts/cloud-setup.sh` from a Miran
checkout (older npm releases do not include it). **Headless cloud MCP
use needs sign-in support, which is coming.** The current browser login
redirects to the VM's loopback address. Installing dependencies alone does
not enable it; it works once that sign-in ships.

For a file that has to fit somewhere (chats and mail take about 25 MB), pass
`max_mb: 25`: the whole file, sound included, lands under it, and `fit` reports
the size and the measured `match` (99+ looks identical; under 97 type goes
soft). `fits: false`: lower `scale` or shorten.

`out` ending in `.gif`, `.webp` or `.apng` makes a looping animated image
(`max_mb` shrinks it to fit). `exportVersions {aspects: ["9:16", "1:1"], out}`
builds other aspect ratios as their own compositions (content refits scene by
scene); read each version's `layout.new_issues`.

Sound: audio layers (music from `addMusic`, effects from `addSound` or kit
pieces, imported files, voiceover lines) are mixed in automatically and
reported as `audio.mixed`; `audio:false` exports silent. A composition without
sound exports silent and says so: score it first (`guide {topic:"sound"}`).
Voice lines mix in at -19 dBFS speech and the music bed ducks 9 dB under them
(`duck_db`).

## Deliver: every file a release needs

`deliver {to: [...]}` is a direct call and a job: one file per target,
`youtube` and `x` (16:9), `shorts`, `tiktok`, `reels` (9:16), `linkedin` (4:5),
`master` (ProRes 422 HQ), `alpha` (ProRes 4444, transparency) and `podcast`
(M4A). Each gets its shape (cut footage goes tall by `edit reframe`, following
the face), its loudness (-14 LUFS, podcast -16, or `loudness`; true peak under
-1 dBTP, measured again on the encoded file), captions (`captions`: `burn` keeps
them in the picture, `srt`, `vtt` or `both` write the sidecar and render a
clean picture), `chapters` for YouTube (`[{at, title}]`, the first at 0, at
least three), a 1280x720 `thumbnail` (`true`, or `{seconds | frame | anchor,
title}`) and a `deliver.json` listing every file. The project is not changed.
Run `critiqueEdit` first and look at the vertical versions before publishing.

MP4 and MOV files carry content credentials (`credentials`: `auto` signs when
the cut has generated media, `on` signs every cut, `off` skips them). They say
which parts a model made and that the file is unchanged since Miran wrote it;
the signer is this Mac, not a verified person or company. Never claim they prove
who made the film.

## Background jobs

A render still running after ~40 s returns early:

```json
{"job":"job-1","status":"running","progress":{"frame":412,"total_frames":900}}
```

Call `jobStatus {job:"job-1"}`: it waits up to ~40 s and returns progress, or
the final result once done. `cancelJob {job}` stops it and removes the partial
file; `listJobs` shows all. Do not edit the composition being rendered.

## Backends

`backend:"auto"` (default) renders on the GPU (Metal); with no GPU it
renders on Flint, Miran's own CPU renderer, and `"cpu"` asks for Flint
(deterministic, slower). Each result names its `engine` and
`engine_reason` (a fallback says so). `listRenderCapabilities` and
`inspectGpuBackend` show both engines side by side;
`inspectRenderGraph {frame}` diagnoses; `benchmarkRender` times frames.

## Motion blur

It is on for new work: `createComposition` and the comps the tools create
start blurred (180°, phase centred, adaptive taps). `setCompositionField`:
`motion_blur_enabled` false records an off that `kit.scene` never overrides,
`shutter_angle` in degrees (180 film look), `shutter_phase` (0 centred),
`motion_blur_samples` = maximum samples. Sampling is adaptive (about one
sample per 1.5 px of motion), so still frames render at normal speed;
particles, animated masks and path morphs always use every sample. Draft
renders cap taps at 4, measured frames included.

## Guardrails

- Rendering never changes the project. A file this session wrote is replaced
  on the next export of the same name; anything else is kept.
- `projectHash` identifies the exact state that produced a file.
- Export after scripts, not inside them (`runScript` refuses renderVideo).

## Failures you will hit

- **`status:"running"`** is not a failure: poll `jobStatus`.
- **Odd size** refused: make width and height even.
- **Banding** in gradients: `quality:"max"`.
- **Export softer than the frames, crawling dark areas, a huge file**: grain.
  Fresh grain every frame is noise the encoder spends the bitrate on.
  `kit.finish` holds each pattern for 3 frames and keeps it static on films
  over a minute; on a hand-made `grain` effect set `hold` (3, or 0 for static)
  and keep it soft (`size` 2, `amount` about 0.02). `encoding.tip` says when.
- **`master` refused**: it needs a `.mov` output path.
- **No sound**: audio layers disabled or out of range; `mixAudio` reports
  what it mixed.
