# Debugging animations

Diagnose the cause, then fix it. Do not change numbers at random.

## Triage order

1. **Does it play at all?** Check the `AnimationTrack` after `LoadAnimation` (`pcall`), the `Animator` exists under the right host, `Animation.AnimationId` is a real, owned, accessible asset, and `Play` is called on the right side (server vs client).
2. **Wrong joints move or nothing moves?** Names in the animation do not match the rig, or the rig has no `Motor6D` for that part, or parts are anchored, or another script/animation controls those joints.
3. **Joints in the wrong place or swinging around the wrong pivot?** Pose values used world-space instead of joint-local transforms, or `C0`/`C1` pivots are off.
4. **Seam pop on loops?** First and last keyframes differ, or easing at the seam stops motion.
5. **Weird twists or spins?** Rotation interpolating the long way across the +/-180 degree boundary, or gimbal-like axis ordering; add an intermediate key or change axis order.
6. **Floating or sliding feet?** Root speed does not match stride length; adjust animation speed or the movement speed, not just the legs.
7. **Parts intersecting?** Amplitude too large for the geometry; reduce rotation, shift the root, or split movement between joints.
8. **Overridden by something else?** Priority too low, a default character animation scripts overriding it, or another track with higher priority on the same joints.
9. **Works in Studio but not in game?** Ownership/permission mismatch (user vs group), unpublished asset, or client/server authority issue.
10. **Jitter?** Two systems writing the same joint each frame, or a replication fight between client and server.

## Useful checks (when you can run code)

```lua
-- list tracks and states
for _, track in ipairs(animator:GetPlayingAnimationTracks()) do
	print(track.Name, track.Animation and track.Animation.AnimationId, track.Priority, track.Length, track.IsPlaying)
end
```

```lua
-- verify Motor6Ds expected by the animation exist
for _, name in ipairs({ "Root", "Neck", "Head" }) do -- replace with real names
	local ok = model:FindFirstChild(name, true)
	print(name, ok and ok.ClassName or "MISSING")
end
```

## Reporting

State what you tested, what failed, the diagnosed cause, the fix, and what you could not test (for example "could not view playback; structural checks only"). Do not report a fix as confirmed until you re-ran the check.
