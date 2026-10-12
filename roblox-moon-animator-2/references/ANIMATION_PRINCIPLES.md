# Animation principles and category guides

Prefer simple, readable, high-quality motion over many keyframes. Every movement should have a purpose.

## Principles to apply

- **Timing and spacing**: timing is how long; spacing is how the motion is distributed (ease in/out gives weight). Heavier creatures move slower and settle slower.
- **Anticipation**: a small opposite move before a big one (crouch before a jump).
- **Follow-through and overlapping action**: tails, ears and loose parts keep moving after the body stops, and start slightly later than the body. Offset each segment of a chain by a few frames.
- **Arcs**: heads, paws and tail tips travel on curves, not straight lines.
- **Weight and balance**: center of mass stays over the support; shift weight before lifting a foot.
- **Secondary motion**: breathing, ear flicks, tail sway; subtle, never competing with the main action.
- **Silhouette**: the pose must read clearly in outline.
- **Consistency**: the same character should have the same personality across animations.
- **Exaggeration**: stylized games can push poses and timing; realistic animals must follow real anatomy and gait. Ask or infer the art direction.

## Avoid

Snapping between poses, accidental joint twisting, limbs through the body, floating or sliding feet on grounded motion, sudden root displacement, excessive head movement, mechanical (metronomic) tail motion, wrong weight distribution, changing proportions, broken loop seams, and interpolation that takes a joint the long way round (check rotations across the +/-180 degree boundary).

## Loop rules

- First and last pose identical (same joints, same values).
- Cyclic motion should have motion continuing through the seam, not stopping at it.
- Secondary parts can have a different period only if it still divides the loop length cleanly.
- Walk and run cycles: left and right half-cycles are offset by half the loop; contact poses mirror.

## Category guides

Adapt every one to the verified joints. Skip joints the rig does not have.

**Idle**: slow breathing (small body or chest scale-like motion via position/rotation), gentle weight shift, small head drift, tail and ear secondary motion, seamless loop, usually 2 to 4 seconds. Keep amplitudes low; idles are seen constantly.

**Walk**: alternating limb contacts, body bob (down on contact, up on passing), head stabilized relative to the world, tail sways opposite the hips, consistent cycle time that matches movement speed to avoid foot sliding.

**Run**: longer stride, shorter contact, more body lean and bob, faster cycle, stronger tail and head counter-motion, brief airborne phase for quadrupeds if the style calls for it.

**Jump**: anticipation (crouch), takeoff (extend), airborne pose (hold with slight drift), landing (compress), recovery (settle to idle). Separate looping air pose and landing if the game logic needs it.

**Eat**: head and neck lower, body stabilizes, chew rhythm with small head or jaw motion if a jaw joint exists (verify), pauses, return to idle.

**Hatch**: egg rocks with increasing intensity, brief pauses to build tension, crack or shell-open effect (only if shell pieces have joints or you handle it with effects/scripts), emergence, reveal pose, transition to idle. Often split across the egg rig and the creature rig.

**Special**: sleep (slow breathing, low posture, relaxed tail), celebrate (bounce, head up, tail wag), picked up (limp or startled reaction, limbs dangle), carried (held pose with slight sway), receive item (look, reach, react), react to player (head turn, ear perk), damage (brief flinch, non-graphic, quick recovery), special ability (anticipation, burst, follow-through).

## Quick quality checklist

Silhouette reads, weight feels right, motion has anticipation where needed, secondary parts lag the body, loop seam is clean, no pops, amplitudes appropriate for the style, duration fits gameplay use.
