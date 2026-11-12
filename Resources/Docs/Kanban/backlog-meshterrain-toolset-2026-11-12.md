---
id: "backlog-meshterrain-toolset-2026-11-12"
status: "backlog"
priority: "medium"
assignee: null
epic: null
dueDate: null
created: "2026-11-12T00:00:00.000Z"
modified: "2026-11-12T00:00:00.000Z"
completedAt: null
labels: ["backlog", "wayfinder:task"]
order: "c2"
---
# Project MeshTerrain toolset and skill

Raised from [Install Unreal PCG frameworks](m01-project-setup-2026-11-11--pcg-install.md) (Req 4). Claude cannot create a Mesh Terrain over MCP, which blocks the two Mesh Terrain sample graphs (`PCG_Smoke_MeshTerrain`, `PCG_Smoke_MeshTerrain_Write`) and, later, M03 onward. See item 1 in [PCG sample graphs the toolset cannot build yet](backlog-pcg-sample-unbuildable-2026-11-12.md).

## Why a new toolset

- `AMeshPartition` spawns with `MegaMeshComponent = None`, so there is no terrain to read or write.
- Terrain sections come from `MeshPartitionRectangleGenerator::Generate` (MeshPartitionEditor module, plain C++, no `UFUNCTION`). No existing MCP toolset, Python, instant graph or PCG node calls it.

## Scope

1. **Toolset** (editor-only C++ in `Source/`, built with the `create-toolset` skill): one tool to create a terrain on a MeshPartition actor, taking size, section layout and optional base height. Possible second tool: report whether an actor has terrain. Prove it by running the two Mesh Terrain samples against the result.
2. **Agent Skill** (built with the `unreal-skill` skill): how to create a terrain, check it exists (`megaMeshComponent` is not None), choose a `MeshPartitionDefinition`, and verify a read or write. Include the `Skill_PCGMeshPartition` facts it builds on.
3. Add the toolset-check lesson (see the claude-rules card) so the next gap is looked for in every toolset before declaring it missing.

## Pass criteria

- A tool call creates a terrain in `L_Smoke`; `Get_MeshTerrain` then returns data and `PCG_Smoke_MeshTerrain_Write` runs with no warnings and visibly raises the terrain.
- The skill is listed by `AgentSkillToolset.ListSkills` and an agent following it alone can repeat the steps.

## Cheaper stopgap

Create the terrain once by hand in Mesh Terrain mode. Does not remove the need for the toolset (M03 will want it repeatable).
