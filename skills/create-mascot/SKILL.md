---
name: create-mascot
description: Create a reusable svgapp.ai mascot for an app, website or brand and generate consistent poses for specific screens (onboarding, empty states, errors, success). Use when the user wants a mascot, a brand character, or the same character in a new pose.
---

# Create a mascot and its poses

These instructions use the svgapp.ai MCP server at `https://web.svgapp.ai/mcp`. If its tools are not available, tell the user how to connect it (see the repository README) and stop. Never ask the user to paste a password or token into the chat; sign-in happens in the client's OAuth flow.

## 1. Project config

Look for `svgapp.conf.json` in the project root.

- If it exists and has an `artStyleId`, read it and reuse `artStyleId`, `colorPaletteId` and `activeMascotId`.
- If it is missing or invalid, call `init_svgapp_config` and save the returned config to `svgapp.conf.json`. Merge into an existing file and keep unknown fields.
- If the user wants a different look, call `list_art_styles` and let them pick.

If the assets should match the product's colors and there is no `colorPaletteId` yet, read the colors from the codebase (Tailwind config, CSS variables, theme files) and call `create_color_palette` with a `name` and the `backgroundColor`, `primaryColor` and `accentColor` (plus optional `customColors`). Merge the returned `configPatch` into `svgapp.conf.json`.

If the user belongs to several workspaces, `list_workspaces` lists them. Every tool accepts an optional `workspace`; without it the personal workspace is used.

## 2. Reuse before creating

If `activeMascotId` is set, or `list_mascots` finds a mascot that fits, use it. Only create a new mascot when the user wants one or none fits.

## 3. Create the mascot

Call `create_mascot` with:
- `description`: species or object, personality, product domain and any brand traits. Describe the character, not the scene.
- `artStyleId` and `colorPaletteId` from the config.
- `aspectRatio` `1:1` for a reusable base image.

Save the returned mascot id as `activeMascotId` in `svgapp.conf.json`.

## 4. Generate poses

Call `generate_mascot_pose` with the `mascotId`, a short `name` for the pose (for example "empty search") and a `prompt` that describes one moment: the action, the emotion, the UI context and any object the mascot holds. Example prompt: "holding a magnifying glass over an empty search results panel, curious but upbeat". Generate one pose per screen or state the user needs.

## 5. Wait for results

Generation is asynchronous. Every creation tool returns a `generationId` and an asset URL right away; the URL shows a placeholder until the image is ready.

- Poll `get_generation_status` with the same `generationId` until it is `completed`. Do not start the generation again because it is still pending.
- If you lost an id, `list_recent_generations` lists recent work.
- Each call to a generation tool starts a new generation that counts against the workspace's plan. Do not retry a failed or slow generation without the user's approval.

Report the finished asset URLs and ids. To use the images in the project, follow the `add-assets-to-app` skill.

## Errors

Describe failures as they are. If a plan or allowance limit is hit, the error names the workspace settings page; pass that on to the user instead of retrying.
