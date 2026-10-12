# Saving, exporting and publishing animations

These are separate operations. Never report one as another.

| Stage | Meaning | Evidence you need |
|---|---|---|
| Generated | Data or script exists in a file or session | The file path / tool result |
| Saved as editable project | Stored in MA2's project storage or in the place file | User confirmation or tool read-back |
| Exported | Converted to a Roblox-playable form (for example a KeyframeSequence in the place) | The instance exists (verified) |
| Published | Uploaded as an Animation asset owned by a user or group, with an asset ID | A real asset ID returned by Roblox |

## What to determine before exporting

1. Which export path the installed MA2 version actually offers (check its own UI/docs; do not assume).
2. Whether an editable project is kept, and where.
3. Whether the target is an in-place KeyframeSequence, a published asset, or both.
4. Whether Roblox Studio must be involved (it is for publishing in most workflows).
5. Ownership: published animations belong to a user or a group. The animation must be owned by the same account or group that owns the experience for it to play, unless the experience has permission to use it. Check this when an animation fails to play in a published game.
6. That the user has permission to publish under the chosen owner.
7. That the final asset ID was obtained and tested.

## Typical Studio-side path (documented Roblox workflow; verify in the user's Studio version)

- A `KeyframeSequence` in the place can be opened in Roblox's Animation Editor and published from there, which uploads it and gives an asset ID (`rbxassetid://<id>`).
- Roblox's Animation Editor can also import animations in some cases; confirm the current import options before telling the user to rely on one.
- Whether MA2 can export directly to the Animation Editor/publish flow, and in what form, depends on the version. Confirm before instructing.

## Open Cloud / API publishing

Roblox has Open Cloud APIs for assets, but support for animation uploads and the required permissions can change. Only use this path if you verified (documentation or a working tool) that it supports animations, and only with credentials the user provided for that purpose. Otherwise tell the user to publish through Studio.

## Integrating the asset

After publishing, put the real ID into the `Animation.AnimationId` (`rbxassetid://<id>`) used by the playback code. Test in a published or Studio-play session with the right owner. Report which of these you actually did.

## Never

- Fabricate or guess asset IDs. Use a labeled placeholder like `rbxassetid://REPLACE_ME` only when none exists.
- Say "uploaded" when something was only generated locally.
- Overwrite an existing published animation or project save without the user's go-ahead.
- Hide a step that needs the user's login or confirmation; name it and what they need to do.

## Handoff template

```
Generated: <file/path or instance>
Saved as editable: <yes/no/unknown, where>
Exported: <yes/no, form>
Published: <yes/no; asset ID if real>
You still need to: <steps>
```
