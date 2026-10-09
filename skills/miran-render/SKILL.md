---
name: miran-render
description: Exports from Miran Fann - PNG frames and contact sheets, PNG sequences, MP4/HEVC/ProRes video with quality presets tuned for Apple Silicon (VideoToolbox), background render jobs for long exports, adaptive motion blur, audio mixing and muxing, and render diagnostics. Use when producing any output file, choosing export quality, or when a render returns a job id.
---

# Exporting

> **Lean tool list:** a call written below as `name {params}` is `miran.name({params})` inside `runScript` (or `query` when it only looks) unless `name` is in your tool list; exports, jobs, voiceover, downloads and panels stay direct calls.

During work the contact sheet is the tool (**miran-critique**); this skill
is about deliverables. Background: `guide {topic:"render"}`; parameters:
`TOOLS.md` in this folder.

## Stills

- `renderFrame {frame, out}`: one PNG, returned to you as an image.
- `renderContactSheet {frames, columns, out}`: several frames in one image,
  each labelled with its frame and time; `frames: "auto"` picks the key
  poses of every scene. It comes back at most 2400 px wide (`max_width`,
  0 = full size).
- `scale: 0.5` on frames, ranges, sheets and video renders at half size
  (camera, blurs and shadows scaled with it): iterate at half, deliver at
  full.
- `renderRange {start, end, dir}`: PNG sequence, `end` exclusive, max 3600
  frames per call.

## Where files go

Each film keeps its own folder: renders, sheets, sounds, voice and captures
of the composition land in `miran/<film>/` (the name comes from the project
or the first composition; the result says where). Write `out` as
`renders/final.mp4` and it resolves there. An export never overwrites a
file another session made: the new one gets `-2`, `-3` and the result's
`note_kept` says so.

## Video

```json
{"tool":"renderVideo","arguments":{"out":"renders/final.mp4"}}
```

`end` defaults to the composition length (exclusive; up to 3 hours of film
per call). Width and height must be even. The result reports the encoding used,
and then checks the file it wrote (`check`, on by default): duration, size,
bitrate, loudness and true peak, plus a strip of eight frames decoded from
the encoded file, returned as an image. Look at it and fix every `problem`
before you call the export done. `checkExport {path}` checks any file.

| quality | output | when |
|---|---|---|
| `draft` | H.264 at half size, motion blur capped at 4 samples: the fastest export | Timing checks while iterating |
| `standard` | H.264 High | Review copies |
| `web` | x264 tuned for flat animation (software) | Uploads: about a third of `high`'s size at 99.6% of the picture |
| `high` (default) | H.264 High, high constant quality, BT.709 | Delivery that plays everywhere |
| `max` | HEVC Main10 (10-bit) from full RGB | Dark scenes, gradients, glows, anything that bands |
| `master` | ProRes 422 HQ; ProRes 4444 with alpha for transparent comps; `.mov` path | Hand-off to an editor or colorist |

Encoding is hardware (VideoToolbox) on Apple Silicon with a software
fallback. Color is tagged BT.709, so what the PNGs show is what the video
shows. Repeat exports reuse unchanged 2 s pieces (`cache`, on by default;
grain exports render whole), so a second pass after a small fix is cheap.

For a file that has to fit somewhere (chats and mail take about 25 MB),
pass `max_mb: 25`: the whole file, sound included, lands under it, and the
result's `fit` reports the size, the passes and the measured `match`
(99+ looks identical; under 97 type goes soft). `fits: false`: lower
`scale` or shorten. Every export reports its `mb`.

`out` ending in `.gif`, `.webp` or `.apng` makes a looping animated image
(GIF 15 fps at 640 px; palette per scene; `max_mb` shrinks it to fit).
`exportVersions {aspects: ["9:16", "1:1"], out}` builds and exports other
aspect ratios as their own compositions (content refits scene by scene,
side-by-side groups stack); read each version's `layout.new_issues`.

Sound: when the composition has audio layers (music from `addMusic`,
effects from `addSound` or kit pieces, imported files), `renderVideo`
mixes them in automatically and reports `audio.mixed`; `audio:false`
exports silent. A composition without sound exports silent and the result
says so: score it first (`guide {topic:"sound"}`). Voiceover lines
(`voiceover {script}`, guide `{topic:"voices"}`) are audio layers too: they
mix in at -19 dBFS speech and the music bed ducks 9 dB under them
(`duck_db`).

## Background jobs

MCP hosts time out long calls (~60 s), so a render still running after ~40 s
returns early:

```json
{"job":"job-1","status":"running","progress":{"frame":412,"total_frames":900}}
```

Call `jobStatus {job:"job-1"}`: it waits up to ~40 s and returns progress,
or the final result (identical to what the render would have returned) once
done. `cancelJob {job}` stops it and removes the partial file; `listJobs`
shows all. Keep working meanwhile, but do not edit the composition being
rendered.

## Backends

`backend:"auto"` (default) renders on the GPU (Metal on Apple Silicon); it
falls back to the CPU reference only without a GPU. Use `backend:"cpu"` only
to get the deterministic reference for a parity check.
`listRenderCapabilities`, `inspectGpuBackend` and `inspectRenderGraph
{frame}` (which nodes run where, with reasons) diagnose; `benchmarkRender`
times frames.

## Motion blur

`setCompositionField`: `motion_blur_enabled` true, `shutter_angle` in
degrees (180 film look), `shutter_phase` degrees (0 centred),
`motion_blur_samples` = maximum samples. Adaptive sampling takes about one
sample per 1.5 px of on-screen motion per frame, so still frames render at
normal speed; particles, animated masks and path morphs always use every
sample. `inspectRenderGraph` reports the planned samples and why.

## Guardrails

- Rendering never changes the project. A file this session wrote is
  replaced on the next export of the same name; anything else is kept and
  the new file is versioned.
- `projectHash` identifies the exact state that produced a file.
- Export after scripts, not inside them (`runScript` refuses renderVideo).

## Failures you will hit

- **`status:"running"`** is not a failure: poll `jobStatus`.
- **Odd size** refused: make width and height even.
- **Banding** in gradients: `quality:"max"`.
- **Export softer than the frames, crawling dark areas, a huge file**:
  grain. Fresh grain every frame is noise the encoder spends the bitrate
  on. `kit.finish` holds each grain pattern for 3 frames (film-like, a
  third of the cost) and keeps it static on films over a minute; on a
  hand-made `grain` effect set `hold` (3, or 0 for static). Keep it soft
  (`size` 2, `amount` about 0.02). The result's `encoding.tip` says when.
- **`master` refused**: it needs a `.mov` output path.
- **No sound**: audio layers disabled or out of range; `mixAudio` reports
  what it mixed.
