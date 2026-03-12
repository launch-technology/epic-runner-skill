# Phase 6: Review & Ship

Your job is to present a final summary of the completed work, handle any last change
requests, and then commit, push, and advance to the next story.

## Step 1: Final Summary

Present a comprehensive summary:

### Story Recap

One paragraph: what was delivered, which user flows were affected, what the user can
now do that they couldn't before.

### Files Changed

Categorized list:

- **New files**: path and brief description
- **Modified files**: path and summary of changes
- **Deleted files**: path and why (if any)

### Key Decisions

Any implementation decisions or judgment calls made during coding that the user should
know about. If none, say "No notable decisions — implementation followed the plan as written."

### Test Results

Summary from Phase 5: build status, test results, which verification steps passed.

## Step 2: User Review

Ask: "Please review the implementation. You can:

1. **Request changes** — describe what needs to change and I'll fix it
2. **Approve and ship** — I'll commit, push, and we can move to the next story"

### If Changes Requested

1. Apply the requested changes.
2. Re-run affected checks from Phase 5 (typecheck, lint, build at minimum).
3. Present the changes and ask for review again.
4. Repeat until approved.

### If Approved — Commit and Push

1. **Stage files** — add specific files by name (not `git add -A` or `git add .`).
   Review the list of changed files and stage only the ones relevant to this story.
   Do not stage unrelated changes.

2. **Commit** with a descriptive message following the project's style:

   ```
   feat(epic-N/story-N): brief description of what was delivered

   Co-Authored-By: Claude Opus 4.6 <noreply@anthropic.com>
   ```

3. **Push** the feature branch:

   ```
   git push -u origin feature/epic-N-story-N-kebab-title
   ```

4. **Ask about PR**: "Want me to create a pull request?"
   - If yes: create a PR using `gh pr create` with:
     - Title: the commit message subject line
     - Body: story recap + acceptance criteria checklist + test results summary
     - Base branch: the main branch (usually `main`)
   - If no: just confirm the branch was pushed.

## Step 3: Advance to Next Story

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

3. Ask: "Ready to groom Story M? Run `/epic-runner groom M` when you're ready."

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

3. Congratulate the user and ask if they want to start a new epic.
