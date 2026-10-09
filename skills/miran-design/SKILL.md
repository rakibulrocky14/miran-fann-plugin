---
name: miran-design
description: Runs Miran Fann's design pipeline - from a structured brief to scored art-direction strategies, specs, diverse candidates, critique and repair, and an imported composition - plus SVG import and vector editing for logos and product artwork, and Lottie import and export. Use when the user wants Miran to propose a design from a brief or brand rules, wants layouts adapted to other aspect ratios, or brings SVG artwork or a Lottie animation, or wants a Lottie for the web.
---

# The design pipeline

> **Lean tool list:** a call written below as `name {params}` is `miran.name({params})` inside `runScript` (or `query` when it only looks) unless `name` is in your tool list; exports, jobs, voiceover, downloads and panels stay direct calls.

Miran has a design layer that turns a structured brief into a critiqued,
repairable composition. You describe intent; it proposes, scores and compiles.
You stay the editor: every candidate is a real composition you can modify with
scene tools afterwards.

Hand-building instead? **miran-create**. Measuring a result? **miran-critique**.
Camera and motion craft live in their own skills.

## Guardrails

- **Preserve user changes.** `buildStaticDesign` with `import:true` adds a
  composition; it does not replace the project. Import, then look, then decide.
- The brief is an **object**, not prose. Passing a paragraph fails schema
  validation before anything runs — see the shape below.

## The pipeline

```
analyzeBrief → pick strategy → createDesignSpec → (generateDesignCandidates)
             → buildStaticDesign(import:true) → critique → repair → import
```

### 1. Write the brief

```json
{
  "version": 1,
  "purpose": "launch",
  "audience": "developers",
  "message": "Ship faster",
  "content": [
    {"key": "headline", "text": "Ship faster", "priority": 100},
    {"key": "subline", "text": "The motion engine for agents", "priority": 60, "optional": true}
  ],
  "platform": "web",
  "format": {"width": 1920, "height": 1080, "aspect_label": "16:9", "fps": 30, "duration_s": 6},
  "brand": {
    "palette_hex": ["#08090A", "#1E2026", "#FF4700"],
    "font_families": ["Geist"],
    "rules": ["always uppercase eyebrows", "no gradients"]
  },
  "accessibility": {"min_contrast": 4.5, "min_body_px": 28},
  "desired_emotion": "precise confidence",
  "prohibited_elements": ["decorative grid"]
}
```

What matters and why:

- **`message` is the one thing the design must land.** If you cannot write it
  in a sentence, the design cannot either. Validation rejects an empty one.
- `content` is a priority-ordered inventory; `optional: true` lets the layout
  drop it on cramped formats (never headlines).
- `brand.rules` are free text ("always uppercase eyebrows") and are enforced
  as measurable constraints where possible.
- `accessibility` becomes QA: minimum WCAG-style contrast (default 4.5) and
  minimum body size in px at final resolution.
- `required_elements` / `prohibited_elements` turn taste into checkable rules.

### 2. Score the brief

`analyzeBrief` returns `{valid, content_keys, problems, scores}`: one entry
per strategy with a score and the **reasons** for every point (`"+2 sparse
brief suits low density"`).

Strategies: `restrained-minimal`, `swiss-typographic`, `editorial`,
`cinematic`, `premium-luxury`, `technical`, `data-centric`,
`expressive-type`, `playful`, `brutalist`, `monochrome`, `product-focused`
(`guide {topic:"design-pipeline"}` gives each thesis).

Take the top score, but read the reasons — they tell you what the brief is
asking for, which is what you will defend if the user pushes back.

### 3. Spec and candidates

`createDesignSpec {brief, strategy}` returns a full spec: art direction
(`art.composition`, `art.typography`, `art.motion`, `art.lighting`, and the
0..1 dials density/contrast/decoration/energy/formality/organic/rhythm/
texture/depth), background primitives with a seed, regions, and the brief
echoed inside.

`generateDesignCandidates {brief, n}` gives N genuinely different candidates
(distinct compositions first, not N shuffles of one). Inspect with
`inspectDesignCandidate` before committing to one.

### 4. Compile and import

`buildStaticDesign {spec, import:true, name}` compiles and adds the
composition to the project. `renderHeroFrame` renders one frame straight from
a spec without importing. `adaptToAspectRatio` recomposes for other formats —
**reflow, not rescale**: a 9:16 cut re-lays-out, it does not crop.

`planMotion` compiles and summarises the motion plan (order, channels,
easing) — read it before animating by hand on top.

### 5. Repair

`proposeRepairs` critiques and proposes (does not apply); `applyRepair`
applies one, checkpointed, optionally importing and writing a PNG. Repair is
evidence-based — the proposals cite the same findings the critique produces.

## SVG: artwork as editable vectors, not a baked image

`importSvg` brings artwork in as editable vector layers (groups kept by
default). Then:

- `listVectorNodes` / `findVectorNodes` walk and search nodes.
- `setVectorFill` restyles with hex, `"none"`, or full paint JSON;
  `setVectorStroke` takes `{"color":"#FFF","width":2,"cap":"round","join":"round","dash":[8,4]}`
  (or full VectorStroke JSON, or null to clear).
- `setVectorText` edits text while keeping it a native text node.
- `morphShape {layer, node, to | to_node | d}` morphs a path into any other
  outline (point counts, parts and holes matched); `convertTextToShapes`
  turns text into one path per letter for letter morphs.
  `animateVectorTransform` sets affine keys.
- `setProtectedArtwork` marks brand artwork as protected with a settle frame;
  `validateProtectedSettle` verifies it pixel-wise afterwards. Protected means
  protected: do not restyle it to make a layout work.
- `exportSvg` writes portable SVG with embedded images and **refuses** masks
  or filters that cannot round-trip — that refusal is information, not a bug.
- The `importSvg` result names everything you animate next: `parts` (`<g id>`
  to layer id), `at` (to its centre), `ids` (paint order), `rest_scale`;
  `kit.svg` returns the same names. Animate scale relative to `rest_scale`.
- Several cards inside one `<g id>` import as one layer. `explode: ["stages"]`
  (also on `kit.svg`) turns that group's children into layers of their own
  under a group layer, exactly where the SVG drew them: move the row via the
  group layer, a card via its own.
- A real hole (ticket notches, a window in a panel) is part of the artwork:
  `fill-rule="evenodd"`, a `<mask>` on the shape or group, or a `<clipPath>`.
  The hole moves with the part and shows what is behind it, which a patch in
  the background colour never does. `guide {topic:"svg"}` has a runnable
  example.

## Lottie: After Effects animations in, web animations out

- `lottie {action: "import", path | url | json, width, at, start_frame}`
  opens a Lottie `.json` or `.lottie` as editable layers in one nested layer
  (the composition the result names). Read the `report`: what did not come
  across is listed, never dropped.
- `lottie {action: "export", composition, out}` writes `.json` or `.lottie`
  for lottie-web, iOS and Android (moves, springs and expressions become
  eased keys; text becomes outlines unless `text: "live"`). Particles,
  procedural fills, video and most effects are reported, not exported: build
  web pieces from shapes, SVG and text.
- `findAssets {query, kind: "icon" | "lottie" | "font"}` searches Iconify
  (open-licence icons), LottieFiles and Google Fonts; `{action: "use", ...}`
  with a result's `use` params fetches, imports and credits it in one step;
  `{action: "credits"}` lists sources and licences for an end card.
- `guide {topic: "lottie"}` has the details and recipes.

## Brands and templates

- `brand {action: "save", name, theme, motion, logo}` once; `brand
  {action: "apply", name}` in each project themes every kit piece and
  template (palette, font, motion style, `kit.logo`).
- `template {action: "list"}`: the starter library (lower third, title
  card, stat callout, chart beat, UI notification, end card).
  `describe` gives the controls; `apply {name, values, start_frame}`
  builds the beat. `save` keeps a script or a composition as your own
  template with controls.

## Hand-building: plan, then check the plan

Write the token plan first: 4-6 named colours (bg, surface, text, muted,
accent), type roles (headline 96 px or more, supporting 40+, UI and body 28+
at 1080p), 8-9% safe margins, one focal point per frame (numbers:
**miran-direct** section 5). Then read it against what you default to and
change every match:

- Everything centred: the hero on a third, each block on one edge (a logo or a title card may centre).
- The same fade-up on every element: one device per beat, never the same twice running.
- Gradient text, a purple-blue wash, glow on everything: one accent, one light direction.
- Pure `#000` / `#FFF` fields: near-black `#0A0A0C`, warm white.
- Three identical cards in a row: vary size, weight or side.
- Decor under ~10% opacity: it vanishes in the encode; make it visible or cut it.

Negative space is a choice per shot (calm styles leave it, kinetic and data
styles fill the frame); every element serves hierarchy, grouping, readability
or story. For concept and pacing read `guide {topic:"creative"}`; unsure, run
**miran-critique** and let numbers say so.

## Failures you will hit

- **`brief: expected type object, got string`** — pass the structured brief.
- **`problems` non-empty from analyzeBrief** — fix the brief; the problems are
  the reasons the design will disappoint.
- **A candidate looks wrong in a way the spec cannot express** — import it and
  hand-fix with scene tools instead of fighting the spec.
- **SVG imports with missing fills** — the source used constructs that did not
  survive; `inspectSvg` and `compareSvgFidelity` tell you exactly what.

## Tool reference (generated)

`TOOLS.md` in this folder lists every pipeline and vector tool with its exact
parameters, generated from the runtime registry.
