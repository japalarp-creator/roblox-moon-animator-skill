# Automation capabilities and tool discovery

Goal: decide honestly which creation method (A to D) is available, then act on it.

## Default assumption

Claude cannot operate Roblox Studio or Moon Animator 2 on its own. A capability exists only if a tool in this session provides it and a result confirms it.

## Discovery procedure

Run these probes at the start of each task. Keep a table of results.

| Capability | How to check | Status values |
|---|---|---|
| File system | List the working directory and any paths the user mentioned | verified / absent |
| Shell | Run a harmless command (`echo`, `ls`) | verified / absent |
| Project files | Look for `.rbxl`, `.rbxlx`, `.rbxm`, `.rbxmx`, `*.project.json`, `src/`, `.lua`, `.luau` | found / none |
| Luau tooling | Check for `luau`, `lune`, `rojo`, `selene`, `stylua` on PATH (only if shell exists) | present / absent |
| MCP / connectors | List the tools actually exposed this session and read each description | list them |
| Studio access | Only via a visible tool (MCP bridge, plugin bridge, computer or browser control) | verified / absent |
| Moon Animator 2 access | Only via a tool that explicitly drives MA2 | verified / absent |
| Screenshots / preview | A tool that captures Studio or a viewport | verified / absent |
| Publishing | A tool or authenticated API that uploads assets | verified / absent |

## Rules

- Read an MCP server's real tool names, parameters and descriptions before first use. Do not guess names.
- If a tool errors, report the error accurately and choose the next-best method. Do not retry blindly or pretend it worked.
- Computer or browser control may exist in some sessions. If it does, treat it as a way to see and operate the user's real Studio window, with the same verify-after-every-step rule. If it does not, say so.
- Never run a command or call an API you have not confirmed exists. No made-up CLI flags, endpoints or file formats.
- Uploading or publishing needs the user's authorization. Never use credentials the user did not provide for this purpose.

## Choosing the method

1. Verified tool drives MA2 -> **Method A**.
2. Verified tool edits Studio's DataModel -> **Method B**.
3. Shell or file system exists (or you can emit code) -> **Method C**: generate KeyframeSequence builders, Luau runtime code, or other data you have confirmed is importable.
4. Otherwise -> **Method D**: plan, exact key pose tables, minimal manual steps.

Many sessions end up at C plus D: Claude generates data and scripts, the user performs the Studio-side import, preview and publish. That is a legitimate outcome; just state the split precisely.

## Reporting template

```
Verified: <capability list>
Not available: <capability list>
Method used: <A/B/C/D> because <reason>
Done by Claude: <items with evidence>
Needs you: <minimal specific steps>
```
