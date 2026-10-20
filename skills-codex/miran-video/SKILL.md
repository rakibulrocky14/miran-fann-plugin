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
(layers, SVG, HTML), **miran-edit** (footage), **miran-motion**,
**miran-camera**, **miran-critique**, **miran-render**, and **miran-polish** for the
last pass before export. The server documents itself: `guide {topic}`,
`describeTool {tool}`, and `miran.help("kit.title")` inside a script for one
kit piece's options.

## Codex: how these films go wrong, and the answer to each

Codex films come out clean and tasteful, and too often as slides. First call
`openViewer` and give the user the link. Then research the subject and write
the research sheet ("Research the subject" below), so the screens are the
product's real ones. Before planning, open the **miran-direct** skill (its `SKILL.md` is in the folder
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

Your runs finish fast: spend the saved time on a second pass. Render the film
with `renderContactSheet {frames: "auto"}`, score it with the director's review
(miran-direct section 8), rebuild the two weakest beats. Then open the
**miran-polish** skill (its `SKILL.md` is in the folder next to this one) and
run its last pass in order before you export.

## The workflow

**References are the default for every video task and revision.** Before planning
or changing the film, search for the best videos relevant to this task unless the
user opts out, requests offline work, or limits references to supplied videos. Load
**miran-reference** (the sibling `SKILL.md`) for discovery, video-link downloads
including Envato previews, contact sheets and task-specific application. Reuse
the inspected studies through this task; keep the user's story and brand.
When the film or study is done, call `reference {action: "release", video: ID}`
for each reference to delete its copied video and keep sheets, frames and notes.

Films come out average when building starts before directing. Copy this
and tick it off:

```
- [ ] 0. Viewer: openViewer, the link in your reply before any other work (again with {composition} once it exists)
- [ ] 1. Brief: the one sentence to remember, audience, length, format, brand; the subject's truth
- [ ] 2. Research and real material: what the subject is, does and is for, its key moments, real copy and numbers; its real screens and pictures (captureSite on the key pages, screenshots from docs, store and press kit, the demo video through reference); the brand's colours, type and logo; a research sheet in plan.md saying which real screen each beat uses ("Research the subject")
- [ ] 3. Direct (miran-direct 1-7): three concepts (one rules out the obvious idea, one an unusual format), the treatment, the arc, the board (with a video key set: one or two generated hero shots where they lift the film, one price for the set; "Footage and generated media"), the design system, saved in plan.md
- [ ] 4. Spine: kit.music {reveal, ending} or voiceover / kit.dialogue {dry: true}; snap the board to it
- [ ] 5. Styleframes: opening, signature shot, end card as finished stills
- [ ] 6. Build: one runScript per kit.scene, each a stack of planes with something performing to its end ("What goes on screen"); a sheet and inspectMotionActivity after each, fix in place
- [ ] 7. Review: inspectMotionActivity (a film carried by music: about 5% median or more and no still hold over ~1 s but the breath before the climax); renderContactSheet {frames: "auto"}; critiqueLayout, critiqueMotion (dead_frame, read_in_motion); the director's review; fix the two weakest beats
- [ ] 8. Finish: the last pass in miran-polish's order (content, motion, type, colour, sound, export): joins, light, sound on every action, captions if it talks, kit.finish, loudness
- [ ] 9. Export: a draft for timing, then the final; read the check that comes back (frames from the file, size, loudness)
- [ ] 10. Report: what was checked, what came from the brief and what you invented
```

**plan.md.** Write the plan as a file next to the project (the film's
folder, `miran/<name>/plan.md`; in a host without file tools, in your
reply). Open it with the one sentence (who it is for, what they must
remember), then the treatment, the board and the design system. Re-read it
after any pause or context compaction, and keep it true as the film
changes: it is how the next session, or the user, knows what the film is.

**The viewer.** The first Miran call of every film is `openViewer`, and the
link goes into your reply before any other work: a live timeline, layers and
preview of the film as you build it. It is not a courtesy to skip when busy:
the user is watching there, and can edit there; your next result carries
`viewer_edits`. Build on their edits, never undo them. If you are partway
into a film and never opened it, open it now.

## Research the subject

The brand is the easy half of research: a logo, two colours, a typeface. A
film made from only that shows guessed screens in the right colours, and
anyone who knows the product sees it. Before the brief is final, find out
what the subject is and what it really looks like. This holds for every film:
a film about a city, a habit or a scientific idea starts with its facts and
its real pictures too.

- **The subject.** Read past the home page: feature pages, docs or help,
  pricing, the changelog, the app store listing, the launch post. Note, in its
  own words:
  - what it is, what it does and who it is for;
  - the three or four moments people use it for;
  - the real names of its screens and buttons;
  - its real copy and the numbers it quotes.
  For a subject that is not a product, note the facts, places, people, dates
  and numbers from sources a careful reader would trust.
- **Its real look.**
  - `captureSite {url}` on the home page and on each key page (a feature
    page, a docs page with a screenshot, pricing, the latest changelog entry).
    `scroll` reaches below the fold and `dark: true` shows the dark theme.
  - Screenshots from the official docs, the app store listing, the press kit
    and the README, found with your web search and fetch tools.
  - The demo or launch video through `reference` (miran-reference): the real
    screens in motion.
  - Study each one closely: the layout, the sidebar width, the row height,
    the type sizes, the icons, the empty and done states, the labels.
- **Rebuild, don't invent.** Write each screen the film needs as HTML from
  those screenshots (`importHtml`): real proportions, real labels, a
  believable amount of content.
  - Third-party images and videos are study material: rebuild from them,
    never paste them in, and release reference videos when the film is done.
  - When the user is the product's team, its captured pages are their own
    material and can appear as themselves (`captureSite {place: true}`).
- **The research sheet**, in `plan.md` before the treatment, and shown to the
  user with the viewer link: what was found and where, and which real screen
  or picture each beat uses. The user can correct it before anything is
  built: a wrong guess about what the product does costs one sentence here
  and a whole film later.

```text
Research: Pantry (a shared grocery list)
What it is    a household list that sorts itself into the aisle order of your usual shop
For           households of two to five who shop weekly ("never buy the second milk")
Key moments   add by voice; the list re-sorts by aisle; a partner ticks an item and it
              vanishes on your phone; the running total
Real copy     "Shop once." (home), "Aisle order" (features page), "Running total" (docs)
Numbers       4.8 stars from 12k ratings (App Store page)
Screens       list view (store screenshot 2), aisle view (features page, captured),
              sharing settings (docs), dark theme (captureSite dark: true)
Beats         1 voice-add, rebuilt from store shot 1; 2 aisle view, rebuilt from the
              features page; 3 two phones, the real ticked state; end card, the logo SVG
Invented      the partner's name and the list items (listed in the final report)
Not found     a demo video; the tablet layout
```

When nothing can be found (an unreleased product, no site), ask the user for
screenshots or a screen recording. If there are none, design the screens in
the brand's style and mark them invented.

## Real material first

Launch and tech films are judged against the real product. UI redrawn by
hand as positioned SVG reads as almost real, and almost real reads as fake.
`guide {topic:"ui"}` has the details and runnable examples.

- **A product with a website**: `captureSite {url}` photographs it and reads
  its palette, fonts, logo, headline and the boxes of its headings, buttons and
  images; `place: true` puts it in the composition, boxes in composition px. `brand {action: "capture",
  url}` saves the palette, fonts and logo as a brand to apply.
- **Art lifted out of a screenshot** (an orb, a glow cut from a captured page)
  carries the page's dither; scaled or used as a fill it reads as static, the
  cheap-video look. A screenshot works as a screen at its own size. Rebuild the
  art in the engine (a circle with a radial `gradient` and a soft
  `gaussian_blur`, or `kit.blob`): it stays smooth at any size.
- **A person, a real place, liquid, glass or a product in real light**, with a
  video key set: a generated hero shot with Miran's type and UI on top
  ("Footage and generated media").
- **Art the engine cannot draw** (a painted plate, an illustration, a
  texture, an object): `generateImage {prompt, aspect}` with the user's
  OpenRouter key from Miran Settings (`generate` also runs video, music and
  edit models on fal or Replicate keys; a `no-key` answer means the user adds
  the key in Miran Settings with `openSettings`, never in chat). Write the film's look once in plan.md
  (the brand's palette, the medium, the light) and put it in every prompt,
  so the images belong to one film; look at each before it goes in.
  `svg: true` traces it into vectors to animate (flat shapes trace cleanly, a
  photographic plate stays an image). Never ask it for words, logos, UI or
  charts. The same prompt returns the saved file; each new image costs money.
- **A screen to show** (an app view, a dashboard, a chat, a settings page):
  write it as HTML and CSS and `importHtml` it. Chrome lays it out with real
  fonts, flexbox, grid and shadows, and every element with an `id` comes in
  as its own layer, so rows arrive, buttons get clicked and cards lift.
  Body text 26-32 px in a 1200-1440 px page.
- **The user's footage or a screen recording**: `createLayer {kind:
  "video", path, fit: "cover"}`, `kit.zoomFollow` on each click,
  `kit.cursor`, `kit.screen3d` to tilt it. Cutting, cutouts, tracking and
  colour: **miran-edit**.
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
- **A video to recreate exactly** ("rebuild this, frame by frame"): same
  brand, words, timing and look are the brief — rebuild every element
  natively under a guide layer (`reference {action: "guide", video}`,
  previews-only onion skin that exports leave out), match frame by frame,
  deliver the recreation, and offer the labelled side-by-side
  (`reference {action: "compare", video, out}`) when they want proof.
  **miran-reference** teaches the workflow; only the reference's own media
  files are ever blocked, never its content.

With no real material, say so in the plan and design screens in the
product's style; never present invented screens as the real product.

## What goes on screen, and keeping it alive

Model-made films fail in one recognisable way: every beat is a section of a
website. A headline on one side, a small piece of UI on the other, a dark
empty field, the parts arriving and then nothing until the cut. A DeepSeek
launch film measured in the 0.1.6 study did exactly that: the same layout five
times, half a percent of the frame changing on a typical frame, ten of its
twenty-four seconds frozen, and `critiqueMotion` 100, because the few moves it
had were well eased. Good launch films come in two kinds:

- **Calm** ones (Cowork, Cursor 2.0, Linear Agent) hold for two or three
  seconds, but every hold is a full frame of legible product with a cursor or
  a voice about to act.
- **Energetic** ones (Lovable, Claude Design, Figma's Config reel) barely stop,
  with glow, light and UI motion under every second.

A launch film carried by music belongs with the energetic ones.

- **A beat is a world, not a layout.** Write each beat as what the viewer
  experiences, in two sentences, then work out the pixels; give every element
  a verb (slams, files into, docks, draws, counts down, locks in). An element
  without a verb is the one that fades in and sits there.
- **Put the idea on screen, not only the UI**: the product's own element huge
  (one row, the button, the number), the problem as a pile, the number as an
  object (the old one struck through, the new one landing in a ring), the
  features as objects with weight that dock into the product, the system as a
  diagram that builds, type as image against the product in depth.
- **Build every shot in planes**, back to front: the scene's own light
  (`kit.spot {at, size, intensity}`, a soft pool from one side, tinted by the
  theme), a faint slow atmosphere (`kit.particles("dust")`, a blurred
  `kit.blob`), back graphics that move slower than the subject (depth), the
  subject big (a UI that must be read fills 60-80% of the width), front details
  (a label, a rule that draws, a ring, the cursor, a burst on the hit), light on
  the subject (`kit.sheen` once, `kit.glow` that swells and settles), and one
  camera move with intent. Most produced frames use five to eight of these.
  The film's style decides what each plane is: the spot and dust in the
  recipe are one style's light and atmosphere. A print film's are grain and
  halftone, a Swiss film's a grid, an editorial film's one soft shadow.
- **Every beat keeps performing after it arrives**, through one of five
  things named on the board: the camera travelling to the part that matters;
  staged reveals paid out on beats; the product doing its job (a count ticks,
  rows land, a state flips); objects acting it out (a card files into a stack,
  cards fan, a result assembles); the cursor leading the eye to a click that
  starts the next thing. Atmosphere is the floor under these, never the
  performance; words still hold their place while they are read.
- **Cause and effect on one frame**: a click, then the ripple, the burst and
  the rows answering one after another, all hung on the click's frame
  (`kit.cursor(...).arrive[i]`); bigger things answer slower.
- **Carry motion across the cut**: the next shot enters the way the last one
  left, cut while both are still moving, one direction for "next" all film
  long, and a real object handed across the join when you can (`kit.match`).
- **Stillness has one job**: the breath before the climax (a third to three
  quarters of a second, then the hit). The end card's words hold to be read
  while a slow push or drift keeps the frame alive.

```js
// A fresh project: one beat built as planes, with the camera travelling to the part that matters.
miran.createComposition({name: "Dayline", width: 1920, height: 1080, fps: 30, duration_frames: 150});
// Dayline's own palette (its blue app icon, on its dark calendar), written in plan.md with its source.
kit.theme({font: "SF Pro", display: "SF Pro", bg: "#0A0E1C", surface: "#141A30", text: "#F1F4FF", muted: "#97A0C2", accent: "#4D6BFE", accent2: "#F2B84B"});
kit.backdrop({style: "solid"});
const T = kit.theme();
const {w, h} = kit.size();
const row = (id, when, who, note, hit) => `<div class="row${hit ? " hit" : ""}" id="${id}"><b>${when}</b> ${who}<span>${note}</span></div>`;
const html = `<style>* { box-sizing: border-box; }
  body { margin: 0; background: ${T.surface}; color: ${T.text}; font: 500 28px/1.3 "${T.font}", system-ui; }
  .app { display: grid; grid-template-columns: 250px 1fr; height: 100%; }
  .side { background: ${T.bg}; padding: 34px 26px; display: flex; flex-direction: column; gap: 14px; color: ${T.muted}; }
  .side b { color: ${T.text}; font: 700 32px "${T.display}", system-ui; margin-bottom: 18px; }
  .main { padding: 32px 36px; display: flex; flex-direction: column; gap: 14px; }
  h1 { font: 700 46px "${T.display}", system-ui; margin: 0 0 6px; }
  .row { display: flex; gap: 18px; padding: 20px 24px; border-radius: 16px; background: ${T.bg}; }
  .row span { margin-left: auto; color: ${T.muted}; font-size: 24px; }
  .row.hit { background: ${T.accent}; } .row.hit span { color: ${T.text}; }</style>
  <div class="app"><div class="side"><b>Dayline</b><div>This week</div><div>Team: Design</div><div>Rooms</div></div>
  <div class="main"><h1>Find a time for 6 people</h1>${row("tue", "Tue 10:00", "4 of 6 free", "Ana is out")}${row("wed", "Wed 14:30", "all 6 free", "best", true)}
  ${row("thu", "Thu 09:00", "5 of 6 free", "Raj is flying")}${row("fri", "Fri 16:00", "3 of 6 free", "two travelling")}${row("mon", "Mon 11:30", "4 of 6 free", "Lee at the dentist")}</div></div>`;
kit.scene({start: 0, end: 150, name: "The answer"}, ({start, end}) => {
  const light = kit.spot({at: [-w * 0.3, h * 0.35], size: w * 1.6, intensity: 0.45});      // the scene's light, from the top left
  const dust = kit.particles("dust", {start, colors: [T.text, T.accent], count: 14, size: 3}); // atmosphere, far and slow
  [light.id, dust.id].forEach((id) => miran.setLayerField({layer: id, field: "intended", value: true}));   // drift on purpose
  const r = miran.importHtml({name: "Dayline", width: 1440, height: 760, html});
  // The page's base goes in with its parts, or its background and sidebar stay flat behind the 3D screen.
  const s = kit.screen3d([r.base, r.parts.tue, r.parts.wed, r.parts.thu, r.parts.fri, r.parts.mon], {start, width: w * 0.72,
    face: {at: start + 45}, push: {to: "wed", at: start + 90, fill: 0.86}});
  kit.spot({behind: s.surface, intensity: 0.35});                              // light on the subject
  kit.sheen(r.parts.wed, {start: start + 118, dur: 26});                       // once, when the answer is close
});
return "the answer";
```

`miran-motion` has the reaction layer (followers, inertia) and effects as
moves (focus, glow, light, colour on a beat).

## Footage and generated media

Footage and generated media are materials like any other; the film process
above does not change. Know where competitors' "cinematic" frames come from:
the 0.1.6 study found most of Higgsfield's showcase frames are generated
video (Seedance, Kling, Veo), with heavy re-rolling and human finishing.
Their motion-design films are code, like ours. Their spectacle there is
density: a sea of objects, a carousel of real cards, a point-cloud object
with live HUD numbers, many copies of one designed element each doing
something. Build that from kit pieces.

A person, a real place, liquid, glass, fabric, food, weather or a product in
real light are pictures the engine does not draw. Cutting an interview or a
phone clip, cutouts, tracking, grading, delivery: load **miran-edit**. Generated
media (`generateImage`, `generateVideo`, `generate`) is a material like the
rest, and it earns its place the way a location shoot would: when it makes the
film better than motion design alone.

### Generated hero shots: plan them when they lift the film

**With a video key set, look for the shot that would gain real-world
presence**, and plan one or two generated hero shots into the board yourself.
Do not wait to be asked, and do not default to motion only. Then build Miran's
exact type, UI and motion on top of them: cutouts, screen replacement, light
wrap, and one grade and one grain pass over everything so the mix is one film.

*Can you?* `openSettings` (a direct call) returns `keys`; video models run on
the `openrouter` entry with `set: true`. Check once, when you write the board.
Hand the user the link only when the key is missing, and never ask for a key
in chat.

*Is this film one that gains?* Judge the shot, not the genre:

- **Good fits**: a person using the thing (hands on a phone, a face reacting,
  someone arriving in the place the product lives); a real place or weather (a
  street at dusk, rain on a window, a kitchen at 7 a.m.); liquid, glass,
  fabric, smoke, steam, food; the product as a physical object in real light
  (a bottle, a device, a package) under Miran's type; a cinematic establishing
  shot that hands off into the designed film; a brand or story film where a
  world with no photographic frame in it would read as a slideshow.
- **Poor fits, stay motion design**: a product-UI or screen demo (the real
  screen is the evidence, a stranger beside it dilutes it); kinetic type and
  lyric films; data stories and charts (the number is the picture); abstract
  brand motion and logo animation; any shot whose point is exact words, a logo,
  interface or a real person's likeness (models garble them, so Miran draws
  those on top); a shot that is decoration only.
- **The test**: cover the generated shot in the board. If the beat says the
  same thing without it, do not buy it. If a viewer would feel the missing
  camera (the hand, the light on the glass, the street), plan it. Two hero
  shots at most in a short film; the rest stays designed, which is also what
  makes the generated ones feel expensive.

*Plan it with its price, once.* In the board, a shot gets a line: what it
shows, its length, where Miran's type and UI sit over it, what the film does
without it. Price every shot with the exact params you will send
(`generateVideo {action: "estimate", ...}` returns the price, the user's
caps and `spent_today_usd`), add them, and put **one total for
the whole generated set** in the plan: "Two generated shots, about $0.45 in
all, inside your caps." Ask once, and run only on the yes. A budget named in
the brief is the yes up to that figure. With no yes (or no one to ask), build
the motion version and say in the report what the shots would have added.

- **Cheapest model that meets the shot.** The default is the user's video model
  from Settings (`heygen/heygen-video-1` unless they changed it): cheaper and
  better than Kling for most plates. `generateVideo {action: "models"}` lists
  live models with a five-second price. Go up to a premium model only when the
  default's draft shows it cannot do the shot, and a price above what you
  quoted is a new question. The default makes 5-15 s: plan the shot long and
  use two or three seconds of it (`place {duration}`).
- **Draft first, the hero last.** One cheap take to judge the idea (the same
  prompt at `resolution: "480p"` with a `seed`; look at the three frames in
  `images`), build the whole film on that take, and only when everything else
  holds, make the hero (`768p`, or `2K` when the camera pushes into it) with
  the same seed (close to the draft, not identical: look again) and swap it in with `place {as: "replace"}`. A premium or 2K
  call is the last thing the film buys.
- **Reuse saved results.** The same call returns the saved file for free
  (`cached: true`). A re-run with one word changed is a new bill, so settle the
  prompt, seed and resolution on the draft and repeat them exactly. `fresh:
  true` is a re-roll: say the price first.
- **Stay inside the person's Settings caps.** `limit-exceeded` means stop and
  offer the cheaper path (motion design, or a still with parallax); never
  retry around it. Pass `max_cost` as the price you quoted: it becomes a ceiling, and models
  that bill after the call need it anyway.
  For `no-key`, `local-only`, `key-refused` and `no-credits`, say it and carry
  on with motion design (the failures are in miran-edit).
- **Never surprise spend.** No paid call before the total was shown and
  accepted. Report what was spent at the end.

*No key?* If `openSettings` shows no `openrouter` key and a shot in the board
would gain, say it once, in the plan: "A generated hero shot (the opening street,
about $0.15) could lift this film. Add an OpenRouter key in Miran Settings to
unlock it. Carrying on with motion design." Then carry on and do not ask again.

*Ask the model for pictures, never for words.* Write the shot as camera, subject,
action, light and look, from the film's palette and light, plus "no text, no
logos, no interface". The brand's type, logo and UI come from Miran.

Three recipes (`[direct]` calls are refused inside `runScript`; the rest run
in scripts). Prompts, names and numbers are examples: yours come from the film.

**A product hero plate, Miran's type and UI over it.** The reveal of a drinks
brand: a real bottle in real light, the designed film around it.

```text
openSettings {}                                                          [direct]   // keys: openrouter set -> plan the shot into the board
generateVideo {action: "estimate", prompt: "A frosted glass bottle of cold brew on dark wet slate, condensation beading, a soft side light from the left, shallow focus, the camera pushes in slowly, no text, no logos", seconds: 5, resolution: "768p"}   [direct]   // add the other shots' estimates: ONE total in plan.md
generateVideo {prompt: "<the same prompt>", seconds: 5, resolution: "480p", seed: 7, name: "bottle"}   [direct]   // the draft: look at images[0]
place {media: "<path from the draft>", as: "background", at: 90, duration: 150, name: "Bottle plate"}
kit.lockup(...); kit.screen3d(...) over it; kit.spot({behind: ...}) matched to the plate's real light direction   // Miran's type and UI, in the plate's light
generateVideo {prompt: "<the same prompt>", seconds: 5, resolution: "768p", seed: 7, name: "bottle", max_cost: 0.25}   [direct]   // the hero, last
place {media: "<path from the hero>", as: "replace", layer: "Bottle plate"}   // same timing, transform and effects
grade {action: "look", name: "film", composition: "Launch", strength: 0.5}; kit.finish({vignette: 0.2, grain: 0.03})   // one grade and one grain over plate, type and UI
```

**A person cut out over a designed world.** A generated shot on a plain wall
(the cutout is cleaner), the world built in Miran, giant type between them.

```text
generateVideo {prompt: "A woman in a green raincoat turns toward the camera and laughs, plain light-grey studio wall, soft front light, locked-off medium shot, no text", seconds: 5, resolution: "480p", seed: 11, name: "ana"}   [direct]   // after the one total was accepted
place {media: "<path>", as: "layer", at: 0, name: "Ana"}
cutout {action: "person", layer: "Ana", mode: "replace"}                  [direct]   // "Ana cutout"; the grey wall turns off
kit.backdrop({style: "mesh", colors: [...]})                              // the world, from the brand's palette
kit.textBehind("Ana", "ON TIME", {start: 30})                             // giant type between the world and the person
kit.lightWrap("Ana cutout", <the backdrop's id>, {width: 24, strength: 0.5})   // the world's light on her edge
grade {action: "look", name: "film", composition: "Launch", strength: 0.4}; kit.finish({grain: 0.03})
```

If the person holds a phone, `track {action: "plane", layer: "Ana", detect:
"screen"}` and `kit.screenReplace("Ana", <the product's own screen>)` put the
product's real UI on it (miran-edit); the model never draws the interface.

**A generated establishing shot that hands off into motion design.** The
opening is a street at dusk; the film continues as designed UI on its last frame.

```text
generateImage {prompt: "A rain-lit street at dusk, one warm shop window, deep blue shadows, wide, no text", aspect: "16:9", name: "street"}   [direct]   // about $0.04: approve the frame first
generateVideo {prompt: "The camera glides slowly forward down the street, rain falling, reflections moving, no text", first_frame: "<the image path>", seconds: 5, resolution: "480p", seed: 3, name: "street"}   [direct]
place {media: "<path>", as: "layer", at: 0, duration: 90, name: "Street"}
place {media: {asset: "<path>", time: 4.8}, as: "background", at: 90, name: "Street held"}   // its last frame becomes the ground
kit.frame(...) pushing the same way the camera did; the product's UI arrives in the shop window's light   // motion carried across the cut
generateVideo {prompt: "<same>", first_frame: "<the image path>", seconds: 5, resolution: "768p", seed: 3, name: "street", max_cost: 0.25}   [direct]   // the hero last, swapped in with place {as: "replace", layer: "Street"}
```

Everything above is two or three sentences of a film, not the film: the
board still carries the story, and the designed shots still carry the product.

Every role is open (`place`):
own or generated media can be a shot, a background, the fill of a word, fire on
black over footage, a screen inside a tracked phone, a matte, a particle
sprite. So combine freely and invent the chain the film needs: a generated
styleframe that becomes a shot; a person cut out over a generated world with
light wrap; giant type with a video inside; a recording tilted in 3D with a
generated hero shot cut in; three ads from one product link
(`captureSite` for the brand, generated shots held to the product with a
bible, motion design on top, `deliver` for each size). One `grade` and one
grain pass over everything is what makes the mix one film.

## Craft as tests

Critics measure pixels. Taste is a set of questions asked of every move:

- **Every movement finishes a sentence.** "This pushes in because the
  cursor is about to click it." "This slams because it is the claim." If you
  cannot finish the sentence, cut the move. Motion that means nothing is a
  screensaver.
- **A hold stays alive because something in it changes meaning**: a state
  flips, a number climbs, the cursor travels, a light sweeps, a row arrives.
  A slow `kit.drift` is the fallback for a hold with nothing to change, and
  it moves the picture around the words, never the words being read
  (`critiqueMotion` reports `read_in_motion`). When that critic flags words
  moving, hold the words and keep the shot alive; never fix it by clearing
  the shot's motion.
- **Rule out the obvious.** Name the concept every other video on this
  subject would use, and do not make it. One of your three concepts takes
  an unusual form: a letter, a countdown, a front page, a map, a receipt.
- **Steer between two failures.** Slideshow: everything arrives early, then
  freezes. Screensaver: ambient motion that says nothing.
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

## Review

Look where films break: at the seams. `renderContactSheet {frames: "auto"}`
samples each scene's middle and both sides of every cut (`kit.scene` makes
the scenes), every cell labelled with its frame and time; a number (`frames:
12`) spreads that many evenly. Then:

- `inspectMotionActivity` first: for a film carried by music, about 5% to 25%
  median activity (energetic launch films measure 5-9%; calm UI films under 2%,
  holding only on a full frame of legible product) and no still hold over about
  a second except the breath before the climax.
- `critiqueLayout` (collisions, safe area, contrast, `misaligned`) and
  `critiqueMotion` (read it by its findings, not its score: `dead_frame` is a
  stretch with nothing but the background, `read_in_motion` is text travelling
  while it is read). It grades the moves that exist: a film with ten good
  moves and ten frozen seconds can score 100, so never read it as "alive".
- The director's review (miran-direct section 8): score each line 1-5, fix
  the two lowest, look once more.
- An independent reviewer when you can start one: give it frames (not the
  session), the brief and the bar; ask for defects ranked with timestamps.
  The builder is the worst judge of its own work.

What gives a film away on the sheet: type alone on a field held for
seconds; words read on the move; a push that crops half a word; a tilted
screen asked to be read; product UI at 14 px (push in until body text is 28);
the same layout or ease beat after beat; on 9:16, an empty half. Never claim a
beat works without looking at its sheet.

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
- Files go in the film's folder (`miran/<name>/`); an export never replaces a
  file another session made (the result says the name it used).
- Read `warnings` and the script `log`: missing glyphs, fallback fonts, a
  title set smaller to fit the frame.
- `importHtml`: in a page's CSS end every font stack with a system name
  (`-apple-system, system-ui`; `ui-monospace, Menlo` for code), or Chrome
  draws a serif. Import the page at the centre and move it by its root
  (`setProperty {layer: r.id, property: "position"}`): in 0.1.6 `at` offsets
  the page's base twice and its parts once. Give `kit.screen3d` the page's
  `r.base` with its parts, or the base stays flat behind the 3D screen.
- Atmosphere (dust, sparkles, a breathing `kit.spot`) moves on purpose: mark
  it `setLayerField {layer, field: "intended", value: true}` so
  `critiqueMotion` does not grade it as a crawl; never delete it to raise the
  score. Mark only atmosphere, never a scene group, a camera move or your own
  moves.

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

Join the beats with `kit.transition` once both sides exist, and pick the
style by what the two shots share (miran-direct section 2). A join moves
like a camera: it leaves rest and lands soft, the comp's motion blur (on in
every kit scene) does the smear, and the swish sound lands on the frame the
picture moves fastest. Do not stack a blur effect on a push or a whip.

### Look it up before you invent it

Miran ships a directory of 637 motion techniques with a Miran mapping for
each. Before designing a move you cannot name, search it:

```
motions {query: "staggered text", family: "type"}
motions {id: "wipe"}          // mapping.kind, args, recipe
```

`kit` and `effect` mappings are calls with arguments; `recipe` is a complete
`runScript` body you can run and adapt; `gap` names a limitation. The same
directory lists 352 CC0 recordings (`sounds {query: "whoosh"}`, then
`sounds {id, place: true}` as a direct call) for
the moments a synthesised `addSound` preset is too clean. Use both as a
vocabulary, not as a crutch: the film still has to be designed for this
subject.

## Revising a beat

Fix a beat where it stands. A note on a detail is a short script on the
layers already there, by the ids the kit returned or `listLayers`:
`setProperty` or `setLayerField` for a place, size, colour or text,
`editKeyframes` for one key, `shiftLayerTime {frames}` to start a piece
earlier or later (its sounds move with it), `deleteLayer`, `kit.addTo(scene,
ids)` for a new layer in the shot. A note on the shot is its script edited and
run again: a `kit.scene` with that name or frames rebuilds it in place, keeping
its transitions and every other shot; a `kit.transition` run again at a cut
replaces the one there. `undo` takes back only the last script.

## Score it

A silent video is unfinished. Kit pieces already play their sounds; add the
bed and the big moments (`guide {topic:"sound"}`):

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
playful for energy; epic and dark for cinematic). On calm music cut on bars and
section changes, not every beat. One hero sound per moment.

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
...). Gemini or ElevenLabs only when asked; `no-key` means: tell them to add it
in Miran Settings, never ask for a key in chat. `voiceover` again with the same
`name` re-voices the lines in place.

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
true})` returns every line's frames and `end`), size the composition to `end`
plus an ending beat (`setCompositionField {field: "duration"}`), then stage it
as miran-direct says: cast large, open wide, then two-shots and close-ups, the
punchline close after `pause: 1`, gestures on the stressed word, `kit.life`
under everything, a gag sound on its frame (`kit.sfx("boing", f)`). Draw sets
1.2x the frame so close-ups never show an edge.

## Vertical (9:16)

Compose for the full height: type and art in tiers, art in the lower third,
backgrounds bleeding off the edges. Keep text in the app's clear box:
`kit.safe({platform: "reels"})` (or `"tiktok"`, `"shorts"`);
`critiqueLayout {platform}` checks the same zones. Captions sit below the
centre, three words a phrase.

## Export

```json
{"out": "renders/draft.mp4", "quality": "draft"}
{"out": "renders/final.mp4"}
```

`draft` is half size and several times faster: watch it for timing.
`renderVideo` defaults to the whole composition at `high` (H.264) with the
sound mixed in; `max` is 10-bit HEVC for dark gradients and glow, `master`
is ProRes (`.mov`), `max_mb: 25` fits an upload. Motion blur is on for new
work (adaptive, free on still frames); record an off with
`setCompositionField {field: "motion_blur_enabled", value: false}` when a
look needs hard edges. Over MCP a long render returns
`{job}`: poll `jobStatus {job}`.

On a machine without a GPU (Miran renders on Flint, its CPU renderer), or when the export may outlast the host's
command, take it through in stretches. Ask `renderVideo {out:"renders/final.mp4",
preflight:true}` at the delivery settings: `estimate` samples this backend,
weights costly, median and cheap windows and includes pixel conversion
plus a short encode at these settings (hardware or software). It reports render and encode times
(`estimated_seconds` is their sum), whether the estimate is `uncertain`,
and offers ranges on the 2 s grid. Estimates are advisory: requested exports always render, even over
eight minutes. Only explicit `preflight:true` returns without exporting;
`start:0` renders without automatic whole-film sampling. One cold exception:
a first whole CPU export over 20 s with no measured rate for its settings
samples render and conversion only (no encode probe), so its result carries
the estimate and ranges, encode unmeasured and `uncertain: true`; a
measured rate is always a pure lookup. Render each offered `start, end` with `cache:true`,
`audio:false` and `check:false` to its own chunk path, keeping composition,
quality, scale and backend fixed. Think in 10–20 s of film at a time,
shorter on a heavy scene or a tighter command limit; keep the cuts on that
grid. Run a failed chunk again and let its completed pieces be reused.
Then one full `renderVideo` at the same settings, without start/end and
with sound enabled, joins the cached pictures and mixes the sound:
`segments.encoded` should be zero. **miran-render**
and `guide {topic:"render"}` cover cache availability and the cloud setup
script (`npx --yes miran-fann cloud-setup` in the release shipping it, or
`bash scripts/cloud-setup.sh` from a Miran checkout). **Headless cloud MCP
use needs sign-in support, which is coming:** the browser login currently
redirects to the VM's own loopback address. Dependency setup does not unlock
it; it works once that sign-in ships. Judge the completed film,
with sound, after this final join.

Every export opens the file it wrote and reports `check`: frames decoded
from the MP4 (a sheet, returned as an image), duration, size, bitrate, audio
and loudness, with problems named in `verdict`. Look at it: a defect only the
encoded file shows (blocky grain, a silent mix, a file too big to share) is
caught there. `checkExport {path}` checks any video. To publish to platforms
(sizes, captions, loudness, thumbnail) use `deliver` (**miran-render**).

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
