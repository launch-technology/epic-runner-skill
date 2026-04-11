# Changelog

## 1.1.0 — 2026-04-10

### Changed

- **Phase 1**: Story outline rules now require vertical slices — stories that touch only one technical layer (e.g. DB migration, helper functions) are explicitly invalid and must be merged into the story that surfaces their behavior to users
- **Phase 1**: Story size heuristic replaced — "more than one focused coding session" replaces the vague 3–4 user flow count
- **Phase 2**: Pre-grooming analysis now explicitly rejects single-layer stories and requires a QA tester to be able to exercise a real user flow before the story is valid
- **Phase 2**: `Complexity` field removed from story template; replaced with `Deployment Safety` — a yes/no question about whether a feature flag is needed before all stories are complete
- **Phase 2**: `Deliverability` section tightened to focus on whether the app is in a coherent state after merge, separate from the flag question
- **Phase 2**: User story statement added to story template — `> As a [user type], I want [goal] so that [reason].` appears as a blockquote immediately after the title; inability to write it cleanly is treated as a blocker
- **Phase 3**: AC coverage check added before approval gate — every acceptance criterion must map to at least one plan entry; gaps are fixed before the plan is presented
- **Phase 4**: `develop` branch is now a hard requirement — workflow stops if `origin/develop` does not exist and offers guided creation with step-by-step confirmation before proceeding
- **Phase 5**: Regression smoke test step added — derives adjacent flows to verify from "Areas This Story Changes"; gates the "previously working flows" DoD checkbox on user confirmation
- **Phase 6**: Commit/push broken into 6 explicit confirmation steps (verify branch → review staged files → confirm message → commit → push → PR description); user must confirm each step before proceeding
- **Phase 6**: PR creation removed — skill now prints a ready-to-paste PR title and body instead of running `gh pr create`
- **Phase 6**: Retrospective prompt added after each story completion and after epic completion; learnings are written back to the epic's Resolved Design Decisions table

## 1.0.0 — 2026-03-12

### Added

- Six-phase scrum workflow: Epic Creation, Story Grooming, Implementation Planning, Code Implementation, Testing, and Review & Ship
- Stateful epic tracking via `.scrum-state.json` per epic directory
- Approval gates between every phase transition
- `resume` support with retroactive state inference from existing files
- Entry points: `new-epic`, `resume`, `groom`, `plan`, `implement`, `test`, `review`, `status`
- Reference docs for each phase under `references/`
- State schema documentation with example
