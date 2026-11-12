---
id: "m01-project-setup-2026-11-11--claude-rules"
status: "in-progress"
priority: "medium"
assignee: null
epic: "m01-project-setup-2026-11-11"
dueDate: null
created: "2026-11-11T00:00:00.000Z"
modified: "2026-11-12T05:36:55.841Z"
completedAt: null
labels: ["m01", "wayfinder:grilling"]
order: "a1V"
---
# Develop CLAUDE rules for project

Blocked by: [Investigate Unreal skills/hooks](m01-project-setup-2026-11-11--skills-hooks-research.md), [Integrate Unreal MCP with Claude](m01-project-setup-2026-11-11--unreal-mcp-claude.md)

Research how Epic and the community write agent rule files for Unreal, then produce a repo-root `CLAUDE.md` (Claude only, no `AGENTS.md`) holding only project and Unreal rules, trimmed so an AI agent interprets it reliably. Global `~/.claude` rules are not restated. It also covers how the author (a visual learner) works best with Claude: when to use diagrams, trees and charts, and what a simple explanation that carries its own context looks like, as a project rule plus a proposed global addition.

## Test & pass requirements

1. **Research saved.** Check: `Resources/Docs/Research/m01-project-setup-2026-11-11--claude-rules.md` exists and is linked from this card. Expected: it cites Epic's guidance on agent rule files (Agent.md or equivalent) and community/open-source recommendations, and states for each source whether it was adopted or rejected, and why.
2. **Existing rules mined.** Check: the research file lists what was kept and dropped from `~/.claude/*.md` and the `CLAUDE.md`/`AGENTS.md` files under `D:/prototyping`. Expected: only engine-agnostic rules are ported; Unity-specific rules are listed as dropped.
3. **Coverage decided by research.** Check: a coverage map in the research file from topic to rule, covering the brief's AI tenets plus the Unreal topics the research found. Expected: every brief tenet is either mapped to a rule or marked as already covered by the global file; no topic is added without a cited source.
4. **File exists, project-only.** Check: `CLAUDE.md` at repo root, committed. Expected: no rule restates a global `~/.claude/CLAUDE.md` rule, and none contradicts one (checked by line-by-line comparison).
5. **Length budget.** Check: line count. Expected: at or under the size the research cites, and never over 150 lines; detail beyond that lives in linked docs.
6. **References exist.** Check: every skill, hook, MCP tool, file or command the rules name. Expected: each exists in the repo or toolchain (includes outputs of the skills/hooks and MCP cards).
7. **Behaviour probes pass.** Check: in a fresh session in this repo, run one prompt per rule that should trigger it (for example an ambiguous task, an asset edit, two agents contending for the editor). Expected: Claude follows the rule unprompted; results recorded in `## Build notes`.
8. **Ablation per rule.** Check: for each rule, remove it, rerun its probe from requirement 7. Expected: behaviour gets worse without it. Rules where behaviour is unchanged are cut as already default.
9. **Visual-learner research.** Check: the research file has a section on working with Claude as a visual learner. Expected: it covers how to prompt for diagrams, trees and charts and which formats render in your tools (Mermaid in VS Code, terminal ASCII, artifacts), and includes Unreal-specific visuals (PCG graphs, node flows, terrain pipeline).
10. **Visual trigger rules.** Check: `CLAUDE.md` states when Claude must use a visual instead of prose (plans, architecture, data flow, comparisons). Expected: each trigger is concrete enough to be tested by a probe.
11. **Plain-explanation standard.** Check: `CLAUDE.md` defines a simple explanation with context (terms defined on first use, why not just what, `GLOSSARY.md` vocabulary). Expected: a probe explaining a PCG concept stands alone without prior Unreal knowledge.
12. **Global proposal.** Check: a drafted addition for `~/.claude/CLAUDE.md` is included in the research file or Build notes. Expected: it is a proposal only; Claude does not edit global files, and you approve or reject it.
13. **Visual probes judged by you.** Check: fresh-session prompts (explain a PCG concept, plan a task), each rule ablated as in requirement 8. Expected: you confirm the output used a useful visual and the explanation stood alone; look and feel is your call, not Claude's.
14. **Readability.** Check: you read the final file. Expected: you can follow it without C++ or Unreal knowledge, and confirm it matches your intent.

## Re-check against Plan.md

From `Resources/Docs/Plan.md`: candidate project rules to consider in the coverage map (still need a cited source per requirement 3):
- Terrain randomness comes only from the Seed; scatter may use a separate random source.
- PCGPythonInterop and other editor-only tooling is never used for launch-time generation.
- Real-data rule: each generation rule cites a free or open-source source (Scope.md).

## Observed failure to address (2026-11-12, build-plan session)

- **What happened:** Claude read `Resources/ProjectBrief.md` with `head -60`, which cut off at line 60. The MILESTONES section starts at line 66, so M02 and M03 were never seen. Claude then said it had "looked at the brief", overstating a truncated read. It also read only the M01 cards it was editing and skipped the others. As a result it asked the user questions the brief already answered, and wrote `Plan.md` without M02/M03 in view.
- **User's words:** "this is a critical failure to assess all information available"; "referring to existing information is critical".
- **Rule to consider (needs a cited source, per requirement 3):** before planning or writing a doc, read the project's source-of-truth docs (`ProjectBrief.md`, `Scope.md`, the milestone and sibling cards) in full, not a truncated slice. If a read is partial, say so rather than claiming it was read. Check what already exists before asking the user.
- **Probe for requirement 7/8:** ask for a plan that depends on a late section of `ProjectBrief.md`; expected: Claude reads the whole file and cites that section, without asking the user for it.

## Observed failure to address (2026-11-12, pcg-install session)

- **What happened:** Asked to build a saved level for PCG sample graphs, Claude said "no MCP tool creates or saves a level, so you must do it manually" after reading only `SceneTools`. It had not read `AssetTools` or `EditorAppToolset`. The user asked "why cant this be done over mcp?" and it was doable: `AssetTools.move` of the Untitled level to `/Game/Samples/PCGSmokeTest/L_Smoke`, then `AssetTools.save_assets`.
- **User's words:** "why cant this be done over mcp?"
- **Rule to consider (needs a cited source, per requirement 3):** before telling the user a step needs a manual action or is impossible over MCP, `describe_toolset` every toolset that could plausibly cover it (Asset, EditorApp, Object, Programmatic, not just the obvious one). Treat levels as assets (move, duplicate, save_assets). State which toolsets were checked when reporting a real gap. Candidate home: the `pgen-unreal` skill ("Editing goes through the unreal MCP") or `CLAUDE.md`; this card decides which.
- **Probe for requirement 7/8:** ask Claude to create and save a new level; expected: it uses AssetTools without asking the user to do it in the editor.
- Source: build notes in `m01-project-setup-2026-11-11--pcg-install.md` (autopsy note).