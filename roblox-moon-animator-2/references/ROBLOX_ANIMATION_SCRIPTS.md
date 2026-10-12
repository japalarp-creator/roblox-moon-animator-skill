# Luau for animation: patterns and templates

Check current Roblox documentation if you are unsure an API still behaves as described. Runtime motion, editable animation data and published assets are different things.

## Which tool for which job

| Need | Use |
|---|---|
| Play a saved animation asset | `Animator:LoadAnimation(animation)` then `AnimationTrack:Play()` |
| Humanoid characters | `Humanoid` -> `Animator` |
| Non-humanoid rigs (animals, props) | `AnimationController` -> `Animator` |
| Editable animation data | `KeyframeSequence` with `Keyframe` and `Pose` children |
| Quick in-Studio preview of a KeyframeSequence | `KeyframeSequenceProvider:RegisterKeyframeSequence` (verify availability and context) |
| Procedural motion (look-at, spring tails) | Runtime code on `Motor6D.Transform`/bones; this is not a reusable animation asset |
| Moving whole models, UI, props | `TweenService` is fine here; it is not a replacement for skeletal animation |

## Playback module (server or client, depending on who owns the character)

```lua
local AnimationPlayer = {}
AnimationPlayer.__index = AnimationPlayer

function AnimationPlayer.new(host: Instance) -- Humanoid or AnimationController
	local animator = host:FindFirstChildWhichIsA("Animator")
	if not animator then
		animator = Instance.new("Animator")
		animator.Parent = host
	end
	return setmetatable({ _animator = animator, _tracks = {} }, AnimationPlayer)
end

function AnimationPlayer:load(name: string, assetId: string, priority: Enum.AnimationPriority?, looped: boolean?)
	local anim = Instance.new("Animation")
	anim.AnimationId = assetId -- e.g. "rbxassetid://<real id>"; never invent IDs
	local ok, trackOrErr = pcall(function()
		return self._animator:LoadAnimation(anim)
	end)
	if not ok then
		warn(("Failed to load animation %s: %s"):format(name, tostring(trackOrErr)))
		return nil
	end
	local track = trackOrErr
	track.Priority = priority or Enum.AnimationPriority.Action
	track.Looped = looped == true
	self._tracks[name] = track
	return track
end

function AnimationPlayer:play(name: string, fade: number?, weight: number?, speed: number?)
	local track = self._tracks[name]
	if track then
		track:Play(fade or 0.1, weight or 1, speed or 1)
	end
end

function AnimationPlayer:stop(name: string, fade: number?)
	local track = self._tracks[name]
	if track then
		track:Stop(fade or 0.1)
	end
end

function AnimationPlayer:destroy()
	for _, track in pairs(self._tracks) do
		track:Stop(0)
		track:Destroy()
	end
	table.clear(self._tracks)
end

return AnimationPlayer
```

Place it as a `ModuleScript` in `ReplicatedStorage` if both sides use it.

## Priorities

`Core < Idle < Movement < Action < Action2 < Action3 < Action4` (higher overrides lower on the same joints). Idle/walk/run usually `Idle`/`Movement`; one-shots like eat or jump `Action`; special abilities `Action2` or higher.

## Replication notes

- Animations played by the owner client on its own character replicate to others. For server-controlled NPCs and pets, load and play tracks on the server so all clients see them.
- Do not put server-only logic in `LocalScript`s, and do not put per-client presentation (camera, local effects) in server scripts.
- If clients trigger a pet animation, send a `RemoteEvent` to the server, validate on the server, then play.

## Building a KeyframeSequence from data (Method C)

Use part names read from the inspected rig. Each `Pose` is named after the child part of a joint (`Motor6D.Part1`), and poses are nested to mirror the rig's joint hierarchy (for example `HumanoidRootPart > Body > Neck > Head`). The `tree` below must be generated from the real Motor6D chain you inspected, not typed from memory of R6/R15.

```lua
type Node = { name: string, children: { Node }? }
type Key = {
	time: number,
	poses: { [string]: CFrame }, -- only the joints that move; others get an identity pose
	easingStyle: Enum.PoseEasingStyle?,
	easingDirection: Enum.PoseEasingDirection?,
}

local function addPoses(parent: Instance, node: Node, key: Key)
	local pose = Instance.new("Pose")
	pose.Name = node.name
	pose.CFrame = key.poses[node.name] or CFrame.new()
	pose.EasingStyle = key.easingStyle or Enum.PoseEasingStyle.Linear
	pose.EasingDirection = key.easingDirection or Enum.PoseEasingDirection.InOut
	pose.Parent = parent
	for _, child in ipairs(node.children or {}) do
		addPoses(pose, child, key)
	end
end

local function buildSequence(name: string, tree: Node, keys: { Key }, looped: boolean, priority: Enum.AnimationPriority?)
	local seq = Instance.new("KeyframeSequence")
	seq.Name = name
	seq.Loop = looped
	seq.Priority = priority or Enum.AnimationPriority.Idle

	for _, key in ipairs(keys) do
		local keyframe = Instance.new("Keyframe")
		keyframe.Time = key.time
		addPoses(keyframe, tree, key)
		keyframe.Parent = seq
	end
	return seq
end
```

Every keyframe gets a complete pose chain (identity `CFrame.new()` for unmoved joints), so the hierarchy is never partial. For loops, make the last key's poses equal the first key's. Verify in Studio that the sequence opens and plays on the target rig before calling it done.

Pose `CFrame`s are local joint transforms (what would go in `Motor6D.Transform`). Build rotations with `CFrame.Angles(math.rad(x), math.rad(y), math.rad(z))` and small positions in studs. Keep first and last keyframes equal for loops.

A script that builds a `KeyframeSequence` in the open place creates editable animation data in that session only. It is not published until the user saves/publishes it through Studio (see `ANIMATION_EXPORT.md`).

## Procedural motion example (runtime, not an asset)

```lua
local RunService = game:GetService("RunService")

local function swayTail(tailJoints: { Motor6D }, amplitudeDeg: number, speed: number)
	local t0 = os.clock()
	local conn
	conn = RunService.Heartbeat:Connect(function()
		if not tailJoints[1] or not tailJoints[1].Parent then
			conn:Disconnect()
			return
		end
		local t = os.clock() - t0
		for i, joint in ipairs(tailJoints) do
			local phase = t * speed - i * 0.35 -- each segment lags the previous
			joint.Transform = CFrame.Angles(0, math.rad(math.sin(phase) * amplitudeDeg * i * 0.5), 0)
		end
	end)
	return conn
end
```

Disconnect the returned connection when the model is destroyed. Note this competes with any animation track on the same joints; choose one owner per joint, or apply it at a lower priority stage as appropriate.

## Script hygiene checklist

Correct `game:GetService` calls, validate required instances, graceful handling of missing assets, no needless `while true` loops (use events or `Heartbeat` only when needed), clean up connections and tracks, explicit priorities, no overwriting unrelated tracks, real asset IDs or clearly marked placeholders, correct script placement stated for each file.
