---
name: add-assets-to-app
description: Download finished svgapp.ai mascots, poses, illustrations and animations into the project and use them in the UI (empty states, onboarding, 404 pages, success screens, loading states), with transparent backgrounds or as SVG where needed. Use after generating assets with svgapp.ai.
---

# Add svgapp.ai assets to the app

These instructions use the svgapp.ai MCP server at `https://web.svgapp.ai/mcp`.

## 1. Make sure the asset is finished

Only download an asset after `get_generation_status` reports `completed`. Before that, the URL serves a placeholder. If you only have an older generation, `list_recent_generations` lists recent work with its status and URLs.

## 2. Pick the right format

- **On a colored or changing background** (cards, modals, dark mode): call `remove_image_background` with the `canvasId` from the completed generation, then wait for that new generation to complete. Skip this when the background is meant to fill the frame, such as a full-bleed hero image.
- **When the user wants SVG** (crisp scaling, editing in a design tool): call `vectorize_image` with the `canvasId` from the generation result, wait until it completes, then download the SVG. Each call starts a new billed vectorization, so call it once and poll.
- **Animations** are transparent WebM files and need no extra step.

## 3. Save into the project

Download the final asset with the client's tools and save it next to the project's other static assets, for example `public/mascot/` or `src/assets/mascot/`. Use descriptive file names that match the screen, such as `empty-projects.png`, `not-found.svg` or `loading.webm`. Prefer local files over remote URLs in production code, unless the user wants remote URLs.

## 4. Use it in the UI

- Follow the project's existing image and component conventions (framework image component, asset imports, Tailwind classes).
- Give each image meaningful `alt` text, or `alt=""` and `aria-hidden` when it is decorative next to text that already says the same.
- Set explicit width and height (or an aspect ratio) to avoid layout shift.
- Keep the mascot supporting the message: put the state text and the call to action next to it, not inside the image.
- For animations, see the `animate-mascot` skill, and show a still pose when `prefers-reduced-motion` is set.

## 5. Record what you added

Add the ids of reused mascots and poses to `svgapp.conf.json` if the project keeps them there, so later requests can reuse the same character.
