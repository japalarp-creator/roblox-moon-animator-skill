# Roblox Moon Animator 2 Animation Expert

A reusable [Claude Skill](https://docs.claude.com) that guides Claude through creating, editing, exporting and integrating animations for Roblox games, with a focus on Moon Animator 2, the Roblox Studio Animation Editor and Luau.

The goal is for Claude to do as much of the animation work as the environment genuinely allows (inspecting rigs, planning motion, generating animation data and Luau code, validating, preparing exports) instead of just explaining how to animate by hand, and to be strictly honest about what it did and did not do.

> **Not affiliated.** This is an independent community project. It is not affiliated with, endorsed by, or sponsored by Roblox Corporation, the author of Moon Animator 2, or Anthropic. See [Disclaimer](#disclaimer-and-trademarks).

## Key idea: honesty about capabilities

Claude cannot operate Roblox Studio or Moon Animator 2 by itself. The skill makes that the default assumption and requires Claude to verify what tools exist in each session before using them. It will not claim to have created keyframes, moved a rig, exported, uploaded or tested anything unless a tool result confirms it, and it never fabricates animation asset IDs.

## Features

- **Tool discovery first.** A checklist for finding out whether the session has file access, a shell, MCP servers, Studio automation, screenshots or publishing, and recording each as verified or absent.
- **Four creation methods**, used in order of what is verified available:
  - **A** direct Moon Animator 2 control (only if a verified tool exists)
  - **B** Roblox Studio automation (only through a verified tool)
  - **C** programmatic animation data and Luau (for example `KeyframeSequence` builders, playback modules)
  - **D** an assisted manual workflow with exact joint names, key pose tables and the smallest set of manual steps
- **Rig inspection** for R6, R15, custom Motor6D rigs, animal rigs and Bone rigs, including a printout script for the user.
- **Structured animation plan** (duration, loop, key poses, per-joint movement, interpolation, validation criteria; 30 FPS planning default, configurable).
- **Animation principles** (timing, spacing, anticipation, follow-through, arcs, weight, loops) plus guides for idle, walk, run, jump, eat, hatch and special animations.
- **Luau patterns**: a playback module using `Animator`, priorities, replication notes, a nested `KeyframeSequence` builder, and a procedural motion example clearly labeled as runtime-only.
- **Save vs. export vs. publish** kept separate, with ownership and permission checks and a handoff template.
- **Debugging guide** for animations that do not play, twist, pop on loops or get overridden.
- **Project file safety**: inspect first, back up, minimal reversible edits, never overwrite existing animations without permission.

## What it does not do

- It does not give Claude control of Moon Animator 2 or Roblox Studio. If your session has no tool that does, the skill falls back to generating plans, data and code and tells you exactly which steps are yours.
- It does not upload or publish animations on its own. Publishing needs your Roblox account and confirmation.
- Details of Moon Animator 2 behavior that vary by version (menus, import and export paths) are marked as *assumed* in the references and should be checked against your installed version and its current documentation.

## Requirements

- Claude with Skills support (Claude.ai, Claude Code or another environment that loads Agent Skills). Check the current documentation for your plan and product.
- Roblox Studio and the Moon Animator 2 plugin on your own machine for anything that must happen inside Studio.
- Optional: a tool that gives Claude real access to your project (file access, an MCP server for Studio, etc.). The skill adapts to whatever is actually present.

## Installation

### Claude Code

Copy the skill folder into a skills directory:

```bash
# personal (all projects)
cp -r roblox-moon-animator-2 ~/.claude/skills/

# or per project
mkdir -p .claude/skills && cp -r roblox-moon-animator-2 .claude/skills/
```

### Claude.ai (custom skills)

1. Zip the skill folder so that `roblox-moon-animator-2/` is the top-level directory inside the ZIP:
   ```bash
   zip -r roblox-moon-animator-2.zip roblox-moon-animator-2
   ```
2. Upload the ZIP in the Skills area of Claude's settings, if your plan supports custom skills. The exact menu names change, so check Anthropic's current help pages.

## Usage examples

The skill activates when you talk about Roblox animation. More prompts are in [`examples/EXAMPLE_PROMPTS.md`](examples/EXAMPLE_PROMPTS.md).

- "Here is my fox pet model (`Fox.rbxm`). Plan and generate a 2 second looping idle with breathing and a tail sway."
- "My walk animation slides the feet on the ground. Diagnose it and fix the timing."
- "Write a server-side Luau module that plays Eat and Idle animations on pets using `AnimationController`, with priorities and error handling."
- "I made the animation in Moon Animator 2 but it won't play in my published game. What should I check?"

A typical response follows this structure: Understanding, Environment inspection, Animation plan, Execution, Validation, Deliverables, Remaining actions.

## Repository layout

```
.
├── README.md
├── LICENSE
├── CHANGELOG.md
├── CONTRIBUTING.md
├── .gitignore
├── examples/
│   └── EXAMPLE_PROMPTS.md
└── roblox-moon-animator-2/          <- the skill (this folder is what you install)
    ├── SKILL.md
    └── references/
        ├── AUTOMATION_CAPABILITIES.md
        ├── ROBLOX_RIGGING.md
        ├── MOON_ANIMATOR_WORKFLOW.md
        ├── ANIMATION_PRINCIPLES.md
        ├── ROBLOX_ANIMATION_SCRIPTS.md
        ├── ANIMATION_EXPORT.md
        └── ANIMATION_DEBUGGING.md
```

`SKILL.md` is the entry point; it tells Claude when to read each reference file.

## Contributing

Issues and pull requests are welcome. Please read [CONTRIBUTING.md](CONTRIBUTING.md); the main rule is not to add claims about Moon Animator 2 or Roblox APIs that you have not verified.

## License

Released under the [MIT License](LICENSE). The license covers only the original text and code in this repository.

## Disclaimer and trademarks

Roblox, Roblox Studio and related names are trademarks of Roblox Corporation. Moon Animator 2 is a third-party plugin made by its own author. Claude is a trademark of Anthropic. All trademarks belong to their respective owners and are used only to describe compatibility. This repository contains no code, assets or documentation copied from those products, and the MIT license grants no rights in them.
