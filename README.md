<p align="center"><img src="assets/logo.png" width="120" alt="Miran Fann"></p>

# Miran Fann

Motion graphics, directed by your agent. Miran Fann is a GPU motion-graphics
engine that runs on your Mac. With this plugin, Claude plans a film, builds each
shot as layers with keyframes, cameras and 3D, scores it with music, sound
effects and a voice, reviews its own frames, and exports MP4, HEVC or ProRes.

## What is inside

- **The Miran Fann MCP server**: tools for scenes, motion, camera, design,
  critique, sound and render, plus a live timeline viewer in your browser.
- **Eleven skills** that teach Claude how to direct and edit:
  - `miran-video` makes a whole film, from brief to export.
  - `miran-edit` edits real footage, from logging to delivery.
  - `miran-direct` covers concept, board, pacing, cutting and review.
  - `miran-create` builds layers, type and effects.
  - `miran-motion` animates like an animator.
  - `miran-camera` handles the camera, lights and 3D.
  - `miran-design` turns a brief into a design and handles SVG and Lottie.
  - `miran-critique` measures and reviews the work.
  - `miran-polish` gives a film its last pass before export.
  - `miran-render` exports.
  - `miran-reference` finds and studies reference videos.

Ask for a video ("a 20-second launch film for my app") and Claude takes it
from there.

## Requirements

- A Mac with Apple Silicon (M1 or later) on macOS 14 or later (stable), or Linux (x64
  or arm64, glibc 2.35+; in beta). On Windows (in alpha), install with `npx miran-fann setup` instead of
  this plugin. Tell us what breaks: [issues](https://github.com/rakibulrocky14/miran-fann-plugin/issues).
- Some features need a newer macOS: slow motion 15.4, transcripts, translation,
  describing footage and learned upscale and denoise 26. `record` (macOS 15)
  asks for Screen Recording permission for the app that runs Claude.
- A Google account. The engine asks you to sign in once, in your browser.

## Install

This repo is also the `miran` plugin marketplace.

- **Claude Code**: `/plugin marketplace add rakibulrocky14/miran-fann-plugin`, then
  `/plugin install miran@miran`.
- **Codex**: `codex plugin marketplace add rakibulrocky14/miran-fann-plugin`, then
  `codex plugin add miran@miran`.
- **Muse Code**: `muse plugins marketplace add miran https://github.com/rakibulrocky14/miran-fann-plugin.git`,
  then `muse plugins install miran@miran` and `muse plugins approve plugin:miran:mcp_server:miran`
  (Muse asks you to trust a plugin's MCP server once).
- **ZCode**: use `npx miran-fann setup`. ZCode 0.16 accepts a GitHub marketplace
  without adding it, so setup installs its own copy.
- **Everything at once**: `npx miran-fann setup` installs the engine and adds the
  plugin to each of those apps it finds.

Start a new session afterwards.

## Updates

Use your app's plugin Update button (Claude Code: `/plugin`, then the `miran`
marketplace). The next session's launcher sees that the plugin is newer than the
engine and installs the matching engine. The engine also updates itself every
few hours (see below).

## What it installs and runs

The plugin starts `scripts/miran-mcp`. That script looks for the engine at
`~/.miran/bin/miran`.

If the engine is missing, or older than the version this plugin was released
with (0.1.6), the script:

1. Downloads the npm package `miran-darwin-arm64@0.1.6` from
   `https://registry.npmjs.org` (about 20 MB).
2. Checks the download against the SHA-512 written in the script.
3. Installs the one binary inside it to `~/.miran/bin/miran`.

A newer engine is used as it is. If the download fails, an engine already on
your Mac starts instead. The script installs nothing else. Films and project
files are written to your project folder, or to `~/miran-work` when there is
no project.

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
  version and installs it (with `npx miran-fann setup`, which also refreshes the
  plugin in your apps from this repo). To turn this off, set `MIRAN_AUTO_UPDATE=off`.

Everything else happens only when you or Claude ask for it:

- models downloaded the first time a feature needs them, pinned and checked:
  the built-in voice (about 350 MB; Hugging Face, GitHub), subject cutouts
  (about 94 MB) and depth (about 50 MB; Hugging Face), and voice cleanup
  (about 8 MB; GitHub);
- generated images, video, music and edits with your own keys (OpenRouter, fal,
  Replicate): the prompt and the files Claude passes go straight to that one
  provider and are billed to your account;
- cloud voices with your own keys (Google Gemini, ElevenLabs);
- icon and animation search (Iconify, LottieFiles) and web fonts (Google Fonts);
- websites you ask it to capture.

Footage is read on your Mac and leaves it only inside a generate call it is
passed to. Screen recordings stay on your Mac, and so does the key that signs
content credentials (it is kept in your Keychain). Every outbound request is
listed in `~/.miran/network.jsonl` and in Miran Settings (time, tool, host and
path, bytes, status; never a key or a prompt). The **Local only** switch in
Settings stops all of them except sign-in, feedback and the update check.

API keys you add are kept in your macOS Keychain.

## Privacy Policy

Read the full policy at https://miranfann.com/privacy.html. It covers what is
collected, how it is used and stored (Supabase, Mumbai), sharing (none, apart
from the services listed above), retention, and how to have your data deleted.

## Support

Email mdrirocky08@gmail.com or visit https://miranfann.com.
