# Changelog

## 1.0.0 — 2026-03-12

### Added

- Six-phase scrum workflow: Epic Creation, Story Grooming, Implementation Planning, Code Implementation, Testing, and Review & Ship
- Stateful epic tracking via `.scrum-state.json` per epic directory
- Approval gates between every phase transition
- `resume` support with retroactive state inference from existing files
- Entry points: `new-epic`, `resume`, `groom`, `plan`, `implement`, `test`, `review`, `status`
- Reference docs for each phase under `references/`
- State schema documentation with example
