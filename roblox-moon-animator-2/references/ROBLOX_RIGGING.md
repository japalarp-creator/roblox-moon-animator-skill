# Inspecting and understanding Roblox rigs

Inspect the target model before generating any rig-specific data. Never assume joints exist because separate parts exist.

## What to determine

1. Model name and where it lives (Workspace, ServerStorage, ReplicatedStorage, rbxm file).
2. Rig type: R6, R15, custom humanoid, custom non-humanoid (animal, creature, prop), Bone/skinned mesh, or unrigged parts.
3. Joint system: `Motor6D` (animation-capable), `Weld`/`WeldConstraint` (not animatable by the animation system), `Bone` instances (skinned meshes), or none.
4. Full hierarchy and, for each Motor6D, its `Part0` (parent side), `Part1` (child side), `C0` and `C1`.
5. Root: usually `HumanoidRootPart` for humanoids, or a chosen root part; for Bones, the root bone.
6. Animation host: `Humanoid` or `AnimationController`, and whether an `Animator` exists under it.
7. Existing animations: `Animation` objects, `KeyframeSequence`s, scripts that play tracks.
8. Gaps: parts that should move but have no joint; Motor6Ds missing; anchored parts; unusual `Part0/Part1` chains.
9. Suitability for the requested animation, and what correction is needed if not.

## How to inspect

- Through Studio automation (verified tool): walk descendants and print `ClassName`, name and, for Motor6D, `Part0.Name -> Part1.Name`.
- From project files: with Rojo-style projects, inspect `.model.json` / `.project.json` and scripts; with binary `.rbxm`/`.rbxl`, you need a tool that can read them. If none, ask the user to run the printout script below in Studio and paste the result.

Printout script for the user (runs in Studio command bar or a plugin):

```lua
local model = workspace:FindFirstChild("ModelName") -- change this
assert(model, "model not found")
for _, d in ipairs(model:GetDescendants()) do
	if d:IsA("Motor6D") then
		print("Motor6D", d:GetFullName(), (d.Part0 and d.Part0.Name) .. " -> " .. (d.Part1 and d.Part1.Name))
	elseif d:IsA("Bone") then
		print("Bone", d:GetFullName())
	elseif d:IsA("Humanoid") or d:IsA("AnimationController") or d:IsA("Animator") then
		print(d.ClassName, d:GetFullName())
	end
end
```

## Rig type notes

- **R6 / R15**: standard names exist, but still verify. R15 uses a deeper chain (`LowerTorso`, `UpperTorso`, limbs in segments); R6 uses `Torso`, `Left Arm`, etc. with joints named like `Neck`, `Left Shoulder`. Custom edits are common, so read the actual names.
- **Custom / animal rigs (Motor6D)**: a typical chain is root -> body -> neck -> head, body -> tail segments, body -> limbs. Name the chain you actually found. Joint pivot positions are in `C0`/`C1`; a bad pivot makes rotations swing around the wrong point.
- **Bone rigs**: animation happens on `Bone` transforms; confirm the animation host and that the mesh is skinned. Bone workflows differ from Motor6D, so verify what the installed MA2 version supports for bones.
- **Egg or prop rigs** (for hatching): often a single part or two shell halves. Check whether the pieces are joined by Motor6D; if not, they cannot be animated by the animation system until joints are added.

## Animal rig checklist

Root/body motion, neck and head, tail chain, front and rear limbs, wings, ears, horns, other appendages, body tilt and balance, foot placement, center of mass, and how limb motion relates to body motion. Note which of these the rig actually has and which it lacks; plan only with what exists.

## Fixing an unsuitable rig

Diagnose first, then propose the smallest fix: add missing `Motor6D`s with correct `Part0/Part1/C0/C1`, set a proper root, add `AnimationController` + `Animator`, unanchor moving parts. Keep the visual result unchanged: do not resize, recolor or restructure beyond what the joint fix needs, and tell the user exactly what changed. Back up before editing.

## Coordinate-space reminder

Pose CFrames and `Motor6D.Transform` are relative to the joint (they are applied on top of `C0 * C1:Inverse()`), so they are local to each joint, not world space and not shared across joints. Rotating a parent changes where all its children end up.
