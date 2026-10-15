---
name: miran-render
description: Exports from Miran Fann - PNG frames and contact sheets, PNG sequences, MP4/HEVC/ProRes video on the native macOS encoder (no ffmpeg), deliver for every platform (shapes, loudness, captions, chapters, thumbnail), background render jobs, motion blur, audio mixing and render diagnostics. Use when producing any output file, choosing export quality, or when a render returns a job id.
---

# Exporting

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
| `draft` | H.264 at half size, motion blur capped at 4 samples: the fastest export | Timing checks while iterating |
| `standard` | H.264 High | Review copies |
| `web` | x264 tuned for flat animation (software) | Uploads: about a third of `high`'s size at 99.6% of the picture |
| `high` (default) | H.264 High, high constant quality, BT.709 | Delivery that plays everywhere |
| `max` | HEVC Main10 (10-bit) from full RGB | Dark scenes, gradients, glows, anything that bands |
| `master` | ProRes 422 HQ; ProRes 4444 with alpha for transparent comps; `.mov` path | Hand-off to an editor or colorist |

On macOS every export uses the native encoder (VideoToolbox): no ffmpeg is
needed, and a software fallback covers a missing GPU. Color is tagged BT.709, so
the PNGs show what the video shows. Repeat exports reuse unchanged 2 s pieces
(`cache`; grain exports render whole), so a second pass is cheap.

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

`backend:"auto"` (default) renders on the GPU (Metal); `"cpu"` gives the
deterministic reference for a parity check. `listRenderCapabilities`,
`inspectGpuBackend` and `inspectRenderGraph {frame}` diagnose;
`benchmarkRender` times frames.

## Motion blur

`setCompositionField`: `motion_blur_enabled` true, `shutter_angle` in degrees
(180 film look), `shutter_phase` (0 centred), `motion_blur_samples` = maximum
samples. Sampling is adaptive (about one sample per 1.5 px of motion), so still
frames render at normal speed; particles, animated masks and path morphs always
use every sample.

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
