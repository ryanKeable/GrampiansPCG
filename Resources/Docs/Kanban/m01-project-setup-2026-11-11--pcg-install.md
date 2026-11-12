---
id: "m01-project-setup-2026-11-11--pcg-install"
status: "ready-to-test"
priority: "medium"
assignee: null
epic: "m01-project-setup-2026-11-11"
dueDate: null
created: "2026-11-11T00:00:00.000Z"
modified: "2026-11-12T02:25:50.736Z"
completedAt: null
labels: ["m01", "wayfinder:task"]
order: "a2V"
---
# Install Unreal PCG frameworks

Get core PCG and any other relevant or critical PCG plugins enabled in the project and proven working, so M02+ can build on them. The required plugin list is not fixed up front: it is decided during this card (research, then a short grill with the user), starting from core `PCG` plus the six already in the `.uproject` (PCGBiomeCore, PCGBiomeSample, PCGPrimitives, PCGPrimitives_MeshPartitionInterop, PCGToolset, PCGPythonInterop).

## Test & pass requirements

"Required plugin" below means each plugin in the final list from requirement 1.

1. **Plugin list decided.** Check: Build notes contain the final required-plugin list with a one-line reason per plugin, agreed with the user (grilled in-card). Expected: list present and includes core `PCG`; plugins excluded or deferred are named with why.
2. **Plugins enabled.** Check: every required plugin is `"Enabled": true` in `PGenAusLandscape.uproject` and shows enabled in Editor > Edit > Plugins. Expected: all match; no required plugin missing or disabled.
3. **Loads clean.** Check: open the project in the editor and read the output log and Message Log. Expected: no plugin load, missing-module or compile errors attributable to a required plugin.
4. **Separate sample graph per plugin.** Check: `/Content/Samples/PCGSmokeTest` holds one minimal level + PCG graph per required plugin, each exercising that plugin's own feature; open each and generate. Expected: each graph generates its output (points, spawned meshes, etc.) with no errors or warnings from the graph. Evidence (screenshot of the graph and result) is saved for each so M02+ can reference it.
5. **Reachable via Unreal MCP.** Check: through the Unreal MCP, Claude lists the PCG toolset and lists or reads one sample graph for each required plugin. Expected: calls succeed and return the real graph, matching what the editor shows.
6. **Samples committed.** Check: `git status` after the build. Expected: `/Content/Samples/PCGSmokeTest` and the `.uproject` change are committed (by the orchestrator), nothing else stray.

7. **Stage 0 runtime spike.** Check: package a Shipping build that creates Mesh Terrain at launch from a PCG graph. Expected: terrain appears at launch, or the card records that it cannot (then Plan.md falls back to Option A and is revised).
8. **Seed determinism.** Check: generate the same Seed twice and compare heights. Expected: identical, or the result is recorded as an open question in Plan.md.

## Re-check against Plan.md

From `Resources/Docs/Plan.md` (Mesh Terrain driven by PCG, plus PCG scatter):
- The required-plugin list must also consider Mesh Terrain / Mesh Partition itself, not only the interop plugin. Confirm core `PCG` and read all six `.uplugin` files (Plan Stage 0).
- PCGPythonInterop is editor-only (research, unverified locally): list it as authoring-only, since it cannot run in a Shipping build. PCGToolset's editor-only status is unverified.
- **Decided: this card absorbs Plan Stage 0.** Requirements 7 and 8 above.
## Build notes (2026-11-12)

Engine: `C:\Program Files\UE_5.8` (5.8.4). `.uplugin` files read directly; all verified from source, not docs.

### Required plugin list (agreed with user)
| Plugin | Reason | Scope |
|---|---|---|
| PCG | core framework | runtime + editor |
| GeometryScripting | dependency of PCGGeometryScriptInterop / MeshPartition | runtime |
| PCGGeometryScriptInterop | geometry-script nodes in PCG; dependency of Biome/MeshPartition interop | runtime |
| MeshPartition | Mesh Terrain core (Plan.md) | runtime |
| MeshTerrainMode | Mesh Terrain editor tools | editor-only |
| PCGMeshPartitionInterop | PCG drives Mesh Terrain | runtime + editor |
| PCGBiomeCore | data-driven biomes | runtime |
| PCGBiomeSample | Biome Core showcase content (no modules) | sample content, do not ship |
| PCGPrimitives | primitives/examples library (no modules; content-only) | content |
| PCGPrimitives_MeshPartitionInterop | primitives for Mesh Partition (no modules; content-only) | content |
| PCGToolset | MCP/assistant graph authoring | **authoring-only** (Editor module only) |
| PCGPythonInterop | Python nodes in PCG | **authoring-only** (Editor module only) |

Deferred (not needed by Plan.md yet): PCGFastGeoInterop (adds FastGeoStreaming; revisit if Stage 0 needs runtime spawning), PCGWaterInterop / MeshPartitionWater (forum: water does not work with Mesh Terrain), PCGNiagaraInterop, PCGInstancedActorsInterop, PCGNaniteAssembliesInterop.

### Status
- Req 1: done. Req 2: `.uproject` now lists all required plugins explicitly (PCG, GeometryScripting, PCGGeometryScriptInterop, MeshPartition, MeshTerrainMode, PCGMeshPartitionInterop added). Editor > Plugins UI check not done.
- Req 3: done. Saved/Logs shows all plugins mounted and modules loaded; no errors attributable to them.
- Req 4: 1 of 12 done. `/Game/Samples/PCGSmokeTest/PCG_Smoke_Core` (grid -> transform) generates 100 points, no messages. No level saved yet (volume is in an unsaved Untitled level); no screenshots.
- Req 5: partly. `PCGToolset` lists, builds, executes and reads node data via MCP; the Core graph was read back. Other graphs not yet built.
- Req 4 update (2026-11-12): 2 of 12. Added `PCG_Smoke_Primitives` (Create Points Grid -> `Transform_Jitter` primitive; description set; not yet generated, no level). User decision: per the engine's "dynamic examples" guidance (static templates vs. small readable graphs the agent can find and read), keep each sample tiny and give it a graph description so an agent can discover it via `GetGraphDescription`/`GetGraphStructure`.
- **Autopsy note (process miss, for the closing autopsy and future recommendations):** I wrongly told the user "no MCP tool creates or saves a level, so you must do it manually". I had only read `SceneTools` and skipped `AssetTools` / `EditorAppToolset`. The user challenged it and it was doable over MCP: the Untitled level (`/Temp/Untitled_1`) was moved with `AssetTools.move` to `/Game/Samples/PCGSmokeTest/L_Smoke`, then `AssetTools.save_assets` saved it and it became the current level. (`AssetTools.duplicate` on `/Game/Sample` also works but `SceneTools.load_level` refuses while the Untitled level is dirty, and the copy may carry stray actors, so I deleted that copy.) **Recommendation:** before telling the user a step needs them, `describe_toolset` every toolset that could plausibly cover it (Asset, EditorApp, Object, Programmatic), not just the obvious one; treat levels as assets (move/duplicate/save_assets); `PCGToolset.SpawnGraphInstance` places volumes into the current level. Add this to the pgen-unreal skill.
- **Level surprise (2026-11-12):** the moved Untitled level was not empty: `L_Smoke` came up as a copy of the Sample level (Landscape + ~64 proxies, ~60 HLOD actors, `Actor_UAID`, minimap). User chose "strip to clean level"; 132 actors removed in one `ProgrammaticToolset.execute_tool_script` call (regex over `find_actors` + `remove_from_scene`). Kept lights/sky/fog/PlayerStart. Lesson: after creating a level, list its actors before trusting it is empty. Also: `ProgrammaticToolset` batches MCP calls, use it for bulk edits instead of 100+ calls.
- Req 4 update: 3 of 12. `PCG_Smoke_GeometryScript` wraps plugin template `TPL_Geometry_CreateMesh` (`SaveAsset=false`) via `AddSubgraphNode`; this accepts graphs not in `ListAvailableSubgraphs`. Generates with no messages; viewport capture shows a dynamic mesh in `Vol_GeometryScript` (`Resources/Docs/Evidence/pcg-smoke/geometryscript.jpg`, 480px, downscaled because a hook blocks large images). It spawns a component on the volume, not a separate actor, so `find_actors` shows nothing. `PCG_Smoke_Primitives` generated 100 points.
- Req 4 update: 4 of 12 graphs, covering 6 of 12 plugins (the Mesh Partition graph stands in for MeshPartition, PCGMeshPartitionInterop and PCGPrimitives_MeshPartitionInterop together; GeometryScript graph covers GeometryScripting + PCGGeometryScriptInterop). `PCG_Smoke_MeshTerrain` = `Get_MeshTerrain` primitive reading actor `MeshTerrain_Smoke` (`/Script/MeshPartition.MeshPartition`, at Y=18000) via `Vol_MeshTerrain`. Executes with no messages, but `GetNodeDataView` returns only `{"data_version":0}`: **weak proof**, a surface data object exists but I could not confirm it holds terrain geometry (the MeshPartition actor has had no terrain built/definition check yet). Needs a visual capture or a Write_MeshTerrain step to prove.
- **Design principle (user, 2026-11-12, stated twice):** the more simple examples showing different contexts/use cases Claude can refer to, the better. So each sample is tiny, covers exactly one use case, and its graph description starts `USE WHEN: ... SHOWS: ...` so an agent can find it by intent via `GetGraphDescription`. Prefer wrapping a plugin's own single-purpose sample over a big template: `PCG_Smoke_GeometryScript` now wraps `BooleanOp` (about 8 nodes, boolean subtract) instead of the 40-node `TPL_Geometry_CreateMesh`. Descriptions on all four graphs rewritten in this form. Plan: more graphs per plugin, one per use case (e.g. GeometryScript Simplify / CombineActors; Primitives Place_RadialScatter / Filter; MeshTerrain Write), not one graph per plugin.
- Samples the toolset cannot build yet (Mesh Partition nodes, GeometryScript nodes, PythonInterop, BiomeCore, surface data readback) are parked in [PCG sample graphs the toolset cannot build yet](backlog-pcg-sample-unbuildable-2026-11-12.md).
- Gap: Mesh Partition nodes (`Mesh Partition PCG Query/Write/...`) are not in `ListNativeNodes`, so `AddNode` cannot add them; use plugin primitives (`Get_MeshTerrain`, `Write_MeshTerrain`) or example graphs as subgraphs instead.
- Shared level `/Game/Samples/PCGSmokeTest/L_Smoke` now exists and is saved (one volume per graph; `Vol_Core` at origin, `Vol_Primitives` at Y=6000). No Mesh Partition, Biome Core, GeometryScript or Python graphs built yet; Biome Core / Mesh Partition need volumes and a MeshPartition actor in that level, and Biome Core skill says duplicate from `/PCGBiomeSample/...`, never edit originals.
- Req 4 update (2026-11-12, later): 9 graphs built. New: `PCG_Smoke_Primitives_RadialScatter` (4 rings, 40 points), `_RandomFilter` (55 of 100 points), `_WriteNoise` (100 points with a `NoiseValue` attribute), `PCG_Smoke_GeometryScript_Simplify` (wraps the plugin's Simplify sample; executes with no messages, not yet captured), and `PCG_Smoke_MeshTerrain_Write` (Get -> Extract -> Transform Points +300 Z -> Write; own actor `MeshTerrain_Write_Smoke` at Y=48000; **does not work yet**, see below). Volumes sit at Y = 0, 6000, ..., 48000 in `L_Smoke`.
- **Pitfall found:** `Filter_RandomChoice` keeps every point when `randomPerDataset` is true and there is one dataset. Set it to false to thin points. Written into the RandomFilter graph description.
- **Mesh Terrain blocker:** both MeshPartition actors have `megaMeshComponent = None`, so no terrain exists. `Get_MeshTerrain` returns no data, and `PCG_Smoke_MeshTerrain_Write` warns "No Valid Input Data Found". The earlier "weak proof" for `PCG_Smoke_MeshTerrain` is therefore not a proof at all: it reads an empty actor. Terrain has to be created first (MeshTerrainMode tools or a Write from a heightmap). Parked in the backlog ticket, item 1; the fix is a new toolset and skill, ticketed as [Project MeshTerrain toolset and skill](backlog-meshterrain-toolset-2026-11-12.md) (user, 2026-11-12: "this sounds like an example of a new toolset/skill we will need to build"). Also: `PCGToolset.PCGSpatialToolset` exists and has not been read yet.
- Not buildable as self-contained samples: GeometryScript `CombineActors` needs world actors tagged `MyTag`; `Simplify` was fine because it builds its own cube.
- Req 7, 8: deferred to a follow-up card (user decision, 2026-11-12). Req 6: done, commits `aeed93a` (plugins, samples, `L_Smoke`, docs) and `52c8da1` (Sample level files). Not pushed. `Content/PCG/PCG_MCPSmokeTest.uasset` left untracked on purpose.

### Final status (2026-11-12)
| Req | Result |
|---|---|
| 1 plugin list | Done. |
| 2 enabled | `.uproject` done; Editor > Plugins UI check not done (no MCP route), left for the user. |
| 3 loads clean | Done. |
| 4 samples | 9 graphs, 7 proven (Core, Primitives, RadialScatter, RandomFilter, WriteNoise, GeometryScript BooleanOp, GeometryScript Simplify). 2 unproven (MeshTerrain, MeshTerrain_Write): no terrain exists. Not built: PythonInterop, BiomeCore, GeometryScript CombineActors (see backlog). Evidence is JPEG captures for the two GeometryScript graphs only; the rest are proven by point counts and data views. |
| 5 MCP | All 9 graphs listed, described and structure-read over `PCGToolset`. |
| 6 committed | Done, not pushed. |
| 7, 8 | Deferred. |

## Autopsy (process and outcome)

**Outcome.** Req 4's intent, "one proven sample per required plugin", was only partly met: seven small graphs work and cover core PCG, PCGPrimitives, GeometryScripting and PCGGeometryScriptInterop. The Mesh Partition plugins, PCGBiomeCore and PCGPythonInterop have no proven sample. The design principle the user set (many tiny single-use-case samples with `USE WHEN ... SHOWS ...` descriptions) works well and is the main reusable result.

**What went well.**
- Wrapping a plugin primitive or sample with `AddSubgraphNode` made each sample 2 to 4 nodes, and it accepts graphs outside `ListAvailableSubgraphs`.
- `ProgrammaticToolset.execute_tool_script` batched 100+ calls (strip the level, build and execute graphs).
- Checking each graph's data (counts, attribute values) caught real problems: the Primitives description said 121 points (it was 100) and `Filter_RandomChoice` returned all points.

**What went wrong, and what to do differently.**
1. **Wrong "can't be done" claim (levels).** I told the user level creation needed them, after reading only `SceneTools`. It worked through `AssetTools.move` plus `save_assets`. Fix: `describe_toolset` every plausible toolset before saying a step needs the user. Already filed on the claude-rules card.
2. **Trusted a new level to be empty.** `L_Smoke` arrived as a copy of the Sample level (132 stray actors). Fix: list a new level's actors before using it.
3. **Weak proof accepted too long.** `PCG_Smoke_MeshTerrain` "executed with no messages" and returned `{"data_version":0}`, and I recorded it as a weak pass. It was reading an empty actor. A single `get_properties` on the actor (`megaMeshComponent: None`) would have shown this much earlier. Fix: when a read returns nothing, check the source object before building on it. Do not count "no errors" as a pass.
4. **Built the Write sample before proving the Read.** Costs two builds, one warning. Fix: prove each stage of a chain before adding the next.
5. **Sizeable context cost from tool output.** `describe_toolset` for large toolsets and 1.9 MB viewport captures (a hook blocks reading them). Fix: decode captures to a small JPEG straight from the saved file, and describe only the toolset needed. Costs were acceptable but the session ran long (about 2.6 hours) and was compacted once.
6. **Scope was unclear at the start.** The card said "one graph per plugin"; the user's real intent was "many simple examples across use cases". It took two statements from the user to land it. Fix: when a card's wording and the stated purpose (reference examples for Claude) differ, ask which wins before building.

**Recommendations to carry forward.**
- Keep the sample set growing as small use-case graphs; revisit via the backlog tickets.
- Mesh Terrain creation needs a new toolset and skill: [Project MeshTerrain toolset and skill](backlog-meshterrain-toolset-2026-11-12.md).
- Add the toolset-check and "verify the source object" rules to `pgen-unreal` (decided on the claude-rules card).
- A hypothesis for this card, stated up front, would have been: "can Claude build and verify PCG graphs over MCP alone?" Answer: yes for points, filters, attributes and GeometryScript samples; no for creating Mesh Terrain, BiomeCore and Python nodes.
