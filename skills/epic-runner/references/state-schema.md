# State File Schema

Location: `prds/Epic-N/.scrum-state.json`

This file tracks workflow progress for an epic. It is the source of truth for resuming
work across sessions. Update it at every phase transition.

## Example

```json
{
  "epic_number": 3,
  "epic_title": "Navigation Architecture — Three Workspaces",
  "epic_dir": "prds/Epic-3",
  "created_at": "2026-03-06T10:00:00Z",
  "updated_at": "2026-03-06T14:30:00Z",
  "current_story": 2,
  "current_phase": "grooming",
  "stories": [
    {
      "number": 1,
      "title": "FG Workspace Header & Sidebar",
      "status": "completed",
      "story_file": "story-1-fg-workspace-header-and-sidebar.md",
      "plan_file": "story-1-fg-workspace-header-and-sidebar_implementation_plan.json",
      "branch": "feature/epic-3-story-1-fg-header-sidebar",
      "completed_at": "2026-03-06T12:00:00Z"
    },
    {
      "number": 2,
      "title": "Org Admin Workspace Chrome",
      "status": "in-progress",
      "story_file": "STORY-2-org-admin-workspace-chrome.md",
      "plan_file": null,
      "branch": null,
      "completed_at": null
    }
  ],
  "notes": ""
}
```

## Fields

| Field           | Type   | Description                                                                      |
| --------------- | ------ | -------------------------------------------------------------------------------- |
| `epic_number`   | number | Matches the directory name (`Epic-N`)                                            |
| `epic_title`    | string | Human-readable title from the epic overview                                      |
| `epic_dir`      | string | Relative path to epic directory (e.g., `prds/Epic-3`)                            |
| `created_at`    | string | ISO 8601 timestamp — when the epic was created                                   |
| `updated_at`    | string | ISO 8601 timestamp — updated on every state change                               |
| `current_story` | number | Story number currently being worked on                                           |
| `current_phase` | string | Current workflow phase (see below)                                               |
| `stories`       | array  | Array of story tracking objects (see below)                                      |
| `notes`         | string | Free-text context for resuming (e.g., "Fixed sidebar bug during implementation") |

## Phase Values

| Value           | Meaning                                       |
| --------------- | --------------------------------------------- |
| `epic-creation` | Epic overview being drafted, not yet approved |
| `grooming`      | Story document being created or revised       |
| `planning`      | Implementation plan being generated           |
| `implementing`  | Code being written                            |
| `testing`       | Manual testing in progress                    |
| `review`        | Final review before ship                      |
| `completed`     | All stories done                              |

## Story Object

| Field          | Type           | Description                                           |
| -------------- | -------------- | ----------------------------------------------------- |
| `number`       | number         | Story number within the epic                          |
| `title`        | string         | Brief story title                                     |
| `status`       | string         | `"pending"`, `"in-progress"`, or `"completed"`        |
| `story_file`   | string or null | Filename of the story document (relative to epic dir) |
| `plan_file`    | string or null | Filename of the implementation plan JSON              |
| `branch`       | string or null | Git feature branch name                               |
| `completed_at` | string or null | ISO 8601 timestamp                                    |

## Rules

- Always update `updated_at` when writing the state file.
- Only one story should have status `"in-progress"` at a time.
- When advancing to the next story, set the previous story to `"completed"` first.
- Story files are relative to the epic directory, not the workspace root.
- The `notes` field is for recording context about interruptions or mid-phase work.
