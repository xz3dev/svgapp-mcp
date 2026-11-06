---
name: animate-mascot
description: Animate an existing svgapp.ai mascot, optionally from one pose to another, and get a transparent WebM video for loading states, onboarding, success moments or empty states. Use when the user wants the mascot to move.
---

# Animate a mascot

These instructions use the svgapp.ai MCP server at `https://web.svgapp.ai/mcp`. If its tools are not available, tell the user how to connect it (see the repository README) and stop.

## 1. Find the mascot

Read `svgapp.conf.json` in the project root and use `activeMascotId`. If it is missing, call `list_mascots` and ask the user which mascot to animate, or follow the `create-mascot` skill first.

## 2. Plan the motion

Agree on what the animation is for, because that decides its shape:

- **Loops** (loading, idle, waiting): start and end on the same pose so the video repeats without a jump.
- **One-shot moments** (welcome wave, success celebration): start on a calm pose and end on the moment, or return to the calm pose.

The animation starts from the mascot's base image unless you pass `startMascotPoseId`, and ends on the same pose unless you pass `endMascotPoseId`. If the user needs specific start or end poses, create them first with `generate_mascot_pose` and wait until they are completed.

## 3. Create the animation

Call `create_mascot_animation` with the `mascotId`, the optional pose ids and a `prompt` that describes the movement only (what happens, how it feels). The look comes from the mascot. `duration`, `aspectRatio` and `resolution` are optional.

## 4. Wait for the video

Poll `get_generation_status` with the returned `generationId` until it is `completed`. A completed animation is a transparent WebM (`videoUrl` / `assetUrl`). Animations take longer than images; keep polling the same id instead of starting a new animation. Each new call is a new generation that counts against the workspace's plan.

## 5. Use it

Follow the `add-assets-to-app` skill to download the video into the project. For web, use a muted, looping, inline `<video>`:

```html
<video src="/mascot/loading.webm" autoplay loop muted playsinline aria-hidden="true"></video>
```

Respect `prefers-reduced-motion` by showing a still pose instead of the video when the user has reduced motion enabled.
