# Example prompts

Use these as starting points. Mention your model and what tools Claude can use (files, MCP server, etc.) so it can pick the right method.

## Planning and generating

- "My pet is a Motor6D fox rig called `Fox` (joints: Root, Body, Neck, Head, Tail1 to Tail3, four legs). Plan a 2 second looping idle with subtle breathing and tail sway, then generate the data and Luau to build it."
- "Create a walk cycle plan for a four-legged pet at 30 FPS. List key poses with joint rotations and timing."
- "Plan a hatching sequence for an egg that wobbles with increasing intensity, cracks open, and reveals the pet. The egg is a single MeshPart."

## Scripting

- "Write a ModuleScript that loads and plays Idle, Walk and Eat tracks for NPC pets using an `AnimationController`. Use proper priorities, `pcall` around loading, and tell me where each script goes."
- "My pets should play animations for all players. Show the server and client split, including a RemoteEvent for an Eat request."

## Diagnosing

- "The animation plays in Studio but not in my published game. Walk me through the likely causes."
- "My loop pops at the end. Here are the first and last keyframes; what's wrong?"
- "Feet slide during the walk. How do I match animation speed to movement speed?"

## Export and publishing

- "Explain the difference between saving, exporting and publishing an animation, and tell me what I need to do to get a working asset ID."

## Rig help

- "Here is the printout of my rig's Motor6Ds. Is it suitable for a jump animation, and what is missing?"
