# epic-runner

A Claude Code skill that orchestrates an end-to-end scrum development workflow. Work is organized into epics broken down into stories, then walked through planning, building, testing, reviewing, and delivering — one story at a time — until the epic is complete.

## How It Works

The skill runs a seven-phase pipeline with an approval gate between every phase. You stay in control at each step; nothing advances until you say so.

```
Phase 1: Epic Creation       -> Define the epic and outline stories
Phase 2: Story Grooming      -> Detail one story at a time
Phase 3: Implementation Plan -> Generate a structured execution plan
Phase 4: Code Implementation -> Build the story
Phase 5: Testing             -> Manual verification and fixes
Phase 6: Code Review         -> Senior-dev-style code review
Phase 7: Ship                -> Push, PR description, and advance
```

After shipping a story, the workflow loops back to Phase 2 for the next one. When all stories are done, the epic is complete.

## Installation

Install from the Claude Code plugin marketplace:

```bash
claude plugin install epic-runner
```

Or use a local directory during development:

```bash
claude --plugin-dir /path/to/epic-runner-skill
```

## Usage

Invoke the skill with `/epic-runner` followed by a command:

| Command              | Description                                    |
| -------------------- | ---------------------------------------------- |
| `new-epic <topic>`   | Start a new epic around the given topic        |
| `resume`             | Pick up where you left off on the active epic  |
| `resume epic-N`      | Resume a specific epic by number               |
| `groom <N>`          | Groom story N for the current epic             |
| `plan`               | Generate an implementation plan for the story  |
| `implement`          | Start coding the current story                 |
| `test`               | Run through testing and fixes                  |
| `code-review`        | Code review of the implementation              |
| `ship`               | Push and advance to the next story             |
| `status`             | Show progress summary for the active epic      |

Running `/epic-runner` with no arguments checks for an active epic and shows its status.

### Example Workflow

```
/epic-runner new-epic user authentication and authorization
  -> Claude asks clarifying questions, produces an epic overview with story outlines
  -> You approve (or request changes)
  -> Epic branch created from your base branch

/epic-runner groom 1
  -> Story branch created from epic branch
  -> Claude reads project context, analyzes code, asks targeted questions
  -> Produces a story document; you approve

/epic-runner plan
  -> Generates a structured implementation plan JSON; you approve
  -> Planning artifacts committed to the story branch

/epic-runner implement
  -> Claude writes code following the plan; you confirm when ready to test

/epic-runner test
  -> Walk through verification steps; confirm all checks pass
  -> Implementation committed to the story branch

/epic-runner code-review
  -> Diff-based code review against the epic branch
  -> Findings categorized as must-fix, should-fix, or notes
  -> Review fixes committed (if any)

/epic-runner ship
  -> Push branch, get PR description, advance to the next story
```

## Branching Model

Epic-runner uses a two-level branching strategy:

```
base branch (main, develop, etc.)
  |-- epic/epic-1-user-auth
        |-- feature/epic-1-story-1-login-pages
        |-- feature/epic-1-story-2-password-reset
        |-- feature/epic-1-story-3-oauth
```

- The **base branch** is configurable per repository (stored in `.epics/config.json`)
- **Epic branches** are created from the base branch during Phase 1
- **Story branches** are created from the epic branch during Phase 2
- Story PRs target the epic branch; the epic branch merges into the base branch when the epic is complete

## Commit Strategy

Commits happen at natural checkpoints, not all at the end:

| When | Commit message | Purpose |
|------|---------------|---------|
| After planning (Phase 3) | `docs(epic-N/story-N): story document and implementation plan` | Preserve planning artifacts |
| After testing (Phase 5) | `feat(epic-N/story-N): [description]` | Implementation checkpoint |
| After code review (Phase 6) | `fix(epic-N/story-N): address code review feedback` | Only if review produced changes |

## Configuration

On first use, epic-runner creates `.epics/config.json` to store repository-level settings:

```json
{
  "base_branch": "develop"
}
```

You'll be asked for your base branch during the first epic creation. The config is reused for all subsequent epics in the same repository.

## State Tracking

Each epic maintains a `.scrum-state.json` file in its directory (`.epics/Epic-N/.scrum-state.json`) that tracks:

- Current story and phase
- Per-story status (pending, in-progress, completed)
- File references for story documents and implementation plans
- Git branch names (epic branch and story branches)
- Timestamps and notes for resuming across sessions

This state file is the source of truth. The skill reads it on every invocation so you can stop and resume at any time — even across different Claude Code sessions.

## Project Structure

```
epic-runner-skill/
+-- .claude-plugin/
|   +-- plugin.json              # Plugin manifest
|   +-- marketplace.json         # Marketplace listing
+-- skills/
|   +-- epic-runner/
|       +-- SKILL.md             # Skill definition and routing
|       +-- references/          # Phase-specific instructions
|           +-- phase-1-epic-creation.md
|           +-- phase-2-story-grooming.md
|           +-- phase-3-implementation-plan.md
|           +-- phase-4-code-implementation.md
|           +-- phase-5-testing.md
|           +-- phase-6-code-review.md
|           +-- phase-7-ship.md
|           +-- state-schema.md
+-- CHANGELOG.md
+-- LICENSE
+-- README.md
```

## Key Design Principles

- **Approval gates everywhere.** The skill never auto-advances between phases. You review and approve each output before moving on.
- **One story at a time.** Stories are groomed, planned, built, tested, reviewed, and shipped individually to keep scope manageable.
- **Resumable.** State is persisted to disk. Stop mid-phase, close your terminal, come back tomorrow — `/epic-runner resume` picks up where you left off.
- **Product-first story documents.** Story grooming produces documents written for a product owner, not an engineer. Technical decisions are deferred to the implementation plan phase.
- **Context-aware.** Each phase reads your existing codebase, previous stories, and project documentation to avoid asking questions the code already answers.
- **Safe commits.** Implementation is committed before code review begins, so review fixes can never corrupt the working implementation.
- **Project-agnostic.** No framework or structure assumptions are hardcoded. Base branch and project layout are configurable per repository.

## License

MIT
