---
id: "m01-project-setup-2026-11-11"
status: "in-progress"
priority: "high"
assignee: null
epic: null
dueDate: null
created: "2026-11-11T00:00:00.000Z"
modified: "2026-11-11T06:00:00.000Z"
completedAt: null
labels: ["m01", "wayfinder:map"]
order: "a0"
---
# M01 - Project set up

Get the toolchain, rules and review process in place so later milestones can be built by AI agents safely. Source: Resources/ProjectBrief.md.

## Destination

All child cards (`m01-project-setup-2026-11-11--*`) are `verified`, and M02+ exist on the board as loose epic cards.

## Notes

**Board as map.** This epic card is the wayfinder map; child cards are its tickets. The Kanban extension drops unknown frontmatter keys on save, so wayfinder state lives in labels and the body.
- Type: label `wayfinder:<task|research|grilling|prototype>`.
- Claim: `assignee: "ryanKeable"` and status `in-progress`.
- Blocking: a `Blocked by:` line under the card's H1. A card is unblocked when every card it lists is `verified`.
- Status: `backlog` open/unclaimed, `in-progress` claimed, `ready-to-test` built and awaiting review, `verified` resolved, `failed` back to the builder, `closed` ruled out of scope.

**One card per session.** Claim it, grill its `## Test & pass requirements` (numbered: what to check, how to check it, expected result) before any build work, build, record `## Build notes`, move to `ready-to-test`.

**Review.** The user, or a fresh opus session that did not build the card, checks the requirements, appends `## Review` (evidence and verdict) and sets `verified` or `failed`. The builder never reviews its own card.

**Research cards** run as a sonnet subagent using the `research` skill; findings are saved to `Resources/Docs/Research/<card-slug>.md` and linked from the card.

**AI tenets (from the brief).** Confer on ambiguity and assumptions. Opus/Fable plan, lesser models execute. Output must be readable and explainable. Self-review on the assumption the work may be wrong or have drifted.

**Skills per session.** Grilling cards: `grilling` + `domain-modeling`. Research cards: `research`. Vocabulary: `Resources/Docs/GLOSSARY.md`. Every new milestone epic starts with a wayfinder map, as done here.

## Decisions so far

## Not yet specified

- Per-milestone development autopsy, including a review of the opus reviewer's context usage. Shape it once *Develop journalistic review process* is settled; it likely feeds the journal.

## Out of scope

- Building the environment itself (M02+ work).
