---
name: miran-create
description: Builds and edits Miran Fann scenes - projects, compositions, layers (text, shape, solid, procedural gradients and patterns, image, nested, particle, null, adjustment, audio), typography, fills, strokes and micro-chamfer edges, effects, masks, track mattes, parenting, assets, and runScript for building many layers in one call. Use when creating or modifying a Miran scene or when you need exact parameter shapes for scene tools.
---

# Building scenes in Miran Fann

> **Lean tool list:** a call written below as `name {params}` is `miran.name({params})` inside `runScript` (or `query` when it only looks) unless `name` is in your tool list; exports, jobs, voiceover, downloads and panels stay direct calls.

Every mutating call is committed to an undo history. For animation load
**miran-motion**, camera **miran-camera**, measuring **miran-critique**,
export **miran-render**; the whole video workflow is **miran-video**.
Details on demand: `guide {topic:"layers"}`, `guide {topic:"text"}`,
`guide {topic:"effects"}` (every effect's params), `describeTool {tool}`.

## Conventions

| quantity | convention |
|---|---|
| position | px from the composition **centre**, **+y up** (`[0, 300]` is above centre) |
| size | px (`width`, `height`, `radius`, `font_size`) |
| rotation | degrees, positive = counter-clockwise |
| scale / opacity | 1 = 100% / 0..1 |
| color | `"#RRGGBB"`, `"#RRGGBBAA"`, or `[r,g,b,a]` with channels **0..1** (0..255 is refused) |
| time | frames; a layer shows on `[in_frame, out_frame)` |
| selectors | layer id (the number `createLayer` returns) or exact name; a result object works where a layer goes (`{layer: card}`, `layers: [a, b]`, a `{ids}` result), but `kit.group` returns a bare id, so `g.id` is `undefined` and the param is dropped |

Layers are 2D (camera-independent) by default. Paint order = creation order:
index 0 is the bottom, the last made paints on top (`listLayers` shows each
`index`). `reorderLayer {layer, to: "top" | "bottom"}`, `{above: other}`,
`{below: other}` or `{index}` changes it: exactly one of the four; `before`,
`after` and `z` are refused with that hint.

## Build many layers in one call

For anything beyond two or three calls use `runScript`: JavaScript calling
`miran.<tool>(params)` with loops and each call's result, atomic, one undo
step. See `guide {topic:"scripting"}`.

```js
const cards = ["Fast", "Precise", "Beautiful"].map((label, i) => {
  const x = (i - 1) * 520;
  const card = miran.createLayer({name: `Card ${i}`, kind: "shape", shape: "rounded",
    width: 440, height: 280, radius: 28, fill: "#16161D",
    stroke_width: 1.5, stroke_color: "#2D3139", specular_intensity: 0.6, position: [x, 0]}).id;
  const text = miran.createLayer({name: `Card ${i} Label`, kind: "text", text: label, font_size: 56}).id;
  miran.setTextStyle({layer: text, font_weight: 600, color: "#F5F5F7"});
  miran.parentLayer({layer: text, parent: card}); // child position is relative to the card
  return card;
});
return miran.renderFrame({out: "qa/cards.png"});
```

Inside scripts, `kit.*` builds finished pieces from the same tools:
kinetic words, mask reveals, typing, blur-ins, counters, draw-on lines and
rings, UI cards, a clicking cursor, charts, colour shifts, glows, SVG parts,
camera moves on a scene's group (drift, punch, zoom, parallax), transitions
between scenes and a finishing grade (`guide {topic:"kit"}`;
`miran.help("kit.name")` lists one piece's options).

## Draw in SVG, animate anything

- Anything with detail (icons, illustrations, UI mockups, badges, whole
  layouts) is best written as SVG markup: `importSvg {svg, name}` (or
  `kit.svg(markup, opts)`). `viewBox` = composition size maps SVG px 1:1;
  each top-level `<g id="x">` becomes the layer `<name>/x`, pivoting on its
  own centre. `trim.end` 0 -> 1 on a layer draws its strokes on
  (`guide {topic:"svg"}`). Drop shadows and blurs in SVG render natively;
  read `warnings` in the result for filters kept as a raster.
- A UI for `kit.screen3d` (the tilted hero shot): one `<g id>` per panel,
  inset from the screen's edge. Keep a panel's rows and items inside its group,
  or as their own `<g id>` declared after it and inside it (they ride it), so
  nothing slides off its card. Draw 1440x900 (face-on it shows at ~0.96x) with
  UI text at 32 px or more: a tilted wide shot is for shape, not reading.
  Captions are 2D layers above it, never in the SVG.
- `animateProperty` animates the look like CSS: `fill.color` / `stroke.color`
  (hex from/to), `stroke.width`, `fill.opacity`, `shape.width`,
  `shape.height`, `shape.radius`, `text.size`, `text.tracking`,
  `text.line_height`, and any effect parameter as
  `effect.<effect>.<param>` (added if missing; shortcuts `blur`, `glow`).
  Leave `from` out to start from the current value.

## The shape toolkit (After Effects shape layers)

`setShapes {layer, ops, gradient, stroke_gradient, stroke_style}` turns one
shape into most motion-design graphics (`guide {topic:"shapes"}`):

- `ops` (in order): `repeater` (copies with a cumulative transform and an
  opacity ramp: rings of dots, bursts, trails), `offset` (grow, shrink,
  concentric rings), `wiggle` (organic or hand-drawn boiling edges),
  `zig_zag` (seals, sound waves), `pucker_bloat`, `twist`,
  `round_corners`, `path` + `merge` (union, subtract, intersect, exclude:
  cut-outs and rings). Every numeric param animates as
  `op.<type>.<param>` (`op.repeater.copies`, `op.offset.amount`).
- `gradient` / `stroke_gradient`: `{colors, angle}` or `{stops, from, to}`,
  `type: "radial"`. Gradient text: the `gradient_overlay` effect.
- `stroke_style`: caps, joins, dashes, and `taper` (brush-like strokes
  that thin toward the ends, optionally rippling).
- Ready-made: `kit.burst`, `kit.ripple`, `kit.loader`, `kit.blob`,
  `kit.sketch`.

## Text boxes, draw-on strokes, counters

- `measureText {layer}` (or `{text, font_family, font_size, ...}`) returns
  the size plus a box per line and per word, in layer and composition px:
  place pills, underlines, cursors or per-word layers exactly on the text.
- Any stroked shape draws on with `animateProperty` on `trim.end` (0 -> 1);
  `trim.start` erases, `trim.offset` spins the drawn part around. Give it a
  transparent fill (`#00000000`); `setShapes {trim:{end:0.7}}` leaves it part
  drawn (a gauge).
- `setTextContent {layer, counter:{to, from, duration_frames, suffix, prefix, decimals, thousands}}`
  makes a text layer count; add `style: "duration", pattern: "m:ss"` (or `mm:ss`,
  `h:mm:ss`, `words`) to count seconds as a clock or "14 min 10 s".

## Layer kinds

`text` (then `setTextStyle`), `shape` (`rect`, `rounded`, `circle`,
`ellipse`, `star`, `polygon`; later `setShapes` for paths), `solid`,
`procedural` (full-frame generator chosen with `proc`: `linear_gradient`,
`radial_gradient`, `angular_gradient`, `mesh_gradient`, `noise`, `grid`,
`dot_grid`, ...; colors as `params:{"c0":"#hex","c1":"#hex"}`), `image` (asset),
`video` (a clip: `path`, trims and speed, see below), `nested` (another
composition), `particle` (`setParticles`), `null`
(parent/controller), `adjustment` (its effects apply to everything below),
`audio`.

Good backgrounds: a `radial_gradient` from a slightly lifted centre into
near-black, low-contrast; never a saturated full-frame rainbow or a giant
default circle (`critiquePrimitives` flags the circle).

## Typography (1080p video)

Choose the family for the brand and style, not by habit: `guide
{topic:"fonts"}` lists the macOS families that render well by mood, with
pairings and sizes. Headlines 96-140 px (display statements
160-400) at weight 700 with tracking -0.02..-0.04 em; subheads about a
third of the headline (40-48 px); body, UI text, labels and captions 28-36
px (never under 28 in a 1080p frame); small uppercase labels with
+0.1..+0.2 em. `tracking` is **em**, not
px. Check the font actually used with `measureText` (`families_used`):
missing families fall back, and the `warnings` of the text calls name them
and any glyph the font lacks (a star in SF Mono draws as a gap). Installed
families need no network (`importFont {list: true, query: "rounded"}`); any
Google font loads by name: `importFont {family: "Inter Tight 600"}` (cached, licence
recorded), then `setTextStyle {font_family: "Inter Tight", font_weight:
600}`. Variable fonts (SF Pro, New York, most Google fonts) take any
weight 100-900, and `text.weight` animates. Colour single characters (an
accent full stop, one word) with `setTextStyle {layer, colors: [{match:
".", color}]}` instead of extra layers.

## Micro-chamfer: the premium edge

Flat shapes read as cardboard; a thin lit stroke reads as machined:

```json
{"name":"Card","kind":"shape","shape":"rounded","width":640,"height":400,"radius":16,
 "fill":"#1E2026","stroke_width":1.5,"stroke_color":"#2D3139",
 "specular_angle":45,"specular_intensity":0.75}
```

`setShapes` patches: unspecified fields keep their value. Strokes exist only
on `kind:"shape"` (text: `setTextStyle`; SVG nodes: `setVectorStroke`).

## Effects, masks, mattes, parenting

- `addEffect {layer, effect, params}`: params by name with plain values
  (numbers, hex colors, `[x, y]`); unknown names are refused with the valid
  list. Effect offsets are screen px, y down. One or two effects per layer;
  finish the frame with an adjustment layer (vignette, a gentle grade).
- `addMask {layer, points, feather}`: polygon in layer px, y up.
- `setMatte {layer, matte, type}`: the matte layer still renders; set its
  opacity to 0 to use it purely as a matte.
- `parentLayer {layer, parent}`: the child inherits position/rotation/scale
  (not opacity); its position becomes relative to the parent, so it jumps
  unless `keep_world: true`.
- `setLayout {layer, children, direction, gap, padding, align, justify,
  wrap, width, height}`: auto layout (flexbox, like Figma). A null, shape
  or solid places its children in a row, column or grid every frame; text
  is measured, so longer copy re-flows and a card background hugs its
  content. Children's positions become offsets on their slots (entrances
  still work); `layout.gap` / `layout.padding` / `layout.order` animate.
  `setLayoutItem` takes a child out of the flow or lets it grow. In
  scripts: `kit.stack`, `kit.row`, `kit.grid` (`guide {topic:"layers"}`).
- `precompose {layers, name}`: moves layers into a new composition placed
  as one layer (the frame stays identical); parents, mattes and
  expression targets they need come along. `move_attributes: false`
  nests one layer's content and keeps its transform, effects and masks
  outside (`guide {topic:"layers"}`).
- `sceneGraph` prints parenting and matte edges.

## Real material: images, footage, screens, code

- Images and clips go straight on the timeline: `createLayer {kind:
  "image" | "video", path, fit: "contain" | "cover" | "stretch", width,
  height}` imports the file and sizes it to the box. A video layer also
  takes `start_frame` (where it starts), `src_in` / `src_out` (trim, in
  seconds) and `speed`; it ends with its clip.
- Product screens: write them as HTML and `importHtml {html, width,
  height}`: Chrome lays them out (real fonts, flexbox, grid, shadows) and
  each element with an `id` comes back as its own layer, ready to animate.
  A public site: `captureSite {url, place: true}` photographs it and reads
  its palette, fonts and logo. `guide {topic:"ui"}` has both.
- Art the engine cannot draw (a painted plate, an illustration, a texture):
  `generateImage {prompt, aspect, svg}` saves an image, and its SVG trace,
  in the film folder with the user's OpenRouter key (`guide {topic:"ui"}`).
- Code: `kit.code(src, {lang, file, numbers, reveal, highlight})` builds an
  editor panel with token colours that reveals by line or types.
- Diagrams: `kit.arrow(a, b, {label, curve})` connects two layers edge to
  edge with a drawn arrow; `kit.flow(items)` wraps pills or chips into rows.
- Other files: `importAsset {path, kind, name}`, then refer to it by name or
  id; `importAssetFolder` / `inferAssetRoles` for brand folders; SVG artwork
  as editable vectors via `importSvg` (**miran-design**).

## Guardrails

- Preserve user changes: `listLayers` / `inspectProject` before restructuring;
  never delete to "clean up".
- `createCheckpoint` before risky sweeps; `batch` and `runScript` are atomic.
- Paths stay inside the workspace.
- Do not claim a scene looks right without rendering and looking at it.

## Failures you will hit

- Layer invisible: check position (centre origin, y up), opacity, in/out
  frames, paint order.
- A param did nothing: the tool you called may not own it; `describeTool`.
