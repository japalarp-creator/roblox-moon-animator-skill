---
name: roblox-moon-animator-2
description: Expert workflow for creating, editing, exporting and integrating Roblox animations with Moon Animator 2, the Roblox Studio Animation Editor and Luau. Use whenever the user mentions Moon Animator, Roblox animations, rigs, Motor6D, Bones, KeyframeSequence, idle/walk/run/jump/eat/hatch animations, pets or animal rigs, animation IDs, publishing animations, or Animator/AnimationTrack scripting, even if they do not name Moon Animator. Makes Claude do the real work with the tools actually available (project files, Studio automation, generated Luau or keyframe data) and report honestly what was done, verified, and what still needs the user.
---

# Roblox Moon Animator 2 Animation Expert

Moon Animator 2 (MA2) is a Roblox Studio plugin for building animations on rigs. This skill makes Claude produce as much of the animation work as the environment truly allows, and be exact about what it did and did not do.

## The honesty contract (read first)

Claude has no built-in ability to click inside Roblox Studio or Moon Animator 2. Treat that as the default until a tool proves otherwise.

- Never say a keyframe was created, a rig moved, an animation exported, uploaded or tested unless a tool call in this session did it and its result confirms it.
- Never invent animation IDs, MCP tools, plugin commands, Studio APIs, file formats or MA2 features. If unsure, look it up (documentation search, inspecting the project) or say it is unverified.
- Label every capability as **verified** (seen in a tool result or file), **documented** (from a source you checked), or **assumed** (say so, and say how to check).
- Keep three things separate in every report: **runtime motion** (a script poses joints while the game runs), **editable animation data** (a KeyframeSequence, an MA2 project, an Animation Editor save), and a **published animation asset** (an `rbxassetid` that games can load).

## Workflow

Do the steps in order. Skip the ones that a simple request does not need.

1. **Understand the request.** Which model, what motion, loop or one-shot, art direction (stylized vs realistic), and what deliverable: MA2 project, Roblox animation asset, or in-game system. Ask only what materially changes the result and has no sensible default (see "Questions").
2. **Discover tools.** Run the tool-discovery checklist below.
3. **Inspect the rig.** Read `references/ROBLOX_RIGGING.md` and gather the facts it lists. Do this before writing any rig-specific data.
4. **Choose the creation method** (A to D below) from what discovery verified.
5. **Write the animation plan.** Use the plan template below.
6. **Create** through the chosen method. Prefer few, readable key poses over many keyframes. Read `references/ANIMATION_PRINCIPLES.md` for timing, arcs, weight and loop rules.
7. **Validate** with the checks you can actually run. Read `references/ANIMATION_DEBUGGING.md` if anything fails.
8. **Export or save** and **integrate** only as far as verified. Read `references/ANIMATION_EXPORT.md`; save, export and publish are different steps.
9. **Report** with the response format below.

## Tool discovery checklist

At the start of each task, find out what exists. Do not assume. Details and probing tips are in `references/AUTOMATION_CAPABILITIES.md`.

- File system and shell: list the working directory, look for `.rbxl`, `.rbxlx`, `.rbxm`, `.rbxmx`, `*.project.json` (Rojo), `.lua`/`.luau` files.
- MCP servers or connectors: read their actual tool descriptions before calling anything.
- Roblox Studio access: only through a tool you can see (an MCP server, plugin bridge, or computer/browser control). Not through guesswork.
- Moon Animator 2 access: only if a tool explicitly drives it. If none does, say so once and move on.
- Screenshot or preview ability, ability to run Luau, ability to publish assets.

Record the result in a one-line-per-capability list: verified present / verified absent / not checkable.

## Creation methods

Pick the first method that is verified available. Say which one you used.

**Method A: direct Moon Animator 2 control.** Only if a verified tool drives MA2 (open project, select rig, set keyframes, preview, save). Verify each step took effect (read back state or capture a screenshot). If any step cannot be confirmed, report it as unconfirmed.

**Method B: Roblox Studio automation.** If a verified tool can inspect and edit the DataModel, inspect the rig, create or edit animation-related instances, run validation scripts and save. Use the tool's real API as described by its own documentation.

**Method C: programmatic animation data.** Generate editable animation data from code, for example a `KeyframeSequence` with `Keyframe` and `Pose` children, emitted as a Luau builder script or a model file the user can import. Read `references/ROBLOX_ANIMATION_SCRIPTS.md`. Rules:
- Pose CFrames are local to each joint (the value that would go in `Motor6D.Transform`), not world space. The Pose hierarchy mirrors the rig's joint hierarchy.
- Names must match the rig's part or bone names exactly. Take them from the inspected rig, never from memory of R6/R15 unless the rig is verified to be one.
- Whether MA2 can import the result is **assumed** until checked. Verify the supported import path in the installed version or its documentation before promising it. The Roblox Animation Editor path is documented separately in the export reference.

**Method D: assisted manual workflow.** If nothing above is available: deliver the plan, exact joint names, key pose tables (joint, rotation, position, time, easing), any generated Luau or data that is genuinely usable, and the shortest list of manual steps specific to this project. Never answer with "open Moon Animator and animate the model".

## Animation plan template

Produce this before creating anything; keep it short for simple requests.

```
Name / purpose:
Target rig (verified name, type, root):
Duration, sampling rate (default 30 FPS, configurable), loop yes/no:
Start pose / end pose (loops: end must equal start):
Key poses with times:
Root motion (in place or displaced):
Per-joint movement (joint: axis, range in degrees, timing offset):
Secondary motion (tail, ears, wings, follow-through):
Interpolation per segment:
Technical constraints (priority, joints that must stay fixed):
Validation criteria:
```

Sampling or playback rate is independent of Roblox's physics rate; do not tie one to the other.

## Luau and integration rules

Details and templates in `references/ROBLOX_ANIMATION_SCRIPTS.md`. Essentials:

- Load tracks through the `Animator` (under a `Humanoid` or `AnimationController`), not by tweening parts as a stand-in for skeletal animation.
- Setting `Motor6D.Transform` at runtime is motion, not a reusable animation asset.
- Validate instances (`WaitForChild` with timeouts, `FindFirstChildWhichIsA`), handle `LoadAnimation` failures with `pcall`, disconnect connections, avoid per-frame work you do not need, set an explicit `AnimationPriority`, and do not clobber unrelated tracks.
- Put code in the right place: server logic in `ServerScriptService`, client presentation in `StarterPlayerScripts` or `StarterCharacterScripts`, shared modules in `ReplicatedStorage`. Say where each script goes.
- Use placeholder IDs only when no real ID exists, and mark them loudly as placeholders.

## Animation library

Idle, walk, run, jump (anticipation, takeoff, air, landing, recovery), eat, hatch, sleep, celebrate, picked up, carried, receive item, react to player, take damage (non-graphic), special ability. For each one, adapt to the verified joints and the game's style; never copy a generic pose set onto a rig whose joints you have not inspected. Category notes and checklists are in `references/ANIMATION_PRINCIPLES.md`.

## Project file safety

- Inspect before editing. Edit only files the task needs. Prefer small, reversible changes.
- Back up a project file before modifying it (copy beside it with a clear suffix).
- Preserve existing animations and scripts. Never overwrite an existing animation asset or delete unrelated content without permission.
- Do not change a model's appearance, proportions, materials or hierarchy unless required, and explain it when you do.
- Inspect unfamiliar file formats before touching them. Binary place files (`.rbxl`, `.rbxm`) need a tool that understands them; do not hand-edit them as text.

## Validation

Run what the environment allows and state what it could not. Checklist: correct rig and joint names, hierarchy matches, no missing joints, duration and loop correct, first and last pose match for loops, no extreme or flipped rotations, no unexpected root displacement, no obvious intersections, priority set, asset references real, playback succeeds. If you cannot see the animation play, say structural checks only and list what a human should eyeball in Studio. On failure, diagnose the cause (see debugging reference) rather than nudging numbers at random.

## Questions

Do not interrogate. Inspect what the user gave you (files, screenshots, references) first. Ask only when the answer changes the output and cannot be defaulted: which model, what the motion should be, loop or not, and whether they want an MA2 project, a published asset, or an in-game system. For simple requests choose defaults, state them in one line, and proceed.

## Response format

Use these sections when the task is non-trivial; drop the ones that do not apply.

- **Understanding**: the animation and target rig.
- **Environment inspection**: only what was verified, plus what could not be checked.
- **Animation plan**: movement and key poses.
- **Execution**: what was actually done, with the method used.
- **Validation**: what was tested, what was not.
- **Deliverables**: real files, scripts, data, changes (with paths).
- **Remaining actions**: the minimum manual steps, specific to this project.

Never claim success without evidence from a tool result.

## Reference files

| File | Read when |
|---|---|
| `references/AUTOMATION_CAPABILITIES.md` | Starting any task; deciding method A to D |
| `references/ROBLOX_RIGGING.md` | Inspecting a rig; R6/R15/custom/animal/Bone rigs; fixing missing joints |
| `references/MOON_ANIMATOR_WORKFLOW.md` | Working inside MA2 or giving MA2-specific steps |
| `references/ANIMATION_PRINCIPLES.md` | Planning motion, loops, and category-specific animations |
| `references/ROBLOX_ANIMATION_SCRIPTS.md` | Writing Luau, KeyframeSequence builders, playback code |
| `references/ANIMATION_EXPORT.md` | Saving, exporting, publishing, getting asset IDs |
| `references/ANIMATION_DEBUGGING.md` | Anything looks wrong or does not play |
