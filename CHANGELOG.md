# Changelog

## 1.2.0 — 2026-11-02

### Added

- **Phase 6: Code Review** — New phase between testing and shipping. Two-pass review: holistic (placement, completeness, missing code, DRY violations, plan adherence, AC traceability with correctness verification, boundary compliance) then per-file (dead code, naming consistency, duplication with existing codebase, error handling at boundaries, hardcoded values, bad precedents). Explicitly skips formatting/style issues that linters handle. DRY violations are weighted as high-priority must-fix items. Findings categorized as must-fix, should-fix, or notes. Creates a separate commit for review fixes (only if changes were made). Review criteria are defined in `phase-6-code-review.md`
- **Epic branches** — Phase 1 now creates an epic branch from the base branch (`epic/epic-N-title`). Story branches are created from the epic branch instead of directly from the base branch. Story PRs target the epic branch; epic branch merges into base when the epic is complete
- **Story branches created earlier** — Story branches are now created at the start of Phase 2 (grooming) instead of Phase 4 (implementation), so planning artifacts are tracked from the beginning
- **Commit after planning** — Phase 3 now commits the story document and implementation plan (`docs(epic-N/story-N): ...`) before implementation begins, preserving planning artifacts on the branch
- **Commit after testing** — Phase 5 now commits the implementation (`feat(epic-N/story-N): ...`) before code review begins, creating a safe checkpoint that can be returned to if review fixes go wrong
- **Repository configuration** — `.epics/config.json` stores repo-level settings (starting with `base_branch`). Created during the first epic; reused for all subsequent epics

### Changed

- **Phase numbering** — Old Phase 6 (Review & Ship) is now Phase 7 (Ship). The new Phase 6 is Code Review
- **Phase 7 (Ship) simplified** — Commit ceremony removed (commits now happen in Phases 3, 5, and 6). Phase 7 only handles: verify branch, push, PR description, and advance to next story
- **Base branch is configurable** — No longer hardcoded to `develop`. Users are asked for their base branch during first epic creation and the value is stored in `.epics/config.json`
- **Project-agnostic** — Removed all Launch77/Turborepo-specific references (hardcoded paths like `/docs/launch77/`, `apps/<app-name>/docs/`, `src/app/`, `src/modules/`, `src/components/`). Phase files now reference project documentation generically and read paths from config or epic overview context
- **Phase 4 (Implementation)** — No longer creates the feature branch (moved to Phase 2). Verifies it is on the correct story branch at the start instead
- **State schema** — Added `epic_branch` field to state file. Added `code-review` and `shipping` phase values. Removed `review` phase value
- **SKILL.md** — Updated routing table with `code-review` and `ship` entry points. Removed `review` entry point. Updated phase flow diagram with commit points and branching operations. Replaced Launch77 project context with generic config reference

## 1.1.1 — 2026-04-11

### Fixed

- **Phase 4**: Stash working directory changes before switching to `develop` for feature branch creation, then restore with `git stash pop` — prevents dirty tree errors from Phase 2/3 files written before the branch switch

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
