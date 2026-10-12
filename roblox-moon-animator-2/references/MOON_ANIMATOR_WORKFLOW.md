# Moon Animator 2 workflow

Moon Animator 2 (MA2) is a community-made Roblox Studio plugin for animating rigs and other instances on a timeline. Details vary by version, so treat anything below marked *assumed* as something to confirm in the installed version or in its current documentation before you tell the user to rely on it.

## Concepts (stable across animation tools)

- **Timeline**: time axis with a duration; playback speed and looping are separate settings.
- **Tracks**: one per animated joint or instance.
- **Keyframes**: stored values (position, rotation, sometimes other properties) at a time, with an interpolation (easing) for the segment that follows.
- **Pose editing**: moving or rotating joints in the viewport to define a keyframe.
- **Interpolation / easing**: linear, ease in/out and other curves; the choice shapes how motion feels.
- **Looping**: for loops, the last pose must match the first so the seam is invisible.
- **Export**: turning the animation into something Roblox can play (see `ANIMATION_EXPORT.md`).

## Typical working order inside MA2 (*assumed; confirm in the installed version*)

1. Open the place, select the rig, and load or create an animation in MA2.
2. Set duration and, if the tool offers it, frame rate.
3. Block key poses first (start, extremes, end), then add breakdowns and secondary motion.
4. Set easing per segment.
5. Preview, adjust, check the loop seam.
6. Save the MA2 project. Export or publish only after the user approves the result.

## What Claude can and cannot do here

- Without a verified tool that drives MA2, Claude cannot create MA2 keyframes. State that plainly.
- Claude can still deliver: the structured plan, key pose tables with exact joint names and rotation/position values, interpolation choices, a Luau runtime preview script, and generated animation data if its import path is verified.
- Do not tell the user a specific MA2 menu, button or shortcut exists unless you verified it or are quoting current documentation. Say "in your version, look for the equivalent of X" when unsure.

## Writing manual steps (Method D)

Be specific to the project. Example of the right level:

```
Rig: Workspace.Fox (Motor6D rig). Joints: Root, Body, Neck, Head, Tail1-3, FL/FR/BL/BR legs.
1. Select Workspace.Fox in MA2 and create an animation named Fox_Idle, length 2.0 s, looping.
2. Key 0.0 s: neutral pose (all Transforms identity).
3. Key 1.0 s: Body Y position +0.05 studs, Neck rotation X +3 degrees, Tail1 rotation Y +6 degrees, Tail2 +9, Tail3 +12 (each delayed 0.05 s).
4. Key 2.0 s: copy 0.0 s. Ease: sine in/out on every segment.
5. Preview looped; check the seam at 2.0 s.
```

Replace the numbers with values derived from the real rig and the plan. Never hand over a vague "animate the model".

## Version and permission checks

Ask the user (or inspect) which MA2 version is installed when export or import behavior matters. Note that animations are tied to an owner (user or group) when published; see the export reference.
