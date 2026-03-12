# epic-runner

A Claude Code skill that orchestrates an end-to-end scrum development workflow. Work is organized into epics broken down into stories, then walked through planning, building, testing, and delivering — one story at a time — until the epic is complete.

## How It Works

The skill runs a six-phase pipeline with an approval gate between every phase. You stay in control at each step; nothing advances until you say so.

```
Phase 1: Epic Creation       → Define the epic and outline stories
Phase 2: Story Grooming      → Detail one story at a time
Phase 3: Implementation Plan → Generate a structured execution plan
Phase 4: Code Implementation → Build the story
Phase 5: Testing             → Manual verification and fixes
Phase 6: Review & Ship       → Final review, push, and advance
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
| `review`             | Final review and ship                          |
| `status`             | Show progress summary for the active epic      |

Running `/epic-runner` with no arguments checks for an active epic and shows its status.

### Example Workflow

```
/epic-runner new-epic user authentication and authorization
  → Claude asks clarifying questions, produces an epic overview with story outlines
  → You approve (or request changes)

/epic-runner groom 1
  → Claude reads project context, analyzes code, asks targeted questions
  → Produces a story document; you approve

/epic-runner plan
  → Generates a structured implementation plan JSON; you approve

/epic-runner implement
  → Claude writes code following the plan; you confirm when ready to test

/epic-runner test
  → Walk through verification steps; confirm all checks pass

/epic-runner review
  → Final review, then push and advance to the next story
```

## State Tracking

Each epic maintains a `.scrum-state.json` file in its directory (`.epics/Epic-N/.scrum-state.json`) that tracks:

- Current story and phase
- Per-story status (pending, in-progress, completed)
- File references for story documents and implementation plans
- Git branch names
- Timestamps and notes for resuming across sessions

This state file is the source of truth. The skill reads it on every invocation so you can stop and resume at any time — even across different Claude Code sessions.

## Project Structure

```
epic-runner-skill/
├── .claude-plugin/
│   └── plugin.json              # Plugin manifest
├── skills/
│   └── epic-runner/
│       ├── SKILL.md             # Skill definition and routing
│       └── references/          # Phase-specific instructions
│           ├── phase-1-epic-creation.md
│           ├── phase-2-story-grooming.md
│           ├── phase-3-implementation-plan.md
│           ├── phase-4-code-implementation.md
│           ├── phase-5-testing.md
│           ├── phase-6-review-and-ship.md
│           └── state-schema.md
├── CHANGELOG.md
├── LICENSE
└── README.md
```

## Key Design Principles

- **Approval gates everywhere.** The skill never auto-advances between phases. You review and approve each output before moving on.
- **One story at a time.** Stories are groomed, planned, built, tested, and shipped individually to keep scope manageable.
- **Resumable.** State is persisted to disk. Stop mid-phase, close your terminal, come back tomorrow — `/epic-runner resume` picks up where you left off.
- **Product-first story documents.** Story grooming produces documents written for a product owner, not an engineer. Technical decisions are deferred to the implementation plan phase.
- **Context-aware.** Each phase reads your existing codebase, previous stories, and project documentation to avoid asking questions the code already answers.

## License

MIT
