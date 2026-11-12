---
id: "m01-project-setup-2026-11-11--unreal-mcp-claude"
status: "in-progress"
priority: "medium"
assignee: "ryanKeable"
epic: "m01-project-setup-2026-11-11"
dueDate: null
created: "2026-11-11T00:00:00.000Z"
modified: "2026-11-11T06:00:00.000Z"
completedAt: null
labels: ["m01", "wayfinder:task"]
order: "a2"
---
# Integrate Unreal MCP with Claude

## Test & pass requirements

Server: Epic's built-in UE 5.8 `ModelContextProtocol` plugin (HTTP, default `http://127.0.0.1:8000/mcp`).

1. **Connects.** With the project's editor open, run `claude mcp list` from the repo root. Expected: `unreal` shows as connected and Claude can list its tools.
2. **Write action.** Via MCP, Claude spawns or edits an actor in the open level. Expected: the change is visible in the editor and undoable (Ctrl+Z).
3. **Reproducible.** Project-scoped `.mcp.json` is committed, plus setup notes (plugin names, port, "editor must be open") in `## Build notes`. Expected: a fresh checkout needs no extra steps beyond opening the editor.

## Build notes

- `.mcp.json` written at repo root. `ModelContextProtocol` and `MCPClientToolset` were already enabled in the `.uproject`.
- The plugin's `bAutoStartServer` defaults to **false**, so the server never listened (editor was open, port 8000 closed). Fixed by committing `Config/DefaultEditorPerProjectUserSettings.ini` with `bAutoStartServer=True`, `ServerPortNumber=8000`. Takes effect on editor start.
- Setup: plugins `ModelContextProtocol` + `MCPClientToolset` (in `.uproject`); port 8000; the editor must be open for `unreal` to connect.
- Req 1 verified: `claude mcp list` shows `unreal: http://127.0.0.1:8000/mcp - Connected`. Toolsets: `ToolsetRegistry.AgentSkillToolset`, `PCGToolset.PCGToolset`, `PCGToolset.PCGSpatialToolset`. There is no generic actor-spawn tool; the write path is PCG (`CreateGraph`, `SpawnGraphInstance`). The first call after connect can fail with ECONNRESET; retry.
- Req 2 write done: created `/Game/PCG/PCG_MCPSmokeTest` and spawned PCGVolume `MCPSmokeTestVolume` in the untitled level. Still needs a human check that the volume is visible and Ctrl+Z removes it. Delete the smoke-test graph afterwards.
- `Config/DefaultEditorPerProjectUserSettings.ini` also has a `[Terminal]` startup-command block (unrelated to MCP), and the `.uproject` has the `Terminal` plugin enabled.
