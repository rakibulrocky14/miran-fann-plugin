<p align="center"><img src="assets/logo.png" width="120" alt="Miran Fann"></p>

# Miran Fann

Motion graphics, directed by your agent. Miran Fann is a GPU motion-graphics
engine that runs on your Mac. With this plugin, Claude plans a film, builds each
shot as layers with keyframes, cameras and 3D, scores it with music, sound
effects and a voice, reviews its own frames, and exports MP4, HEVC or ProRes.

## What is inside

- **The Miran Fann MCP server**: tools for scenes, motion, camera, design,
  critique, sound and render, plus a live timeline viewer in your browser.
- **Eight skills** that teach Claude how to direct:
  - `miran-video` makes a whole film, from brief to export.
  - `miran-direct` covers concept, board, pacing, cutting and review.
  - `miran-create` builds layers, type and effects.
  - `miran-motion` animates like an animator.
  - `miran-camera` handles the camera, lights and 3D.
  - `miran-design` turns a brief into a design and handles SVG and Lottie.
  - `miran-critique` measures and reviews the work.
  - `miran-render` exports.

Ask for a video ("a 20-second launch film for my app") and Claude takes it
from there.

## Requirements

- A Mac with Apple Silicon (M1 or later). Windows and Linux are not supported yet.
- A Google account. The engine asks you to sign in once, in your browser.

## What it installs and runs

The plugin starts `scripts/miran-mcp`. That script looks for the engine at
`~/.miran/bin/miran`. If you already installed it with `npx miran-fann setup`,
it uses that copy.

If the engine is missing, the script:

1. Downloads the npm package `miran-darwin-arm64@0.1.3` from
   `https://registry.npmjs.org` (about 20 MB, once).
2. Checks the download against the SHA-512 written in the script.
3. Installs the one binary inside it to `~/.miran/bin/miran`.

It installs nothing else. Films and project files are written to your project
folder, or to `~/miran-work` when there is no project.

## What it sends and fetches

- **Sign-in**: Google sign-in through Supabase (`miranfann.com`). Google shares
  your name, email address and profile picture. Your session is kept on your Mac
  in `~/.miran/account.json`, readable only by you.
- **Usage**: each time the engine starts while you are signed in, it records
  that it is in use, with the Miran Fann and macOS versions and the app it runs in.
- **Issue reports**: when a film is done, Claude writes a short paragraph about
  the problems it ran into in Miran Fann. Paths, emails and keys are removed
  first, and reports are kept for 3 days. To turn this off, run
  `npx miran-fann feedback off`, or set `MIRAN_FEEDBACK=off` or `DO_NOT_TRACK=1`.
- **Updates**: every few hours, the engine checks the npm registry for a newer
  version and installs it. To turn this off, set `MIRAN_AUTO_UPDATE=off`.

Everything else happens only when you or Claude ask for it:

- the built-in voice model (Hugging Face, GitHub);
- cloud voices and generated images with your own keys (Google Gemini,
  ElevenLabs, OpenRouter);
- icon and animation search (Iconify, LottieFiles) and web fonts (Google Fonts);
- websites you ask it to capture.

API keys you add are kept in your macOS Keychain.

## Privacy Policy

Read the full policy at https://miranfann.com/privacy.html. It covers what is
collected, how it is used and stored (Supabase, Mumbai), sharing (none, apart
from the services listed above), retention, and how to have your data deleted.

## Support

Email mdrirocky08@gmail.com or visit https://miranfann.com.
