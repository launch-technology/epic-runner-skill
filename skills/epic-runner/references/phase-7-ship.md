# Phase 7: Ship

Your job is to push the completed story branch, provide a PR description, and advance
to the next story. All commits have already been made in earlier phases — this phase
handles delivery only.

## Prerequisites

Verify that:

- The current phase in the state file is `"shipping"`.
- The story branch has at least one commit (the implementation commit from Phase 5).

## Step 1: Verify Branch

Run `git status` and `git branch --show-current`. Present the output:

```
Current branch: [branch name]
State file branch: [branch from state file]
Clean working tree: [yes/no]
```

If the branch name doesn't match the state file, stop and flag it. Ask the user how
to proceed before touching anything else.

If there are uncommitted changes, stop and ask the user what to do — these are unexpected
at this point since all commits should have been made in Phases 3, 5, and 6.

## Step 2: Push

Ask: "Ready to push `[branch-name]` to origin?"

Wait for confirmation. Then run `git push -u origin [branch-name]`. Show the output.

## Step 3: PR Description

Do not create a PR automatically. Print a ready-to-use PR description the user can
paste into GitHub or use with `gh pr create`:

```
Title: feat(epic-N/story-N): [brief description of what was delivered]

Body:
## Summary
[Story recap — what was delivered, which flows are affected]

## Acceptance Criteria
- [ ] [each criterion from the story doc]

## Test Results
[Build status, verification steps that passed]

## Commits
- [commit 1 message — docs/planning]
- [commit 2 message — implementation]
- [commit 3 message — code review feedback (if applicable)]
```

The PR should target the epic branch (stored in the state file's `epic_branch` field),
not the base branch.

Tell the user: "Branch is pushed. Here's a PR description you can use — just run
`gh pr create --base [epic-branch]` or open a PR on GitHub targeting `[epic-branch]`."

## Step 4: Advance to Next Story

After pushing:

1. Update the state file:
   - Set current story's `status` to `"completed"` with `completed_at` timestamp.
   - Find the next story with status `"pending"`.
   - Set `current_story` to the next story's number.
   - Set `current_phase` to `"grooming"`.
   - Update `updated_at`.

2. Present the status summary:

   ```
   Story N: [Title] — completed
   Stories: [completed/total]

   Next up: Story M — [Title]
   ```

3. Ask: "Before we move on — anything from this story worth capturing? For example:
   a design decision that turned out wrong, something that took longer than expected,
   or a convention we should carry into the next story. If yes, I'll update the epic's
   Resolved Design Decisions table."

   If the user shares something, update `.epics/Epic-N/epic-overview.md` to add or
   amend the relevant row in the Resolved Design Decisions table before advancing.

4. Ask: "Ready to groom Story M? Run `/epic-runner groom M` when you're ready."

### If All Stories Are Complete

If there are no more pending stories:

1. Update the state file:
   - Set `current_phase` to `"completed"`.
   - Update `updated_at`.

2. Present:

   ```
   All stories in Epic N are complete!

   Epic: [N] - [Title]
   Stories completed: [total]

   Summary of what was delivered:
   - Story 1: [Title] — [one-line summary]
   - Story 2: [Title] — [one-line summary]
   ...
   ```

3. Tell the user: "The epic branch `[epic-branch]` is ready to merge into `[base-branch]`.
   You can create a PR from `[epic-branch]` -> `[base-branch]` when you're ready."

4. Ask: "Anything from this epic worth capturing before we close it out? For example:
   decisions that turned out wrong, patterns to carry forward, or things to do
   differently next time."

   If the user shares something, update the epic overview's Resolved Design Decisions
   table accordingly.

5. Congratulate the user and ask if they want to start a new epic.
