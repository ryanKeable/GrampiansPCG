---
id: "backlog-pcg-sample-unbuildable-2026-11-12"
status: "backlog"
priority: "medium"
assignee: null
epic: null
dueDate: null
created: "2026-11-12T00:00:00.000Z"
modified: "2026-11-12T00:00:00.000Z"
completedAt: null
labels: ["backlog", "wayfinder:task"]
order: "c1"
---
# PCG sample graphs the toolset cannot build yet

Raised from [Install Unreal PCG frameworks](m01-project-setup-2026-11-11--pcg-install.md) (Req 4). Per the "more simple examples, one use case each" principle recorded there, these plugin features still need a small sample graph with a `USE WHEN: ... SHOWS: ...` description, but `PCGToolset` could not build them in the 2026-11-12 session. Find a route for each, then build the sample.

## Items

1. **Mesh Partition nodes.** `Mesh Partition PCG Query / Write / Layer Write / Projection Spawner` are not in `ListNativeNodes` (even with `bCommonOnly=false`), so `AddNode` rejects them ("not in available nodes"). Working workaround: wrap plugin primitives with `AddSubgraphNode` (`Get_MeshTerrain` works). Open: do `Write_MeshTerrain`, `Extract_MeshTerrainPoints` and the `/PCGPrimitives_MeshPartitionInterop/Content/Examples/MeshTerrain/*` graphs wrap the same way, and does a Write sample change the terrain visibly? **Blocker found 2026-11-12:** the `MeshPartition` actors in `L_Smoke` have `megaMeshComponent = None`, i.e. no terrain exists, so `Get_MeshTerrain` returns nothing and `PCG_Smoke_MeshTerrain_Write` (Get -> Extract -> Transform Points -> Write, already built) warns "No Valid Input Data Found". Need a way to create terrain over MCP (MeshTerrainMode tools, or copy a terrain actor from `/PCGPrimitives_MeshPartitionInterop/Levels/L_MeshTerrain_Basic`), then re-run both graphs. Research done 2026-11-12: `PCGSpatialToolset` only has `RunPCGInstantGraph`, and `/PCGPrimitives/Instants` has no terrain-creation graph. `Skill_PCGMeshPartition` says nothing on creating terrain, and the plugin's `MeshTerrain_Hills` example assumes terrain already exists. In source, terrain sections come from `MeshPartitionRectangleGenerator::Generate` (MeshPartitionEditor module, plain C++, no `UFUNCTION`), so no MCP or Python route exists. Tracked as [Project MeshTerrain toolset and skill](backlog-meshterrain-toolset-2026-11-12.md). Remaining routes: (a) create the terrain once in the editor with Mesh Terrain mode; (b) a small editor-only C++ function in `Source/` that calls the generator and is exposed to MCP; (c) try copying the terrain from `L_MeshTerrain_Basic`. The existing `PCG_Smoke_MeshTerrain` read is therefore unproven.
2. **GeometryScript nodes.** `Mesh To Dynamic Mesh Element`, `Dynamic Mesh Transform`, `Boolean Operation`, `Spawn Dynamic Mesh` are also not addable via `AddNode`. Workaround: wrap the plugin's single-purpose samples under `/PCGGeometryScriptInterop/SampleContent/GeometryScriptNodes/Graphs/` (`BooleanOp` done; `Simplify`, `CombineActors`, `ChangeMaterials`, `DynMeshToStaticMesh` not yet). A wrapped graph spawns a component on the volume, not an actor, and exposes no data on `Out`, so `GetNodeDataView` cannot prove it; only a viewport capture does.
3. **PythonInterop.** No Python node found in the native node list and no `UPCG*Settings` header in `PCGPythonInterop/Source` (the plugin has only an Editor module). Unknown how to author or run a Python node via MCP. Needs research before a sample can be built.
4. **BiomeCore.** Not a node problem: the `BP_PCGBiomeCore` / `BP_PCGBiomeVolume` actors and BiomeDefinition / BiomeAsset / BiomeGenerator data assets must be duplicated from `/PCGBiomeSample/...` (never edited in place) and configured through `ObjectTools`/`DataAssetTools`. Follow `Skill_PCGBiomeCore` and `Skill_PCGBiomeCoreRefinement`. Not yet attempted; `L_Smoke` has no landscape, so use the skill's no-landscape path.
5. **Data view for surface/dynamic-mesh output.** `GetNodeDataView` gives no usable readback for surface (Mesh Terrain) or dynamic-mesh data. Decide a standard proof for those sample types: viewport capture, or an extra node that converts to points.

## Pass criteria

Each item ends with either a working small sample graph (generates with no warnings, has a `USE WHEN` description, evidence saved) or a recorded reason it cannot be built over MCP, with the manual editor steps instead.

## Related lessons

- `AddSubgraphNode` accepts graphs outside `ListAvailableSubgraphs`.
- Check every toolset before declaring a gap (see the claude-rules card).
